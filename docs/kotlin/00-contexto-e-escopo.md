# Contexto, baseline e escopo

## Objetivo

Reimplementar O Aventureiro em Kotlin com separação rigorosa entre regras, orquestração, apresentação e infraestrutura. A arquitetura deve permitir múltiplos frontends e armazenamentos sem bifurcar o motor do jogo.

## Baseline funcional atual

O port C já implementa os dez comandos do original, mapa 8×8 regenerado por partida, conectividade garantida por DFS aleatório, portas extras, jogador, combate, comunicação, fuga e perseguição, itens, escudo, medicamentos, teleporte, mapa com neblina de exploração e interface terminal responsiva.

As correções posteriores ao handover que devem ser consideradas parte do baseline incluem:

- fórmulas mais fiéis ao BASIC para cura, escudo e loot;
- energia própria e fuga multi-sala de tripulantes;
- permanência do tripulante na sala de origem quando o jogador foge e não é seguido;
- arma do tripulante revelada na apresentação;
- cinco medicamentos iniciais;
- item de sala coletável apenas uma vez;
- distinção entre sala visitada e examinada;
- mapa permanente, mapa em tela cheia como fallback e atualização antes da narração;
- setas como atalho, barra de comandos, pausas dramáticas configuráveis;
- UTF-8, quebra de palavras, resize e layouts estreitos, inclusive Termux;
- proteção contra truncamento das mensagens do TWIN.

`management/README.md` não lista os pacotes 21–33, portanto não pode ser usado sozinho para determinar o estado implementado.

## Política de compatibilidade

O alvo é paridade comportamental observável com o port C atual, preservando a intenção do BASIC quando os documentos registram uma correção de fidelidade. Não se exige reproduzir a sequência exata de `rand()` do C para uma mesma seed. Exige-se:

- mesmas pré-condições e consequências dos comandos;
- mesmas faixas e distribuições probabilísticas documentadas;
- mesmos estados terminais e regras de retenção/perda;
- mesma informação revelada ao jogador;
- invariantes equivalentes do mapa e inventário.

Qualquer mudança deliberada de regra deve ser registrada como ADR e protegida por um perfil de regras versionado; não deve entrar disfarçada como detalhe de implementação.

## Escopo do plano

Inclui:

- projeto Kotlin Multiplatform e suas fronteiras;
- domínio, motor determinístico e camada de aplicação headless;
- modelo de dados e contratos de repositório;
- adaptador JSON e validação;
- frontend terminal, API, mobile e web client-side only;
- persistência opcional de partidas e caminho para YAML/SQLite;
- testes de paridade, propriedade, contrato e smoke;
- migração faseada com handoff por fase.

## Não objetivos iniciais

- alterar o port C ou o BASIC;
- multiplayer ou comandos concorrentes na mesma partida;
- editor visual de mapas/dados;
- autenticação pública, monetização ou serviços em nuvem;
- reproduzir exatamente layout e temporização ncurses em todos os frontends;
- localizar o jogo para outros idiomas antes de estabilizar eventos semânticos e o português;
- adotar event sourcing completo como armazenamento primário.

## Princípios de implementação

1. O motor recebe um comando e produz novo estado mais eventos; não lê teclado e não desenha UI.
2. Aleatoriedade, relógio e geração de identificadores entram por portas controláveis.
3. O domínio usa identificadores estáveis, nunca posição em array como identidade persistida.
4. Conteúdo de jogo é imutável durante uma partida; estado de partida é separado dele.
5. Frontends recebem projeções seguras, sem conteúdo de salas ainda oculto.
6. Pausas são dicas de apresentação, nunca `sleep` dentro do motor.
7. Toda borda serializada contém `schemaVersion` e possui validação/migração explícita.
8. A primeira implementação de cada contrato inclui um fake em memória e testes de contrato.

## Critério global de conclusão

A versão estará arquiteturalmente completa quando a mesma suíte de cenários do motor rodar nos targets comuns, os quatro frontends consumirem o mesmo contrato de aplicação, o JSON puder ser substituído por um repositório em memória ou SQLite sem tocar no domínio e as regras críticas do port C estiverem cobertas por testes rastreáveis.

