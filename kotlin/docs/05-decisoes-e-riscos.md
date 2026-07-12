# Decisões iniciais e riscos

## ADRs iniciais

### ADR-001 — Núcleo Kotlin Multiplatform

Decisão: domínio, motor e aplicação ficam em código comum. Motivo: mobile e web client-side only precisam executar as regras sem servidor e sem duplicação.

### ADR-002 — Hexagonal e estado unidirecional

Decisão: o motor é puro no nível da interface, recebendo estado, comando e RNG e devolvendo transição/eventos. I/O e apresentação são adaptadores.

### ADR-003 — IDs estáveis

Decisão: IDs de conteúdo são value objects persistentes; posição em arrays é detalhe de armazenamento. Isso remove a suposição frágil do C de que `id == índice`.

### ADR-004 — Eventos semânticos, não strings

Decisão: regras não produzem texto pronto. Localização, quebra de linha e ritmo pertencem à apresentação/aplicação.

### ADR-005 — Repositórios separados

Decisão: conteúdo estático, saves e sessões ativas têm portas distintas. Um `Repository<T>` genérico esconderia semânticas de validação e concorrência diferentes.

### ADR-006 — Compatibilidade comportamental, não RNG binário

Decisão: não reproduzir `rand()` da libc. Reproduzir regras, faixas, distribuições e estados, usando um RNG multiplataforma versionado para novos saves.

### ADR-007 — API é frontend

Decisão: Ktor/JVM hospeda a aplicação headless, mas não define as regras nem o modelo central. Mobile e web podem rodar offline.

## Decisões a fechar na Fase 0

- significado/nome definitivo do campo `agressivo`;
- momento de resolver o tipo de item oculto;
- alvo mobile inicial: Android somente ou Android+iOS;
- UI mobile compartilhada ou nativa;
- Kotlin/JS ou Wasm/JS como primeiro alvo web;
- biblioteca de terminal após spike de Unicode, resize e pseudo-terminal;
- política de saves na primeira release;
- se mensagens da versão C são golden text ou apenas referência de sentido.

## Riscos e mitigação

| Risco | Impacto | Mitigação |
|---|---|---|
| lógica comum usa API JVM por acidente | web/mobile bloqueados tarde | habilitar JVM e browser já na Fase 1; build dos dois em CI |
| UI textual dirige arquitetura | acoplamento repetido do C | frontend só consome `GameView` e envia comandos |
| DTO/API vaza mapa oculto | quebra de regra e segurança | projeção dedicada + testes de não vazamento |
| saves quebram após mudança de dados | partidas irrecuperáveis | `schemaVersion`, `contentVersion`, migrações e rejeição clara |
| aleatoriedade impede testes | flakiness e regressões invisíveis | porta de RNG, RNG roteirizado e replay |
| excesso de módulos no início | custo Gradle/token alto | poucas unidades físicas, fronteiras lógicas; extrair quando necessário |
| Compose web/mobile domina o cronograma | núcleo fica sem validação | frontends só depois do serviço headless estabilizado |
| biblioteca terminal não cobre Termux/Unicode | perda do frontend de referência | spike limitado na Fase 6 e fallback simples |
| SQLite vira requisito prematuro | atraso sem validar porta | memória+JSON primeiro; SQLite só na Fase 9 |
| paridade baseada só no handover antigo | reintrodução de bugs | matriz inclui backlog 21–33 e código atual |
| eventos proliferam sem taxonomia | contratos instáveis | catálogo de eventos versionado na Fase 5 |
| API recebe retries concorrentes | comandos duplicados | revisão esperada e idempotency key |

## Critérios de escolha tecnológica

Não fixar versões neste documento. Na Fase 1, escolher versões compatíveis e piná-las no catálogo Gradle. Bibliotecas devem:

- suportar os targets realmente habilitados;
- não invadir domínio/motor;
- ter manutenção ativa e documentação primária;
- permitir testes sem ambiente gráfico;
- ser substituíveis atrás de uma borda pequena.

Ktor é o candidato natural para API JVM; serialização oficial Kotlin é candidata para DTOs; Compose Multiplatform é candidato para mobile/web; SQLDelight é candidato para SQLite multiplataforma. A seleção final exige spike e ADR, não adoção automática.

