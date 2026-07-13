# Brief permanente — Fase 6: frontend terminal

## Objetivo

Entregar a experiência textual completa em JVM usando exclusivamente a porta de aplicação.

## Pré-condições

Fase 5 concluída e contrato congelado para a release.

## Contexto mínimo a ler

- contrato/exemplos da Fase 5;
- `aventureiro/src/ui.h/.c` e `game.c` como referência de UX;
- backlogs 16–20 e 26–32;
- testes de terminal em `aventureiro/tests/smoke_test.py`.

## Escopo

- spike comparando bibliotecas de terminal para Unicode, teclas, resize e teste PTY;
- composição JVM com JSON;
- HUD, log, mapa, barra de comandos, ajuda e prompts;
- atalhos de setas e mapa em tela cheia;
- layouts largo/estreito e resize;
- quebra de palavras, truncamento seguro e ritmo configurável;
- smoke test automatizado.

## Fora de escopo

Regra de jogo no frontend, API e persistência alternativa.

## Entregáveis

- ADR da biblioteca;
- executável/distribuição JVM;
- renderer e input mapper isolados;
- smoke tests para terminal normal, 53×29 e largura extrema;
- opção de desativar pausas.

## Sequência sugerida

1. Spike descartável e ADR.
2. Loop mínimo usando a aplicação.
3. HUD/log/mapa.
4. prompts/atalhos/decisões.
5. responsividade e Unicode.
6. smoke/fuzz curto.

## Critérios de aceite

- nenhum import do motor interno;
- resize não corrompe HUD/mapa;
- nomes e mensagens UTF-8 não são cortados no meio;
- decisão de seguir e exame escuro funcionam;
- morte/vitória e reinício/saída restauram terminal;
- smoke não usa sleeps dramáticos obrigatórios.

## Devolver à Fase 10

Procedimento de empacotamento, plataformas testadas, limitações da biblioteca e checklist de regressão visual.

## Skills sugeridos

- `research` para bibliotecas e suporte real de terminal;
- `prototype` para o spike descartável de Unicode/resize/PTY;
- `tdd` para input mapper e renderer pelos seams confirmados;
- `implement` e `code-review` por ticket.

## Limite de contexto

Não tentar copiar coordenadas ncurses linha a linha. Preservar comportamento e informação, adaptando layout à biblioteca.
