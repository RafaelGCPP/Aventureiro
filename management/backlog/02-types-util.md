# Pacote 2 — `types.h` + `util.h`

**Tamanho:** S · **Depende de:** — · Ver [handover_aventureiro_c.md](../../handover_aventureiro_c.md) seção 6

## Objetivo

Definir as structs "substantivo" e as funções de sorteio, sem nenhuma lógica de jogo ainda.

## Entregáveis

- `src/types.h` — `Config`, `TipoSala`, `Arma`, `Tripulante`, `BaseDeDados`, e os structs de estado
  (`Jogador`, `Celula`, `Mapa`) já com os campos previstos pelos módulos futuros (mesmo que só usados
  depois).
- `src/util.h` — funções inline de sorteio: `sorteio_intervalo(min, max)`, `sorteio_chance(percentual)`,
  etc. Usar `int` para índices, não enum, conforme o aviso da seção 6 do handover sobre `-Warray-bounds`
  com enums usados como índice de array.
- Um `main.c` provisório mínimo só para validar que os headers compilam sozinhos (`gcc -Wall -Wextra`).

## Critério de aceite

Compila limpo com `-Wall -Wextra -Werror`. Nenhuma lógica de jogo real ainda — é puramente a
"gramática" que os próximos pacotes vão falar.

## Pontos de atenção (do handover)

- `rand()` exige `<stdlib.h>` — fácil de esquecer em arquivos que só usam via uma função utilitária
  de outro header.
