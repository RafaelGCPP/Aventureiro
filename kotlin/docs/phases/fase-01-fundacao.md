# Brief permanente — Fase 1: fundação multiplataforma

## Objetivo

Criar uma fundação Kotlin Multiplatform mínima que prove as fronteiras e compile nos targets que mais restringem o núcleo.

## Pré-condições

Fase 0 concluída, especialmente ADRs de targets e UI.

## Contexto mínimo a ler

- este handoff;
- `kotlin/docs/01-arquitetura.md`;
- ADRs produzidos na Fase 0;
- documentação oficial das versões selecionadas.

## Escopo

- Gradle wrapper, Kotlin DSL e catálogo de versões;
- targets JVM e browser; mobile pode ser declarado agora somente se não tornar o gate inviável no ambiente;
- source sets/módulos com dependências na direção correta;
- teste mínimo comum executado em JVM e browser;
- lint/formatação, CI e política de warnings;
- spikes limitados de serialização e RNG persistível.

## Fora de escopo

Modelo completo, dados do jogo, regras, UI e API.

## Entregáveis

- build reproduzível;
- namespaces e módulos vazios ou marcadores mínimos;
- teste de arquitetura/dependência quando a ferramenta escolhida permitir;
- ADR da matriz de versões e algoritmo de RNG;
- instruções de build curtas.

## Sequência sugerida

1. Subir o menor projeto common+JVM+browser.
2. Fixar versões compatíveis.
3. Criar fronteiras lógicas/físicas.
4. Adicionar commonTest nos dois targets.
5. Configurar CI e documentação.

## Critérios de aceite

- build e testes passam em JVM e browser;
- `domain`/`engine` não importam Java, DOM, Ktor, Compose ou filesystem;
- dependências estão pinadas;
- nenhuma biblioteca é adicionada sem uso imediato ou spike documentado;
- o projeto C permanece intocado.

## Devolver à Fase 2

Comandos de build, matriz de targets, estrutura final, bibliotecas aceitas e limitações descobertas.

## Skills sugeridos

- `research` para matriz de versões e suporte oficial dos targets;
- `prototype` para spikes descartáveis de build, serialização ou RNG;
- `implement` no ticket aprovado;
- `code-review` ao final de cada ticket.

## Limite de contexto

Não modelar o jogo “para aproveitar”. O sucesso é o esqueleto verificável, não volume de classes.
