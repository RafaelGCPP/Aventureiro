# Handoff — Fase 0: baseline e decisões

## Objetivo

Transformar o port C e seu histórico em uma especificação curta, testável e sem ambiguidades antes de criar o projeto Kotlin.

## Pré-condições

Nenhuma. Esta fase não cria código de produção.

## Contexto mínimo a ler

- `kotlin/docs/00-contexto-e-escopo.md`;
- `kotlin/docs/02-modelo-de-dados.md`;
- `aventureiro/src/types.h`, `map.c`, `player.c`, `combat.h/.c`, `game.c`;
- `aventureiro/data/*.json`;
- objetivos e decisões dos pacotes 10, 12, 13 e 22–33;
- consultar o BASIC somente para uma divergência concreta.

## Escopo

- matriz dos dez comandos: entrada, pré-condições, consumo de turno, RNG, mutações, eventos, decisão seguinte e término;
- catálogo das constantes e origem C/BASIC;
- invariantes do mapa, jogador, encontro e item;
- cenários de regressão derivados dos bugs já corrigidos;
- fechar ADRs listados em `05-decisoes-e-riscos.md`;
- definir o perfil de compatibilidade inicial e a política de texto.

## Fora de escopo

Build Gradle, classes Kotlin, redesign de regras e escolha detalhada de layout.

## Entregáveis

- documento de especificação executável/traceabilidade;
- lista priorizada de golden scenarios;
- ADRs numerados para targets, item oculto, semântica de tripulante, mobile/web e terminal;
- checklist de dados esperados: 12/10/20 e configuração 8×8.

## Sequência sugerida

1. Extrair a matriz do C.
2. Cruzar apenas pontos controversos com backlog/BASIC.
3. Registrar invariantes e cenários de bugs.
4. Fechar ADRs com decisão e consequência.
5. Revisar se toda aleatoriedade tem faixa/distribuição.

## Critérios de aceite

- cada comando possui caminho de sucesso e falhas relevantes;
- pacotes 22–33 aparecem na rastreabilidade;
- não resta decisão necessária para estruturar o build da Fase 1;
- nenhuma regra é descrita apenas por mensagem textual;
- divergências aceitas são explícitas.

## Devolver à Fase 1

Targets iniciais, perfil de compatibilidade, lista de módulos, algoritmo de RNG a investigar e ADRs fechados.

## Limite de contexto

Não reler os 34 pacotes integralmente. Buscar por regra/bug específico e registrar a conclusão consolidada.

