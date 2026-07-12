# Modelo de dados

## Separação fundamental

Há três famílias de dados distintas e elas não devem compartilhar o mesmo agregado:

1. conteúdo estático: catálogo e regras carregados de JSON/YAML/SQLite;
2. estado mutável de uma partida: mapa gerado, jogador, encontros e decisões pendentes;
3. projeção pública: somente o que um frontend pode exibir naquele momento.

Essa separação evita salvar cópias desnecessárias do catálogo, permite trocar o repositório e impede que uma API revele salas ainda não exploradas.

## Conteúdo estático

### GameDefinition

Raiz imutável e versionada:

| Campo | Papel |
|---|---|
| `schemaVersion` | versão do formato persistido |
| `contentId` / `contentVersion` | identifica o conjunto de dados e balanceamento |
| `locale` | idioma original dos textos de conteúdo |
| `configuration` | tamanho e parâmetros iniciais |
| `ruleSet` | probabilidades, custos, faixas e perfil de compatibilidade |
| `roomTypes` | mapa por `RoomTypeId` |
| `weapons` | mapa por `WeaponId` |
| `crewArchetypes` | mapa por `CrewId` |

### Configuration

- tamanho do grid;
- vida, energia, dinheiro e medicamentos iniciais;
- arma inicial por ID;
- chances de tripulante, item, sala escura, acidente e porta extra.

### RuleSet

Concentra regras hoje espalhadas entre JSON e constantes em C: custo de lanterna e escudo, chances de fuga/pânico/perseguição, loot, cura, suborno, comunicação e dano de acidente. Valores fiéis e não ajustáveis podem continuar imutáveis, mas devem estar nomeados e associados a `ruleSetVersion` para que saves e testes saibam qual semântica usar.

### RoomTypeDefinition

`RoomTypeId`, nome/chave de texto e papel especial. Em vez de booleano genérico, recomenda-se um papel enumerado (`NORMAL`, `TELEPORT`) que possa crescer sem combinação inválida de flags.

### WeaponDefinition

`WeaponId`, nome/chave de texto, dano máximo, chance de acerto e custo de energia. Validações: dano positivo, chance 0–100 e custo não negativo.

### CrewDefinition

`CrewId`, nome/chave, frase/chave, vida inicial, flag de inteligência/agressividade conforme a semântica atual e `WeaponId`. O nome do campo deve ser decidido na Fase 0 porque no BASIC/C `agressivo` também controla capacidade de comunicação e a possibilidade de fugir, não simplesmente “ataca ou não”.

## Estado da partida

### GameState

| Campo | Papel |
|---|---|
| `gameId` | identidade da partida |
| `contentRef` | ID e versão do conteúdo usado |
| `ruleSetVersion` | semântica aplicada |
| `seed` | reprodução lógica |
| `rngState` | necessário para save/replay determinístico |
| `revision` | controle otimista e ordenação |
| `turn` | rodada lógica |
| `status` | `AWAITING_START`, `RUNNING`, `WON`, `DEAD`, `ABANDONED` |
| `ship` | mapa gerado e estado das células |
| `player` | recursos, posição e inventário |
| `pendingDecision` | escolha que deve ser resolvida antes de outro comando |

### ShipMap e CellState

`ShipMap` contém tamanho, posição do teleporte e células indexadas por uma posição validada. Cada célula contém:

- `RoomTypeId`;
- conjunto de portas cardeais, com reciprocidade obrigatória;
- escuridão;
- `visited` e `examined` independentes;
- item oculto e seu estado de coleta;
- encontro opcional.

Não persistir flags redundantes como `temTripulante` mais `tripulanteVivo`. A ausência/presença de `EncounterState` representa isso. Uma morte ou saída remove o encontro; um tripulante que foge é movido mantendo vida e energia.

### ItemState

O modelo deve distinguir:

- ausência de item;
- item oculto ainda não coletado;
- item coletado.

O tipo (`ENERGY`, `MEDICINE`, `MONEY`) pode ser pré-selecionado na geração, como sugere o BASIC, ou resolvido no primeiro exame para compatibilidade interna com o C. A escolha não deve ser inferida durante a codificação: será uma decisão registrada na Fase 0. Depois de resolvido, nunca se sorteia novamente.

