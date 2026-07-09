# Pacote 15 — narrar a arma do tripulante ao apresentá-lo

**Tamanho:** S · **Depende de:** [Pacote 10](10-polimento.md)

## Objetivo

Fidelidade ao original: ao descrever a sala/tripulante (linha 6270 do `aventureiro.p.bas`), o BASIC
já revela a arma do inimigo na mesma mensagem que o apresenta, antes de qualquer ataque:

```
"TWIN REPORTA..."
 "E UM [M$] ARMADO COM [N$]. [X$]."
```

`M$` é o tipo/nome da criatura, `N$` é a arma, `X$` é a frase de efeito. O port atual
(`narrar_sala` em [aventureiro/src/combat.c:158](../../aventureiro/src/combat.c#L158)) omite a arma:

```c
log_msg(r, "Ha alguem aqui: %s. \"%s.\"", tripulante->nome, tripulante->frase);
```

O jogador do port não sabe com que arma vai apanhar antes de decidir atacar — o original dá essa
informação de cara. O dado já existe: `Tripulante::id_arma`
([types.h:68](../../aventureiro/src/types.h#L68)), já usado no contra-ataque em combat.c.

## Entregáveis

- Em `narrar_sala` (combat.c), buscar `bd->armas[tripulante->id_arma].nome` e incluir na mensagem,
  mantendo nome do tripulante e frase. Não precisa reproduzir literalmente o "TWIN REPORTA..." como
  linha separada — mensagem de log única já basta, mas o conteúdo (arma incluída) deve bater com o
  original.
- Comentário citando a linha 6270 do original, como já é convenção nas demais funções de combat.c.

## Critério de aceite

Ao entrar numa sala com tripulante vivo (ou usar o comando Examinar sobre ela), a mensagem de log
inclui o nome da arma do tripulante, não só nome e frase. Smoke test / sessão manual cobrindo esse
caso confirma a saída.

**Confirmado.** Verificado com `pexpect`+`pyte` dirigindo o binário (seed 1): ao entrar numa sala com
tripulante, o log mostrou `"Ha alguem aqui: Carregador Arbock, armado com Bumerangue. \"Pouco
amigável.\""` — nome, arma e frase juntos, igual ao original. `tests/smoke_test.py` (fuzzing de
crash) também rodou limpo depois da mudança.
