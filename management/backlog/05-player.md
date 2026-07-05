# Pacote 5 — `player.h/.c`

**Tamanho:** S · **Depende de:** [Pacote 2](02-types-util.md), [Pacote 4](04-map.md)

## Objetivo

Struct e ciclo de vida do jogador: nascer na Sala de Teleporte, iniciar vida/energia/dinheiro do
`config.json`, adicionar arma ao inventário, usar medicamento.

## Entregáveis

- `src/player.h/.c`: `Jogador jogador_iniciar(const Config *cfg, const Mapa *mapa)`, `jogador_adicionar_arma`,
  `jogador_usar_medicamento`, `jogador_trocar_arma`.
- Sem I/O, sem ncurses — puramente lógica testável por `assert` em `main.c` (ex.: jogador começa com
  vida cheia, usar medicamento sem tê-lo não faz nada, etc.).

## Critério de aceite

Bateria de `assert`s no smoke test cobre os casos acima e passa sem crash.
