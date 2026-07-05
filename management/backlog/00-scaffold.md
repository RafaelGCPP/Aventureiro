# Pacote 0 — Decisões travadas + esqueleto do projeto

**Tamanho:** S · **Depende de:** — · Ver [handover_aventureiro_c.md](../../handover_aventureiro_c.md)

## Objetivo

Eliminar as perguntas em aberto da seção 7 do handover com respostas padrão (documentadas, revisáveis
depois), e criar a estrutura de diretórios/arquivos vazios para que todo pacote seguinte tenha um
lugar certo para escrever código.

## Decisões adotadas por padrão

(Mudar depois é só editar `config.json`, não código.)

- Grid padrão: 8x8 (igual ao original).
- Fórmulas de dano/cura/loot: valores razoáveis livres, sem tentar recuperar constantes ofuscadas do BASIC.
- Fuga/perseguição de inimigos: versão simplificada (sala vizinha aleatória, sem perseguição multi-sala).
- Mapa visual: sem ASCII map por enquanto — 100% textual, como o original. (Pode virar um pacote extra depois.)
- Save/load: não implementar nesta fase — uma partida = uma sessão contínua, como o original.

## Entregáveis

- Estrutura de diretórios `aventureiro/{data,src}/`.
- `Makefile` mínimo (ainda sem `.c` para compilar — só o alvo vazio).
- `cJSON.h`/`cJSON.c` vendorizados (baixar a versão single-file oficial do repo DaveGamble/cJSON).
- `.gitignore` para binários/objetos.
- README do subprojeto com as decisões acima.

## Critério de aceite

`make` roda sem erro (mesmo que só compile o cJSON vendorizado como teste). Árvore de diretórios
bate com a seção 6 do handover.
