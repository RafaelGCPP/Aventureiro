# Brief permanente — Fase 0: baseline e decisões

## Objetivo

Transformar o port C e seu histórico em uma especificação curta, testável e sem ambiguidades antes de criar o projeto Kotlin.

## Pré-condições

Setup dos skills concluído em `AGENTS.md` e `docs/agents/`. Esta fase não cria código Kotlin nem altera o port C.

## Contexto mínimo a ler

- `AGENTS.md` e `docs/agents/domain.md`;
- `kotlin/docs/00-contexto-e-escopo.md`;
- `kotlin/docs/06-processo-com-skills.md`;
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

- `CONTEXT.md` criado apenas quando o primeiro termo canônico for resolvido;
- ADRs necessários em `docs/adr/`, sem transformar toda decisão em ADR;
- matriz de especificação executável/traceabilidade;
- lista priorizada de golden scenarios;
- spec publicada no GitHub Issues por `to-spec`, com o label `ready-for-agent`;
- decisões fechadas para targets, item oculto, semântica de tripulante, mobile/web e terminal;
- checklist de dados esperados: 12/10/20 e configuração 8×8.

## Sequência sugerida

1. Iniciar `grill-with-docs` e resolver uma decisão por vez.
2. Extrair a matriz do C, pesquisando fatos em vez de perguntá-los.
3. Cruzar apenas pontos controversos com backlog/BASIC.
4. Registrar termos, invariantes, cenários de bugs e ADRs realmente necessários.
5. Revisar se toda aleatoriedade tem faixa/distribuição.
6. Executar `to-spec`, confirmar o seam de testes e publicar a spec.

## Critérios de aceite

- cada comando possui caminho de sucesso e falhas relevantes;
- pacotes 22–33 aparecem na rastreabilidade;
- não resta decisão necessária para estruturar o build da Fase 1;
- nenhuma regra é descrita apenas por mensagem textual;
- divergências aceitas são explícitas;
- spec publicada referencia os documentos locais sem copiá-los integralmente;
- nenhum código Kotlin foi criado.

## Skills sugeridos

- `grill-with-docs` e `domain-modeling` durante a entrevista;
- `research` somente quando uma decisão depender de fonte primária externa;
- `to-spec` depois que as decisões e seams estiverem fechados;
- `handoff` apenas se a entrevista for interrompida.

## Devolver à Fase 1

Link da spec, glossário, ADRs, targets iniciais, perfil de compatibilidade, lista de módulos, seams de teste e algoritmo de RNG a investigar.

## Limite de contexto

Não reler os 34 pacotes integralmente. Buscar por regra/bug específico e registrar a conclusão consolidada.
