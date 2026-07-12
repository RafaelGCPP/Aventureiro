# Handoff — Fase 3: estado, mapa e exploração

## Objetivo

Entregar o primeiro vertical slice jogável do motor: criar partida, explorar o mapa e encerrar pelo teleporte, ainda sem combate completo.

## Pré-condições

Conteúdo validado e fake de repositório disponíveis.

## Contexto mínimo a ler

- `kotlin/docs/02-modelo-de-dados.md`, estado/comandos/projeção;
- `kotlin/docs/03-estrategia-de-testes.md`;
- matriz da Fase 0;
- `aventureiro/src/map.c`, `player.c` e trechos de exploração de `combat.c`;
- backlogs 04, 05, 11, 12, 14, 22, 24, 25 e 33 quando citados pela matriz.

## Escopo

- `GameState`, mapa, célula, jogador e RNG versionado;
- DFS iterativo, portas extras e povoamento;
- inicialização na sala de teleporte segura;
- mover, escudo, medicamento, situação, examinar e acionar teleporte;
- escuridão, lanterna, acidente, item único, visitada/examinada;
- eventos semânticos e projeção básica com fog of war;
- propriedades e golden scenarios de exploração.

## Fora de escopo

Ataque, reação, fuga, comunicação, textos finais e frontends.

## Entregáveis

- motor parcial puro;
- RNG roteirizado/test support;
- propriedades do mapa em muitas seeds;
- cenários de exame claro/escuro e teleporte;
- hash/replay simples de estado para comparar JVM e browser.

## Sequência sugerida

1. Estado e invariantes.
2. Gerador e propriedades.
3. Jogador e início.
4. comandos de navegação/recursos.
5. exame/itens e projeção.
6. replay cross-target.

## Critérios de aceite

- todas as células alcançáveis e portas recíprocas;
- teleporte único, visitado e seguro;
- cinco medicamentos e Pistola Laser iniciais conforme conteúdo;
- item não reaparece; sala sem item fica examinada;
- dados ocultos não aparecem na projeção;
- mesma sequência roteirizada produz mesmo resultado nos targets.

## Devolver à Fase 4

Forma de transição, eventos existentes, fixtures de mapa/encontro e quaisquer desvios registrados.

## Limite de contexto

Não começar combate. Fakes de encontro podem existir apenas para preparar fixtures.

