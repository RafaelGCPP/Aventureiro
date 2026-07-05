# Pacote 8 — `game.h/.c`: orquestração

**Tamanho:** M · **Depende de:** [Pacote 6b](06b-combat-avancado.md), [Pacote 7](07-ui.md)

## Objetivo

O "maestro" — tela de título com a história/lista de comandos, loop principal (lê tecla, despacha
pro comando certo em `combat.h`), telas de fim de jogo (morte e vitória via teleporte). Ver fluxo
geral na seção 2.1 do [handover](../../handover_aventureiro_c.md).

## Entregáveis

- `src/game.h/.c`: `game_tela_titulo()`, `game_loop(Jogador*, Mapa*, BaseDeDados*)`, `game_tela_morte()`,
  `game_tela_vitoria()`.
- Liga `ui.h` (Pacote 7) com `combat.h`/`player.h` (Pacotes 5/6) — é o primeiro ponto em que o jogo
  todo existe de ponta a ponta.

## Critério de aceite

Jogo é jogável manualmente do início (tela de título) até um fim (morte ou teleporte), sem crash,
num terminal real.