### EncounterState

`CrewId`, vida atual e energia atual. Energia inicial fica entre 100 e 249 no baseline. O estado se desloca entre células em fugas; não se recria ao reentrar na sala.

### PlayerState

- vida, energia, dinheiro e medicamentos;
- posição;
- conjunto/lista ordenada de `WeaponId` possuídas;
- arma selecionada por ID, não por índice externo;
- escudo ligado.

Invariantes: arma selecionada pertence ao inventário; não há IDs duplicados; recursos que não admitem negativos são normalizados/validados; posição pertence ao mapa.

### PendingDecision

Representa escolhas multi-etapa sem depender de prompt. Primeiro caso: seguir ou não um tripulante que fugiu. O caminho sorteado e o encontro relacionado permanecem privados no estado. Enquanto há decisão pendente, apenas comandos compatíveis com ela são aceitos.

## Comandos

O conjunto público cobre os dez comandos e decisões auxiliares:

| Comando | Dados de entrada |
|---|---|
| Mover | direção |
| Atacar | nenhum; usa arma atual |
| Fugir | nenhum |
| Trocar arma | `WeaponId` possuída |
| Comunicar | postura e valor de suborno quando aplicável |
| Alternar escudo | nenhum |
| Usar medicamento | nenhum |
| Consultar situação | nenhum; pode ser consulta sem consumir turno |
| Examinar | escolha de lanterna quando necessária |
| Acionar teleporte | nenhum |
| Resolver decisão | ID/opção da decisão pendente |

Atalhos de teclado, ajuda e mapa não são comandos de domínio.

## Eventos

Eventos são fatos tipados e ordenados. Categorias mínimas:

- navegação e descoberta;
- descrição de sala e saídas;
- revelação/ausência de tripulante;
- ataque, acerto, erro, dano, morte e loot;
- fuga do jogador, perseguição do tripulante, pânico e trilha;
- comunicação e seus resultados;
- consumo/obtenção de recursos;
- escudo, lanterna, acidente e cura;
- item encontrado/ausente e sala examinada;
- vitória, morte e erro de regra.

Mensagens em português são renderizadas a partir desses eventos. Uma dica de apresentação pode marcar pausa após determinado evento, mas não altera a transição.

## Projeção visível

`GameView` contém HUD, sala atual, saídas, inventário, comandos permitidos, decisão pendente pública e `MapView`. Para células não visitadas, `MapView` não expõe tipo, item, tripulante, escuridão ou portas totalmente desconhecidas. Mantém os símbolos conceituais atuais: posição, teleporte, visitada e examinada.

## Portas de repositório

### GameContentRepository

Carrega uma `GameDefinition` validada por ID/versão. Não retorna árvores JSON nem entidades de banco. Falhas são estruturadas: fonte ausente, schema incompatível, campo inválido, ID duplicado e referência quebrada.

### SaveGameRepository

Lista metadados, grava snapshot com revisão esperada, lê e remove saves. Save inclui versão de schema, referência do conteúdo, RNG e decisão pendente. Conteúdo estático não deve ser duplicado, salvo se for necessário empacotar uma versão customizada.

### SessionRepository

Necessário principalmente para API. Faz criação, leitura e compare-and-set por revisão. Implementação local pode ser simples memória; implementações duráveis podem reutilizar snapshots.

### ContentDocumentSource

Porta opcional de infraestrutura abaixo do repositório para obter documentos por nome/URI. Permite que o mesmo decoder JSON leia arquivo JVM, asset mobile ou resposta de `fetch` no browser sem contaminar o domínio.

## Formato e migrações

Os JSONs Kotlin devem adotar um manifesto raiz versionado ou quatro documentos ligados por um manifesto. IDs deixam de significar índice. Chaves `_comment` podem ser toleradas na importação do formato C, mas comentários operacionais devem migrar para documentação ou metadados próprios.

Pipeline obrigatório:

`bytes/texto → DTO do schema → migração → validação cruzada → modelo de domínio imutável`

O caminho inverso só existe para ferramentas/saves; o domínio nunca serializa a si próprio diretamente.

