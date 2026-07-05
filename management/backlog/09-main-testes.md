# Pacote 9 — `main.c` final + Makefile completo + smoke test automatizado

**Tamanho:** M · **Depende de:** [Pacote 8](08-game.md)

## Objetivo

Integração final: parse de argumentos (ex.: `--seed`, `--data-dir`), inicialização do RNG,
carregamento de dados, `ncurses` init/end, e um harness de teste automatizado via `pexpect`/`pyte`
(mencionado na seção 6 do [handover](../../handover_aventureiro_c.md)) simulando comandos aleatórios
para fuzzing básico de robustez.

## Entregáveis

- `src/main.c` definitivo substituindo os `main.c` provisórios dos pacotes anteriores.
- `Makefile` completo (build, clean, e um alvo `test` que roda o harness Python).
- Script Python (`tests/smoke_test.py`) que abre o binário via `pexpect`, envia sequências de teclas
  aleatórias (0-9) por N passos, e falha se o processo crashar ou travar.

## Critério de aceite

`make && make test` passa localmente, incluindo pelo menos uma rodada de fuzzing de ~200 comandos
aleatórios sem crash.
