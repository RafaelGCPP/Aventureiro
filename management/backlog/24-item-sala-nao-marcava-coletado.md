# Pacote 24 — fidelidade: item de sala sorteava de novo a cada exame

**Tamanho:** S · **Depende de:** [Pacote 12](12-fidelidade-formulas.md)

## Objetivo

Usuário notou: ficar examinando a mesma sala repetidamente sorteia itens novos direto, sem limite.
Checado no `aventureiro.p.bas` (linhas 5220-5230):

```
5220 LET X$=R$(D,A,6)
5230 LET R$(D,A,6)=" "
```

O tipo de item da sala é lido pra uma variável local (`X$`) e o campo da sala (`R$(D,A,6)`) é
**apagado imediatamente**, antes até de decidir a recompensa. Reexaminar a mesma sala depois disso
lê `R$(D,A,6)=" "`, não bate com nenhum dos tipos (`P`/`M`/`E`) e cai direto em `"NADA."` (linha
5410). Ou seja: **é um achado de item único por sala**, não infinito — a porta tinha até o campo
certo pra isso (`Celula::item_coletado` já existia em `types.h`), mas nada nunca setava esse campo
pra `true`.

## Entregáveis

- `combat.c` (`comando_examinar_sala`): `celula->item_coletado = true;` logo após confirmar que há
  item a coletar (mesmo ponto onde o original apaga `R$(D,A,6)`, antes de sortear o tipo/valor da
  recompensa) — assim reexaminar a mesma sala depois cai no `if (!celula->tem_item ||
  celula->item_coletado)` já existente e mostra `"Nada."`.

## Critério de aceite

Jogando manualmente: examinar uma sala com item mostra a recompensa (energia/medicamento/dinheiro)
normalmente na primeira vez; examinar a mesma sala de novo depois mostra `"Nada."` sempre.
`tests/smoke_test.py` continua passando.

**Resolvido e confirmado.** Verificado com pexpect+pyte (seed 11): primeiro exame de uma sala achou
"Cargas de energia: +200.", segundo exame na mesma sala (mesma posição, sem sair) deu "Nada." — como
esperado. `ctest --test-dir build` rodado 3x seguidas sem falha após a mudança.
