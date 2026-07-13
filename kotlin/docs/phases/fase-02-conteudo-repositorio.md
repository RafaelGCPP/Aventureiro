# Brief permanente — Fase 2: conteúdo e repositório JSON

## Objetivo

Representar e carregar todo o conteúdo estático atual atrás de uma porta substituível, com schema e validação explícitos.

## Pré-condições

Build comum da Fase 1 aprovado.

## Contexto mínimo a ler

- `kotlin/docs/02-modelo-de-dados.md`, seções de conteúdo e repositórios;
- ADRs da Fase 0;
- `aventureiro/data/*.json`;
- `aventureiro/src/types.h` e `data_loader.c`;
- backlog 01 e 03 somente para critérios históricos.

## Escopo

- value objects de IDs e definições imutáveis;
- configuração e `RuleSet` versionado;
- porta `GameContentRepository` e fake em memória;
- DTOs de schema separados do domínio;
- adaptador JSON e fontes de documento necessárias aos targets atuais;
- validação local e cruzada;
- importação/cópia para a nova árvore Kotlin, sem alterar os JSONs C;
- testes de contrato.

## Fora de escopo

Mapa, jogador, save de partida, SQLite/YAML e UI.

## Entregáveis

- schema versionado documentado;
- conteúdo 12 salas/10 armas/20 tripulantes;
- erros estruturados;
- suíte comum memória+JSON;
- decisão registrada sobre constantes do `RuleSet`.

## Sequência sugerida

1. Definir IDs e invariantes.
2. Criar fake e testes de contrato.
3. Criar DTO/schema e decoder.
4. Migrar dados sem tocar na origem.
5. Adicionar validação cruzada e casos inválidos.

## Critérios de aceite

- IDs duplicados e referências quebradas falham claramente;
- chances fora de 0–100 e valores impossíveis são rejeitados;
- consumidores não sabem se a fonte é memória ou JSON;
- decoder compila nos targets comuns habilitados;
- `schemaVersion` é obrigatório e testado.

## Devolver à Fase 3

Modelo estático, regras validadas, builders de teste e contrato exato da fonte de aleatoriedade.

## Skills sugeridos

- `codebase-design` para o seam do repositório e profundidade do módulo;
- `tdd` para memória, JSON e validação pelo contrato público;
- `implement` por ticket;
- `code-review` ao final de cada ticket.

## Limite de contexto

Não implementar um framework genérico de persistência. Atender somente o catálogo real e erros necessários.
