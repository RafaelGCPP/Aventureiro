# Brief permanente — Fase 9: frontend web client-side only

## Objetivo

Entregar um build estático jogável no browser, sem backend obrigatório, reutilizando motor e aplicação comuns.

## Pré-condições

Fase 5 concluída e target browser saudável desde a Fase 1.

## Contexto mínimo a ler

- contrato e exemplo da Fase 5;
- ADR web da Fase 0/1;
- `kotlin/docs/02-modelo-de-dados.md`, projeção e fontes de documento;
- documentação oficial de Kotlin/JS ou Wasm/JS e toolkit escolhido.

## Escopo

- bundle estático e composição browser;
- carregamento de conteúdo empacotado/asset;
- UI responsiva com comandos, mapa, HUD e narrativa;
- persistência local de saves;
- funcionamento offline após carregamento quando viável;
- smoke em browser real e verificação de ausência de chamadas de API obrigatórias.

## Fora de escopo

Servidor de jogo, conta, ranking e sincronização.

## Entregáveis

- site estático distribuível;
- adaptador de `ContentDocumentSource` para assets/fetch;
- adaptador de save browser;
- testes de inicialização, reload e fluxo curto;
- orçamento inicial de bundle registrado.

## Sequência sugerida

1. Inicializar aplicação comum no browser.
2. carregar e validar conteúdo.
3. renderizar fluxo mínimo.
4. completar comandos/decisões/mapa.
5. save/reload/offline.
6. smoke e tamanho de bundle.

## Critérios de aceite

- jogo inicia e evolui sem endpoint de aplicação;
- reload restaura partida e próxima aleatoriedade;
- conteúdo oculto não aparece no DOM/DTO de projeção;
- build é hospedável como arquivos estáticos;
- regras passam nos testes comuns do target browser.

## Devolver à Fase 10

Formato de distribuição, browsers testados, armazenamento escolhido, tamanho de bundle e limitações offline.

## Skills sugeridos

- `research` para Kotlin/JS ou Wasm/JS e APIs do browser;
- `prototype` para UI e estratégia offline;
- `tdd` para composição, save/reload e projeção;
- `implement` e `code-review` por ticket.

## Limite de contexto

Não recriar um segundo motor em TypeScript/JavaScript. Interop deve ficar somente nas bordas do browser.
