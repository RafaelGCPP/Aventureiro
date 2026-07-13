# Plano da versão Kotlin de O Aventureiro

Este diretório contém o plano de implementação da versão Kotlin. Ele foi preparado a partir de três fontes, nesta ordem de autoridade:

1. o comportamento implementado no port C em `aventureiro/src`, seus dados e testes;
2. os registros de decisões e correções em `management/backlog` (inclusive os pacotes 21–33 que não aparecem no índice desatualizado);
3. o BASIC original e `handover_aventureiro_c.md`, usados para rastreabilidade e para esclarecer regras.

Nenhum código Kotlin é definido aqui. Os contratos apresentados são conceituais; nomes concretos podem ser ajustados durante a implementação por ADR.

## Leitura recomendada

- [00-contexto-e-escopo.md](00-contexto-e-escopo.md): baseline, objetivos, decisões e não objetivos.
- [01-arquitetura.md](01-arquitetura.md): modelo arquitetural, módulos e dependências.
- [02-modelo-de-dados.md](02-modelo-de-dados.md): conteúdo estático, estado de partida, comandos, eventos e persistência.
- [03-estrategia-de-testes.md](03-estrategia-de-testes.md): paridade, determinismo e pirâmide de testes.
- [04-plano-faseado.md](04-plano-faseado.md): sequência, dependências e critérios de saída.
- [05-decisoes-e-riscos.md](05-decisoes-e-riscos.md): decisões candidatas, índice de ADRs e riscos.
- [06-processo-com-skills.md](06-processo-com-skills.md): fontes canônicas, workflow de skills e unidade de trabalho.
- [phases/README.md](phases/README.md): índice dos briefs permanentes de cada fase.

## Resultado arquitetural pretendido

O produto terá um núcleo de jogo headless em Kotlin Multiplatform. Esse núcleo não conhecerá terminal, HTTP, Android, iOS, DOM, arquivos ou banco de dados. Frontends e mecanismos de armazenamento serão adaptadores substituíveis:

- terminal JVM com experiência equivalente à versão ncurses;
- servidor de API JVM;
- aplicativo móvel usando o núcleo embarcado;
- aplicação web client-side only, também com o núcleo embarcado;
- repositórios de conteúdo JSON inicialmente, com YAML e SQLite possíveis sem alterar regras de jogo.

## Regra para sessões assistidas por IA

Uma fase é um milestone com um gate verificável; um ticket é a unidade que deve caber em uma sessão fechada. Carregar apenas o brief da fase, o ticket atual e os poucos documentos indicados por ambos. Trabalhar um ticket da frontier por vez e não antecipar tickets bloqueados.

Os briefs em `phases/` são permanentes e descrevem intenção, contexto e gate. O skill `handoff` tem outro papel: registrar em diretório temporário o estado de uma sessão interrompida, referenciando os artefatos permanentes sem duplicá-los.

## Fontes canônicas

- `CONTEXT.md`: glossário do domínio, criado de forma lazy por `domain-modeling`.
- `docs/adr/`: decisões arquiteturais aceitas.
- `kotlin/docs/`: arquitetura, modelo, estratégia, riscos e milestones duráveis.
- GitHub Issues: spec executável e tickets com critérios de aceite e dependências.
- diretório temporário do sistema: handoff de sessão incompleta.
