# Pacote 16 — bug: acentos quebrados no Linux

**Tamanho:** S · **Depende de:** [Pacote 10](10-polimento.md)

## Objetivo

No macOS os acentos renderizam certo. No Linux (relatado rodando dentro de um terminal PowerShell —
provavelmente WSL), nomes com acento saem como lixo tipo `DetenM-CM-'M-CM-#o` em vez de `Detenção`.

Hipótese (ver comentário já existente em
[ui.c:19-31](../../aventureiro/src/ui.c#L19-L31)): `setlocale(LC_ALL, "")` em `ui_iniciar()` depende
da locale do **ambiente** ter uma variante `.UTF-8` disponível (`LANG`/`LC_ALL`). No macOS isso quase
sempre vem configurado por padrão no terminal; em muitas instalações Linux mínimas/WSL/Docker, a
locale UTF-8 não está gerada ou as variáveis não estão exportadas nessa sessão — `setlocale` cai
silenciosamente pra `"C"`/`"POSIX"`, e aí volta o problema que o comentário já descreve (ncurses conta
cada byte de um caractere multi-byte como uma coluna, e o terminal mostra os bytes crus em notação
`M-x`).

## Entregáveis

- **Confirmar a hipótese**: rodar `locale` e `locale -a` no ambiente Linux que reproduz o bug; ver se
  existe alguma `*.UTF-8` na lista e se `LANG`/`LC_ALL` estão setados.
- **Fallback em `ui_iniciar()`** (`ui.c`): se `setlocale(LC_ALL, "")` não resultar numa locale UTF-8
  (checar o retorno / usar `nl_langinfo(CODESET)` pra confirmar `"UTF-8"`), tentar explicitamente
  uma locale UTF-8 conhecida (ex. `"C.UTF-8"`, disponível em quase toda distro Linux moderna mesmo
  sem locale extra gerada) antes de desistir.
- Se mesmo com fallback a distro não tiver nenhuma UTF-8 instalada, decidir se vale um aviso claro no
  início do jogo (em vez de tela quebrada silenciosa) e/ou nota no
  `aventureiro/scripts/install-deps.sh` / README pedindo pra gerar uma locale UTF-8 no Linux.

## Critério de aceite

No mesmo ambiente Linux onde o bug foi reproduzido (screenshot: sala mostrando
"Cela de DetenM-CM-'M-CM-#o" em vez de "Cela de Detenção"), rodar o binário mostra os acentos
corretos, sem depender do usuário já ter `LANG`/`LC_ALL` UTF-8 setados na shell.

**Implementado, verificação parcial.** Adicionada `garantir_locale_utf8()` em `ui.c` (chama
`setlocale(LC_ALL, "")`, confere `nl_langinfo(CODESET)` e cai pra `setlocale(LC_ALL, "C.UTF-8")` se
não for UTF-8). Build limpo (`-Wall -Wextra -Werror`) e `tests/smoke_test.py` sem crash.

O que **não** deu pra confirmar nesta sessão: reproduzir o bug em si. Rodando o binário no macOS com
`LANG=C LC_ALL=C` forçado (simulando o ambiente Linux quebrado) os acentos já saíam certos **mesmo
com o código antigo** (testado via `git stash` comparando antes/depois) — ou seja, o macOS não
reproduz esse bug de jeito nenhum, provavelmente por diferença de libc/ncurses entre BSD e Linux.
Não dá pra provar que o fallback resolve o caso real sem testar num Linux/WSL de verdade. Próximo
passo natural: confirmar no teste do Termux que já estava planejado (ver
[[project-aventureiro]]) ou em qualquer WSL disponível.
