# Brief permanente — Fase 4: interações e combate

## Objetivo

Completar todas as regras de interação com tripulantes e fechar o motor de jogo.

## Pré-condições

Transições e mapa da Fase 3 estáveis.

## Contexto mínimo a ler

- matriz/golden scenarios da Fase 0;
- `kotlin/docs/03-estrategia-de-testes.md`;
- `aventureiro/src/combat.h/.c`;
- backlogs 06a, 06b, 12, 13, 15, 20, 23, 29 e 32 nos pontos relevantes.

## Escopo

- troca de arma;
- ataque, acerto, dano, energia, escudo e contra-ataque;
- morte, loot e remoção de encontro;
- fuga do jogador e seguimento 50% condicionado ao destino;
- pânico e fuga multi-sala do tripulante;
- decisão pendente de seguir/não seguir e chance de perder rastro;
- subornar, irritar e ser amigável;
- eventos de revelação do TWIN com nome, arma e frase separados;
- regressões históricas e replay cross-target.

## Fora de escopo

Strings/localização final, gerenciamento de sessões, HTTP e UI.

## Fatias internas

1. ataque/contra-ataque;
2. morte/loot/troca de arma;
3. fuga do jogador;
4. fuga do tripulante e decisão de seguir;
5. comunicação;
6. regressões e matriz completa.

Cada fatia termina em testes antes da seguinte.

## Critérios de aceite

- dez comandos possuem cenários de sucesso/falha;
- vida e energia do encontro persistem ao mover;
- fugir e não ser seguido deixa inimigo na origem;
- tripulante fugido é movido mesmo se não for seguido;
- decisão pendente bloqueia comandos incompatíveis e não revela caminho;
- mensagens não são buffers fixos; eventos completos não truncam;
- morte e vitória tornam o estado terminal;
- JVM e browser reproduzem o mesmo roteiro.

## Devolver à Fase 5

Catálogo completo de comandos/eventos, estados terminais, decisões pendentes e necessidades de projeção.

## Skills sugeridos

- `to-tickets` antes da implementação para dividir as fatias e blockers;
- `codebase-design` para preservar um seam pequeno do motor;
- `tdd` e `implement` em um tracer bullet por vez;
- `code-review` ao final de cada ticket.

## Limite de contexto

Não integrar uma UI. Para inspecionar cenários, usar asserts e renderizador de teste mínimo.
