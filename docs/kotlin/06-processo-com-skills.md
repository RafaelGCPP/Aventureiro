# Processo de trabalho com skills

## Objetivo

Compatibilizar o plano Kotlin com os skills de engenharia sem criar fontes concorrentes de verdade. O plano local explica a arquitetura e os milestones; o tracker controla o trabalho executável.

## Artefatos e responsabilidade

| Artefato | Responsabilidade | Não deve conter |
|---|---|---|
| `CONTEXT.md` | glossário do domínio | implementação, cronograma ou decisões arquiteturais |
| `docs/adr/` | decisões aceitas e seus trade-offs | backlog, diário de sessão ou riscos genéricos |
| `kotlin/docs/` | arquitetura, modelo, testes, riscos e milestones | estado transitório de tickets |
| GitHub spec | problema, solução, histórias, decisões e seams de teste | cópia integral dos documentos locais |
| GitHub tickets | tracer bullets, blockers e critérios de aceite | ensaio arquitetural já registrado em ADR/plano |
| handoff temporário | estado de um ticket interrompido e próximos comandos | duplicação de spec, plano, ADR, diff ou commit |

## Workflow da Fase 0

1. Iniciar pelo brief `phases/fase-00-baseline.md`.
2. Executar `grill-with-docs`, que combina `grilling` e `domain-modeling`.
3. Resolver decisões uma por vez. Fatos verificáveis devem ser pesquisados no repositório, não perguntados ao usuário.
4. Criar `CONTEXT.md` apenas quando o primeiro termo canônico for resolvido.
5. Criar ADR apenas quando a decisão for difícil de reverter, surpreendente sem contexto e fruto de trade-off real.
6. Consolidar matriz de paridade, constantes, invariantes e golden scenarios.
7. Executar `to-spec`, confirmar o seam de testes e publicar a spec no GitHub com `ready-for-agent`.
8. Encerrar a fase sem criar código Kotlin.

## Da spec aos tickets

Depois da Fase 0, `to-tickets` divide a spec em tracer bullets. Fase e ticket têm papéis diferentes:

- fase: milestone/gate arquitetural;
- ticket: unidade implementável em uma sessão;
- ciclo TDD: uma pequena fatia comportamental dentro do ticket.

Tickets carregam blockers explícitos. Trabalhar somente a frontier: tickets abertos, desbloqueados e ainda não assumidos.

## Seams por estágio

O requisito de fatia vertical é relativo ao seam já disponível, não aos quatro frontends futuros ao mesmo tempo.

| Estágio | Seam mais alto disponível |
|---|---|
| Fases 1–2 | build comum e contrato de `GameContentRepository` |
| Fases 3–4 | interface pública do motor e transição de estado |
| Fase 5 | porta da aplicação headless e `GameView` |
| Fases 6–9 | entrada do frontend até resposta renderizada via aplicação |
| Fase 10 | composição do adaptador e suíte de contrato |

Os seams definitivos são confirmados na Fase 0/spec. Testes observam comportamento público e não métodos internos.

## Implementação e review

- `implement` só é usado quando um ticket autoriza implementação; ele segue a spec/tickets, usa `tdd` quando possível, roda a suíte completa e cria commit conforme definido pelo próprio skill.
- Antes de usar `tdd`, o seam do ticket deve estar confirmado.
- `code-review` encerra o ticket comparando o diff com um fixed point explícito e separando aderência a padrões de aderência à spec.
- `prototype` é descartável e responde uma pergunta concreta; sua conclusão vai para ADR, issue ou nota antes da remoção.
- `research` usa fontes primárias e registra apenas achados que alimentam uma decisão.

## Handoffs

Os briefs em `phases/` são permanentes. O skill `handoff` é usado somente quando uma sessão termina com trabalho ainda em andamento. Ele grava no diretório temporário do sistema, inclui skills sugeridos para a retomada e referencia os artefatos existentes em vez de copiá-los.

## Labels e tracker

O tracker e o vocabulário estão documentados em `docs/agents/`. Antes de publicar a primeira spec, confirmar que os labels canônicos existem no GitHub. Labels adicionais de `wayfinder` só são necessários se esse fluxo vier a ser adotado; o plano atual já tem destino e milestones suficientes e não requer `wayfinder` agora.

