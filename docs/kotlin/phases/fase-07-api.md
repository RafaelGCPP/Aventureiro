# Brief permanente — Fase 7: frontend API

## Objetivo

Expor a porta de aplicação por HTTP sem transformar contratos de transporte em domínio.

## Pré-condições

Fase 5 concluída.

## Contexto mínimo a ler

- contrato e erros da Fase 5;
- `kotlin/docs/01-arquitetura.md`, seção API;
- ADR de framework/serialização da Fase 1.

## Escopo

- servidor JVM e composição;
- DTOs HTTP versionados e mappers;
- criar, consultar, comandar, salvar/restaurar e encerrar partida;
- `expectedRevision` e conflito;
- idempotency key para comandos mutáveis;
- erros de validação, regra, ausência e schema;
- filtro de projeção e testes de rota;
- especificação OpenAPI se suportada sem duplicar o contrato manualmente.

## Fora de escopo

Autenticação pública, banco distribuído, WebSocket e matchmaking.

## Entregáveis

- API executável;
- documentação de requests/responses;
- suíte de integração HTTP;
- teste de concorrência/retry por partida;
- configuração de sessão e expiração.

## Sequência sugerida

1. DTOs e mappers.
2. rotas create/get/command.
3. revisão/idempotência.
4. save/restore/end.
5. erros e documentação.
6. testes de não vazamento.

## Critérios de aceite

- cliente HTTP conclui uma partida;
- DTO não serializa estado interno ou caminho secreto;
- comando duplicado não aplica efeito duas vezes;
- conflito informa revisão atual sem sobrescrever;
- servidor pode trocar JSON por fake/SQLite por composição;
- testes do motor não são repetidos via HTTP.

## Devolver à Fase 10

Contrato publicado, estratégia de distribuição, limites operacionais e decisões de armazenamento.

## Skills sugeridos

- `codebase-design` para DTOs e seam de transporte;
- `tdd` para rotas, revisão, idempotência e não vazamento;
- `implement` e `code-review` por ticket.

## Limite de contexto

Não adicionar complexidade de produção sem requisito: um processo e sessão em memória são suficientes para validar a arquitetura.
