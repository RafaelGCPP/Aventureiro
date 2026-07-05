# Pacote 4 — `map.h/.c`: geração do labirinto

**Tamanho:** M · **Depende de:** [Pacote 2](02-types-util.md), [Pacote 3](03-data-loader.md)

## Objetivo

Gerar o mapa da nave garantindo conectividade total (randomized DFS a partir da Sala de Teleporte),
com portas extras adicionadas depois para atalhos/ciclos — corrigindo o problema do original (salas
potencialmente inalcançáveis), conforme apontado na seção 5.4 do
[handover](../../handover_aventureiro_c.md).

## Entregáveis

- `src/map.h/.c`: `Mapa gerar_mapa(const Config *cfg, const BaseDeDados *bd)`.
- Povoamento de cada `Celula` com tipo de sala sorteado (exceto a sala de teleporte, que é fixa),
  presença de tripulante (conforme chance do `config.json`) e presença de item.
- Uma função de verificação de conectividade (BFS/DFS a partir da sala de teleporte, confirma que
  todas as células são alcançáveis) usada só em teste/debug, não em runtime normal.
- Smoke test em `main.c`: gera um mapa e imprime uma visualização ASCII simples em texto de debug
  (não é a UI final — só para conferir visualmente que o labirinto é conexo).

## Critério de aceite

Rodar a verificação de conectividade em 100+ mapas gerados aleatoriamente (loop no smoke test) nunca
encontra sala inalcançável.
