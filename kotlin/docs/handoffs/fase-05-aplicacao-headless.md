# Handoff — Fase 5: aplicação headless

## Objetivo

Oferecer uma única porta estável para qualquer frontend criar, consultar, comandar, salvar e restaurar partidas.

## Pré-condições

Motor completo e eventos da Fase 4 aprovados.

## Contexto mínimo a ler

- `kotlin/docs/01-arquitetura.md`, contrato de execução/API;
- `kotlin/docs/02-modelo-de-dados.md`, projeção e repositórios;
- catálogo de comandos/eventos entregue pela Fase 4.

## Escopo

- serviço/facade de partida;
- criação com seed opcional e referência de conteúdo;
- revisão otimista e erros tipados;
- `SessionRepository` em memória;
- `SaveGameRepository` em memória e formato versionado;
- projeções `GameView` e `MapView` sem vazamento;
- comandos permitidos e decisões pendentes públicas;
- mapeamento de eventos para mensagens portuguesas e dicas de ritmo;
- replay, snapshot e restauração do RNG.

## Fora de escopo

HTTP, terminal, Compose, banco real e autenticação.

## Entregáveis

- porta de aplicação independente de transporte;
- DTOs internos/projeções imutáveis;
- catálogo versionado de eventos/textos;
- contratos de sessão/save;
- teste headless de partida inteira.

## Sequência sugerida

1. Definir request/response da porta.
2. Implementar sessão e revisão.
3. Implementar projeção segura.
4. Implementar save/replay.
5. Renderizar português e ritmo fora do motor.
6. Testar um fluxo completo.

## Critérios de aceite

- frontend fictício joga sem acessar estado interno;
- comando com revisão antiga não é aplicado;
- retry com mesmo identificador pode ser reconhecido sem duplicar mutação;
- save/restauração preserva próxima decisão aleatória;
- mapa oculto permanece oculto em todas as respostas;
- pausas podem ser ignoradas sem alterar estado.

## Devolver às Fases 6–9

Contrato público, exemplos de fluxo, códigos de erro, política de revisão, catálogo de mensagens e composição padrão.

## Limite de contexto

Não desenhar DTO pensando só em HTTP nem expor tipos internos por conveniência.

