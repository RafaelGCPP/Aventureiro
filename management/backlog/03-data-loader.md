# Pacote 3 — `data_loader.h/.c`

**Tamanho:** M · **Depende de:** [Pacote 1](01-dados-json.md), [Pacote 2](02-types-util.md)

## Objetivo

Ler os 4 arquivos JSON do Pacote 1 via cJSON e preencher `BaseDeDados` e `Config` (structs do Pacote 2).

## Entregáveis

- `src/data_loader.h/.c` com uma função tipo `bool carregar_dados(const char *dir, BaseDeDados *bd, Config *cfg)`.
- Tratamento de erro claro se um arquivo estiver ausente ou malformado (mensagem + saída, sem crash).
- Smoke test em `main.c`: carrega os dados e imprime na tela (`printf`) as 12 salas, 10 armas e
  20 tripulantes carregados, para conferência visual manual contra a seção 4 do
  [handover](../../handover_aventureiro_c.md).

## Critério de aceite

Rodar o binário imprime exatamente os mesmos dados das tabelas do handover (sem truncar strings,
sem off-by-one nos arrays).
