# Pacote 18 — setas do teclado como atalho de movimento

**Tamanho:** S · **Depende de:** [Pacote 6a](06a-combat-basico.md), [Pacote 7](07-ui.md)

## Objetivo

Hoje mover exige dois passos: apertar `0`, depois a letra da direção (`N/S/L/O`) entre as saídas
disponíveis (`comando_mover_interativo` em
[game.c:72-91](../../aventureiro/src/game.c#L72-L91), via `ler_opcao(teclas)`). Sugestão do usuário:
as setas do teclado funcionarem como atalho direto — seta ativa o comando `0` + a direção
correspondente num único toque, sem passar pelo prompt.

## Entregáveis

- `keypad()` já está ligado em `janela_log` e `stdscr` ([ui.c:36](../../aventureiro/src/ui.c#L36) e
  [ui.c:42](../../aventureiro/src/ui.c#L42)), então `KEY_UP/KEY_DOWN/KEY_LEFT/KEY_RIGHT` já chegam em
  `wgetch`. Mapear em `ui_ler_comando()` ([ui.c:96-108](../../aventureiro/src/ui.c#L96-L108)):
  `KEY_UP`→Norte, `KEY_DOWN`→Sul, `KEY_RIGHT`→Leste, `KEY_LEFT`→Oeste (checar orientação com o
  usuário — o mapa ASCII do Pacote 14 pode já ter uma convenção N/S/L/O em tela pra bater).
- Decidir o contrato de retorno: mais simples é adicionar um terceiro pseudo-valor tipo
  `-3..-6` (um por direção) que `game_loop` traduz direto pra `comando_mover(jogador, mapa, bd, dir)`
  **pulando** `comando_mover_interativo`/o prompt "para que lado" — já que a seta já diz a direção.
- **Direção sem saída conectada**: decidir o comportamento (ignorar a tecla silenciosamente vs.
  mensagem "não há saída nesse sentido" sem consumir rodada) — original não tem equivalente direto
  pra isso, então é decisão de UX nova, não fidelidade.
- Atualizar a ajuda (`mostrar_ajuda()`, [game.c:150-164](../../aventureiro/src/game.c#L150-L164)) pra
  mencionar o atalho.

## Critério de aceite

Jogando manualmente: apertar uma seta move o jogador direto na direção correspondente (quando há
saída conectada), sem precisar digitar `0` antes nem escolher a letra depois. `0` + letra continua
funcionando do jeito antigo. `tests/smoke_test.py` atualizado pra exercitar as teclas de seta.
