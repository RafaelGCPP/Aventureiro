# Pacote 32 — bug: fugir e ser seguido corta a revelação do tripulante na sala nova

**Tamanho:** S · **Depende de:** [Pacote 13](13-perseguicao-fiel.md), [Pacote 29](29-twin-reporta-uma-linha-so.md)

## Objetivo

Usuário reportou (via print de tela): ao fugir e o tripulante perseguir com sucesso ("Infelizmente o
Engenheiro de Bordo veio atrás de você."), a apresentação da sala nova parava na metade — o log
mostrava até `"TWIN reporta..."` e nunca mostrava quem era o tripulante, com qual arma, nem a frase
dele. Sem nenhuma mensagem de erro; simplesmente sumia.

Achado durante uma sessão que também cobria o [Pacote 30](30-hud-corrompe-terminal-estreito.md) — bug
não relacionado a terminal estreito, reproduziu igual num terminal largo (100 colunas). Causa: **o
mesmo `Resultado` acumula mensagens de comandos diferentes empilhados**, e o buffer tem tamanho fixo.
`log_msg` ([combat.c:97-106](../../aventureiro/src/combat.c#L97-L106)) descarta em silêncio qualquer
mensagem além de `MAX_MENSAGENS_RESULTADO` (`combat.h:8`, era **8**) — sem erro, sem aviso, só para de
escrever.

`comando_fugir` ([combat.c:657-730](../../aventureiro/src/combat.c#L657-L730)), quando o tripulante
segue com sucesso, empilha no mesmo `Resultado r`:

1. `"Você fugiu pelo lado %s."` ([combat.c:695](../../aventureiro/src/combat.c#L695))
2. `"Infelizmente o %s veio atrás de você."` ([combat.c:725](../../aventureiro/src/combat.c#L725))
3. `entrar_em_sala` ([combat.c:728](../../aventureiro/src/combat.c#L728)) → `"Você entrou numa nova
   sala."` ([combat.c:204](../../aventureiro/src/combat.c#L204)) + `narrar_sala` → `"Sala tipo: %s"`
   ([combat.c:148](../../aventureiro/src/combat.c#L148)) + `"Saídas: ..."` +, com tripulante na sala
   (e o que seguiu **sempre** está lá, é ele quem acabou de se mudar pra lá): `"Há alguém..."` +
   `"Há alguma coisa aqui..."` + `"TWIN reporta..."` + `"É um %s, armado com %s."`
   ([combat.c:184-190](../../aventureiro/src/combat.c#L184-L190)) + a frase do tripulante.

Total: **10 mensagens**, 2 acima do limite de 8. As duas últimas — exatamente a revelação (`"É um ...
armado com ..."`) e a frase — são sempre as cortadas, porque `log_msg` só descarta o que vem *depois*
do limite (não é um buffer circular).

`combate_seguir_tripulante_fugido` ([combat.c:354-373](../../aventureiro/src/combat.c#L354-L373)), a
perseguição por escolha do jogador (`"Quer segui-lo?"`), tem o mesmo problema: cada sala percorrida
soma uma mensagem `"Você o segue pelo lado %s."` ([combat.c:363](../../aventureiro/src/combat.c#L363))
antes do mesmo `entrar_em_sala`.

**Por que isso passou pelo Pacote 29:** aquele pacote existe justamente por causa de truncamento de
mensagem (outro limite, `MAX_TAMANHO_MENSAGEM`, bytes dentro de uma mensagem) e conferiu explicitamente
`MAX_MENSAGENS_RESULTADO` antes de fechar — mas só contou as mensagens de dentro de `narrar_sala`
("Sala tipo" + "Saídas" + 5 falas = 7, cabia com folga de 1). Não contou a mensagem de
`entrar_em_sala` que precede a chamada (`"Você entrou numa nova sala."`, o que já fecha em 8, exato),
nem — principalmente — que `entrar_em_sala` é chamada por `comando_fugir` e
`combate_seguir_tripulante_fugido` **depois** de outras mensagens já terem sido logadas no mesmo
`Resultado`. A conferência olhou a função isolada, não os chamadores.

## Entregáveis

- Aumentar `MAX_MENSAGENS_RESULTADO` (`combat.h`) para cobrir o pior caso real de `comando_fugir` (10,
  ou 11 com a mensagem de escudo desligado por energia) com folga, e uma perseguição
  (`combate_seguir_tripulante_fugido`) de algumas salas.
- Documentar no próprio `#define` (não só aqui) o motivo do número escolhido e a lista de mensagens que
  compõem o pior caso — pra próxima vez que alguém mexer em `narrar_sala`/`entrar_em_sala`/qualquer
  comando que os chame, o comentário já aponta que o orçamento precisa ser conferido de novo, contra
  os *chamadores*, não só a função isolada (é exatamente o que faltou no Pacote 29).
- Reproduzir manualmente (não dá pra caçar uma seed fixa barata aqui: depende de 3 sorteios em cadeia —
  sucesso ao fugir, sucesso da perseguição, direção de fuga — sem uma busca dedicada tipo a do Pacote
  29) e confirmar que a sequência completa aparece.

## Critério de aceite

Fugindo de um tripulante com sucesso e ele conseguindo seguir ("Infelizmente ... veio atrás de você."):
a sala nova narra completo, incluindo `"É um <tripulante>, armado com <arma>."` e a frase dele, sem
cortar. `tests/smoke_test.py` continua passando.

**Resolvido e confirmado.** `MAX_MENSAGENS_RESULTADO` (`combat.h:29`, após o Pacote 30 renumerar o
arquivo) subido de 8 para **16**, com comentário explicando o cálculo do pior caso de
`comando_fugir` (11) e o de `combate_seguir_tripulante_fugido` (cabe até ~7 salas seguidas; trilhas
mais longas que isso — o teto teórico é `MAX_TRILHA_FUGA=64` — ainda cortariam a revelação final,
aceito como resíduo raro num grid 8x8, e não pior que o limite de 8 original).

Reprodução: sessão real via pexpect (seed 1, sem controle de RNG do lado do teste — só navegação
aleatória até achar um tripulante e repetir Fugir até "Infelizmente..." aparecer). Log final
capturado, com o fix aplicado:

```
Você fugiu pelo lado Sul.
Infelizmente o Engenheiro de Bordo veio atrás de você.
Você entrou numa nova sala.
Sala tipo: Sala de Teleporte
Saídas: Norte
Há alguém...
Há alguma coisa aqui...
TWIN reporta...
É um Engenheiro de Bordo, armado com Espada Laser.
Esse não vai ser fácil
```

10 mensagens, todas presentes — antes do fix, as duas últimas não apareciam. `tests/smoke_test.py`
completo (painel, barra, fala do tripulante, resize, setas, HUD estreito, comando M, fuzz) rodado
depois da mudança, sem falha.

**Nota:** não foi adicionado um caso automatizado no `smoke_test.py` especificamente pra este cenário —
diferente do Pacote 29 (que achou uma seed fixa via BFS offline sobre o mapa gerado, uma busca
determinística porque dependia só da posição do tripulante), este bug depende de 3 sorteios de runtime
em sequência (sucesso ao fugir, sucesso da perseguição, direção sorteada), sem uma forma barata de
fixar isso numa seed. O fuzzer genérico já passa por este caminho ocasionalmente entre os comandos
aleatórios, mas só detecta crash, não conteúdo cortado — fica como lacuna conhecida, não coberta por
teste automatizado.
