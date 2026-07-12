# Estratégia de testes

## Objetivo

Provar regras e fronteiras sem depender de uma UI específica. O principal ativo da migração será uma suíte comum que rode em JVM e browser e torne cada fase pequena e reversível.

## Oráculos

1. código C atual e seus testes para comportamento observável;
2. pacotes de backlog para bugs e decisões já corrigidos;
3. BASIC/handover para fórmulas e rastreabilidade;
4. invariantes matemáticos para geração e estado.

Não comparar a mesma seed entre C `rand()` e Kotlin. Comparar cenários roteirizados, intervalos, probabilidades em amostras controladas e consequências.

## Determinismo

O motor recebe uma fonte de aleatoriedade explícita. Testes usam:

- RNG roteirizado, que devolve valores predeterminados;
- RNG seeded multiplataforma com algoritmo definido pelo projeto;
- gravação do estado do RNG em snapshots;
- replay de comandos com hashes de estado por revisão.

Nenhum teste de domínio depende do relógio, sleeps ou ordem de threads.

## Camadas de teste

### Domínio e motor

Testes unitários por regra e cenários completos em `commonTest`. Cobertura obrigatória dos dez comandos, contra-ataque, loot, morte, vitória e decisões pendentes.

### Propriedades/invariantes

- mapa totalmente conectado;
- portas recíprocas e sem saída fora do grid;
- exatamente uma sala de teleporte segura;
- posição do jogador sempre válida;
- IDs referenciados existem;
- arma selecionada pertence ao inventário;
- item coletado não reaparece;
- encontro movido preserva vida/energia e existe em no máximo uma célula;
- projeção nunca revela célula não visitada;
- estado terminal rejeita novos comandos de jogo.

### Golden scenarios

Arquivos pequenos descrevem estado inicial, sequência de escolhas aleatórias, comandos, eventos esperados e estado final. Devem cobrir bugs 22–33, em especial fuga não seguida, apresentação completa do TWIN e sala examinada sem item.

### Contratos de repositório

A mesma suíte roda contra memória e JSON; posteriormente YAML/SQLite. Casos: round-trip de save, schema desconhecido, IDs duplicados, referência de arma inválida, chance fora de 0–100, conteúdo ausente e conflito de revisão.

### Aplicação/API

Testar criação de partida, revisão otimista, idempotência, decisão pendente, filtragem da projeção e DTOs versionados. Testes HTTP não revalidam todas as fórmulas do motor.

### Frontends

- terminal: smoke em pseudo-terminal, resize e sequência curta de comandos;
- API: testes de rota e contrato;
- mobile: testes de presenter/view-model e poucos testes de UI críticos;
- web: teste de bundle, inicialização offline e fluxo no browser.

## Matriz de rastreabilidade mínima

| Área | Fonte C/BASIC | Teste Kotlin esperado |
|---|---|---|
| mapa | `map.c`, pacotes 4 e 25 | conectividade, densidade e reciprocidade |
| jogador | `player.c`, pacotes 5, 12 e 22 | estado inicial, inventário e cura 8–10 |
| exploração | `combat.c`, pacotes 11, 20, 24 e 33 | escuro, lanterna, item único, examinada |
| combate | `combat.c`, pacotes 6b, 12 e 13 | energia, dano, escudo, reação e fuga |
| fuga | pacotes 13, 23 e 32 | seguir, não seguir, realocação e eventos completos |
| apresentação | pacotes 15–20 e 26–31 | contrato de eventos; smoke específico por frontend |
| fim | `game.c` | morte e teleporte |

## Gates por fase

- build limpo em todos os targets já habilitados;
- nenhum teste anterior removido ou afrouxado sem ADR;
- testes novos falham antes da implementação quando viável;
- cobertura de branches não é meta isolada; cenários e invariantes são o gate;
- frontends não importam internals do motor;
- adapters passam a suíte de contrato correspondente.

## Testes estatísticos

Usar apenas para confirmar distribuição, com seed fixa, tolerância documentada e amostra suficiente. Fórmulas discretas devem preferir teste exato com RNG roteirizado. Estatística não substitui testes de limites.

