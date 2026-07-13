# Brief permanente — Fase 8: frontend mobile

## Objetivo

Executar o jogo offline em dispositivo móvel com o mesmo núcleo e retomada segura.

## Pré-condições

Fase 5 concluída e ADR da Fase 0 definindo Android versus Android+iOS e UI compartilhada versus nativa.

## Contexto mínimo a ler

- contrato e exemplo headless da Fase 5;
- ADR mobile;
- `kotlin/docs/02-modelo-de-dados.md`, projeção/save;
- guias oficiais do target e toolkit escolhidos.

## Escopo

- composição mobile com conteúdo empacotado;
- tela de título, jogo, mapa, inventário/situação e fim;
- presenter/view-model consumindo a aplicação;
- entrada para todos os comandos e decisões;
- save local, suspensão e retomada;
- acessibilidade, orientação e tamanhos essenciais;
- testes de lógica de apresentação e smoke no emulador/dispositivo.

## Fora de escopo

Conta online, sincronização em nuvem, loja e notificações.

## Entregáveis

- aplicativo no(s) alvo(s) escolhido(s);
- armazenamento local atrás de `SaveGameRepository`;
- fluxo offline completo;
- documentação de build/execução e limitações de plataforma.

## Sequência sugerida

1. Composição e primeira projeção.
2. navegação/telas essenciais.
3. comandos e decisões.
4. mapa/HUD/narração.
5. save e lifecycle.
6. acessibilidade e smoke.

## Critérios de aceite

- iniciar/jogar/suspender/matar processo/retomar preserva RNG e revisão;
- UI não contém fórmulas ou sorteios;
- funciona sem rede após instalação;
- texto e controles são acessíveis;
- estado terminal é apresentado corretamente.

## Devolver à Fase 10

Targets realmente validados, passos de assinatura/distribuição ainda pendentes, formato de save e problemas de compatibilidade.

## Skills sugeridos

- `research` para suporte oficial dos targets e toolkit;
- `prototype` para fluxos e alternativas de UI;
- `tdd` para presenter/view-model e lifecycle pelo seam público;
- `implement` e `code-review` por ticket.

## Limite de contexto

Priorizar uma experiência completa simples. Animações e design system avançado ficam depois da paridade.
