# Plano faseado de implementação

## Estratégia

O plano maximiza aprendizado cedo e limita contexto por ticket. Primeiro congela-se a especificação, depois prova-se a portabilidade do núcleo, em seguida implementam-se dados e regras. Frontends só começam quando existe uma porta de aplicação estável.

Cada fase é um milestone com um gate de saída, não uma sessão. `to-tickets` divide o milestone em tracer bullets verificáveis, cada um dimensionado para uma nova janela de contexto. Uma sessão trabalha somente um ticket da frontier; o brief permanente da fase não é um diário de execução.

## Visão geral

| Fase | Nome | Tamanho | Depende de | Resultado verificável |
|---|---|---:|---|---|
| 0 | Baseline e decisões | S | — | glossário, matriz de paridade, ADRs e spec publicada |
| 1 | Fundação multiplataforma | M | 0 | build/test JVM+browser e fronteiras vazias |
| 2 | Conteúdo e repositório JSON | M | 1 | dados atuais carregados e validados por contrato |
| 3 | Estado, mapa e exploração | M | 2 | mapa determinístico conectado e jogador explorando |
| 4 | Interações e combate | L | 3 | dez comandos, fuga/perseguição, morte e vitória |
| 5 | Aplicação headless | M | 4 | serviço de partida, projeção e replay versionados |
| 6 | Frontend terminal | M | 5 | jogo completo no terminal JVM |
| 7 | Frontend API | M | 5 | sessões via HTTP com revisão/idempotência |
| 8 | Frontend mobile | M/L | 5 | partida offline no alvo mobile escolhido |
| 9 | Frontend web client-side only | M | 5 | jogo no browser sem servidor |
| 10 | Repositório alternativo e release | M | 6–9 | SQLite ou YAML por contrato, matriz final e distribuição |

Após a Fase 5, as fases 6–9 são independentes conceitualmente. Devem continuar separadas para economizar contexto e permitir priorização; não precisam ser executadas na ordem numérica se houver um frontend prioritário.

## Fase 0 — Baseline e decisões

Consolidar regras atuais, bugs corrigidos e divergências conhecidas. Usar `grill-with-docs` para resolver uma decisão por vez, mantendo o glossário com `domain-modeling` e criando somente os ADRs que atendam aos critérios de irreversibilidade, surpresa e trade-off real. Produzir matriz comando × pré-condição × mutação × eventos × consumo de turno, inventário de mensagens e cenários golden priorizados.

Depois das decisões, `to-spec` sintetiza e publica a spec no GitHub Issues com `ready-for-agent`, após confirmação do seam principal de testes. Saída: nenhuma dúvida sem dono sobre compatibilidade, fontes canônicas atualizadas e spec pronta para decomposição.

## Fase 1 — Fundação multiplataforma

Criar build Gradle, catálogo de versões, targets JVM e browser desde o primeiro dia, source sets comuns, módulos/pacotes vazios e regras de dependência. Configurar testes comuns e CI mínimo. Fazer spikes curtos somente para serialização, RNG persistível e compilação no browser.

Saída: projeto vazio compila e testa nos dois targets; domínio não enxerga APIs JVM/JS.

## Fase 2 — Conteúdo e repositório JSON

Implementar value objects, definições estáticas, `RuleSet`, validação, portas de conteúdo/save e adaptadores em memória/JSON. Importar os quatro JSONs do C para um schema versionado sem alterar os originais. Criar testes de contrato e referências cruzadas.

Saída: 12 salas, 10 armas e 20 tripulantes são carregados; dados inválidos falham com erros estruturados; trocar memória por JSON não muda consumidores.

## Fase 3 — Estado, mapa e exploração

Implementar estado de partida, RNG determinístico, geração DFS, portas extras, povoamento, jogador inicial, movimento, escudo, medicamento, situação, exame e teleporte. Incluir projeção básica com neblina e distinção visitada/examinada.

Saída: propriedades do mapa passam em muitas seeds; cenários de exploração são reproduzíveis; nenhuma string/UI dentro do motor.

## Fase 4 — Interações e combate

Implementar ataque/contra-ataque, loot, fuga do jogador, persistência do inimigo, pânico, fuga multi-sala, decisão de seguir, troca de arma e comunicação. Completar eventos e regras de morte/vitória.

Esta é a maior fase. `to-tickets` deve dividi-la em tracer bullets separados para combate básico, reação/loot, fuga/perseguição, comunicação e regressões, explicitando blockers. Cada ticket termina verde antes de liberar o próximo.

Saída: dez comandos e bugs históricos cobertos por cenários roteirizados; replay idêntico no JVM e browser.

## Fase 5 — Aplicação headless

Criar serviço de partida, comandos públicos, decisões pendentes, revisão otimista, sessões, save/load, projeções seguras, catálogo de eventos e apresentação em português. Definir DTOs internos estáveis sem acoplar HTTP.

Saída: um teste headless cria e joga uma partida completa usando somente a porta de aplicação; saves restauram RNG e revisão.

## Fase 6 — Frontend terminal

Selecionar biblioteca após spike de Unicode, resize, teclado e pseudo-terminal. Implementar HUD, log, barra, mapa, prompts, setas, layout estreito e ritmo. O frontend apenas traduz entrada em comandos e renderiza respostas.

Saída: experiência completa e smoke test cobrindo terminal normal, estreito e resize.

## Fase 7 — Frontend API

Implementar servidor JVM, DTOs HTTP versionados, criação/consulta/comando/save, revisão esperada, idempotência e erros. Garantir que projeções não revelem estado oculto.

Saída: cliente HTTP joga uma partida; comandos repetidos não duplicam efeitos; conflitos retornam estado/revisão coerentes.

## Fase 8 — Frontend mobile

Fechar alvo Android ou Android+iOS conforme ADR. Implementar UI offline, ciclo de vida, save local, acessibilidade e retomada. Reutilizar presenter/view-model comum apenas se ele não carregar APIs de UI.

Saída: fluxo iniciar–jogar–suspender–retomar–vencer/morrer em dispositivo/emulador.

## Fase 9 — Frontend web client-side only

Compilar núcleo e aplicação para browser, carregar conteúdo empacotado, persistir save local e implementar UI responsiva/offline. Nenhum endpoint é obrigatório para jogar.

Saída: build estático funciona servido por host simples, recarrega um save e passa smoke no browser.

## Fase 10 — Repositório alternativo e release

Implementar um segundo adaptador real, preferencialmente SQLite se persistência cruzada for prioridade ou YAML se editabilidade for prioridade. Rodar a mesma suíte de contrato, fechar migrações, documentação de execução, licenças, artefatos e matriz de CI.

Saída: troca de adaptador por composição, sem mudança no domínio; release candidates dos frontends prioritários.

## Orçamento racional de contexto

Em cada ticket:

1. ler `AGENTS.md`, o brief da fase e o corpo completo do ticket;
2. abrir somente os documentos, ADRs e fontes indicados por eles;
3. confirmar o seam de teste quando ainda não estiver definido na spec;
4. implementar um tracer bullet com `tdd` quando houver código;
5. rodar gates relevantes e executar `code-review` contra o fixed point do ticket;
6. registrar testes, decisões e dívida no ticket ou commit, sem duplicar os documentos canônicos;
7. usar `handoff` somente se a sessão terminar com o ticket incompleto.

Evitar reler todo o BASIC, todo o backlog ou todos os frontends. A matriz da Fase 0, os ADRs e os golden scenarios passam a ser a memória operacional da migração.
