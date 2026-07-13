# Domain Docs

This is a single-context repository. The C and Kotlin versions are implementations of the same game domain, not separate bounded contexts.

## Before exploring, read these

- `CONTEXT.md` at the repository root, when it exists;
- relevant ADRs under `docs/adr/`, when they exist.

If these paths do not exist, proceed silently. Do not create empty placeholders. The `domain-modeling` skill, including when reached through `grill-with-docs`, creates the glossary and ADRs lazily when terminology or a decision is actually resolved.

## Expected structure

```text
/
├── CONTEXT.md
├── docs/
│   └── adr/
└── aventureiro/ and kotlin/
```

`CONTEXT.md` is a glossary of domain language and contains no implementation decisions. `docs/adr/` records architectural decisions that are hard to reverse, surprising without context, and the result of a real trade-off.

## Use the glossary vocabulary

Use canonical domain terms from `CONTEXT.md` in issue titles, specifications, tests, interfaces, refactoring proposals, and user-facing planning. Do not drift to synonyms the glossary explicitly rejects.

If a required concept is absent, reconsider whether it is implementation language. If it is a real domain gap, resolve it with `domain-modeling` before adding it to the glossary.

## Flag ADR conflicts

If proposed work contradicts an existing ADR, surface the conflict explicitly and identify the ADR. Do not silently override or weaken an accepted decision.

