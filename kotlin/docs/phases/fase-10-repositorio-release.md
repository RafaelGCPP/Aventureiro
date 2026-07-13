# Brief permanente — Fase 10: repositório alternativo e release

## Objetivo

Provar a substituição de armazenamento e consolidar os frontends prioritários em release candidates reproduzíveis.

## Pré-condições

Fases 2–5 concluídas e pelo menos um frontend validado; idealmente 6–9 concluídas.

## Contexto mínimo a ler

- contratos de conteúdo/save/sessão da Fase 5;
- resultados e limitações das Fases 6–9;
- `kotlin/docs/03-estrategia-de-testes.md`;
- ADRs de persistência e distribuição.

## Escopo

- escolher um adaptador: SQLite para persistência/consulta ou YAML para edição humana;
- implementar sem mudar domínio/aplicação;
- executar a mesma suíte de contrato;
- migração de schema e compatibilidade de saves;
- CI final por target prioritário;
- documentação de execução, licenças e empacotamento;
- matriz de paridade e riscos residuais.

## Fora de escopo

Adicionar novas mecânicas, multiplayer ou infraestrutura cloud.

## Entregáveis

- segundo adaptador real;
- prova de troca por composição/configuração;
- suíte de contrato verde;
- artefatos release candidate;
- guia operacional e de desenvolvimento;
- relatório final de paridade e dívida técnica.

## Sequência sugerida

1. Decidir SQLite versus YAML por necessidade real.
2. Implementar adapter mínimo.
3. Rodar contratos e migrações.
4. Fechar CI/empacotamento/licenças.
5. Executar matriz final e registrar gaps.

## Critérios de aceite

- nenhum arquivo de domínio/motor muda para suportar o adapter;
- conteúdo e save inválidos falham de forma equivalente ao JSON;
- artefatos são reproduzíveis a partir de checkout limpo;
- documentação distingue frontends concluídos dos experimentais;
- toda divergência do baseline tem ADR ou issue.

## Encerramento

Produzir um handoff de manutenção contendo versões, comandos, arquitetura real, matriz de testes, formato de dados, migrações e próximos passos. Só então considerar a migração concluída.

## Skills sugeridos

- `research` para o adaptador e empacotamento escolhidos;
- `codebase-design` para provar que o seam existente não precisa mudar;
- `tdd` para a suíte de contrato do segundo adaptador;
- `implement` e `code-review` por ticket.

## Limite de contexto

Esta fase consolida; não deve virar um novo backlog de features.
