# Pacote 20 — pausas dramáticas (examinar sala / apresentar tripulante)

**Tamanho:** M · **Depende de:** [Pacote 6a](06a-combat-basico.md), [Pacote 15](15-narrar-arma-tripulante.md)

## Objetivo

O usuário lembra de pausas de ~1s no original em dois pontos: durante o comando Examinar (entre a
mensagem de luz e "Você encontrou...", e de novo antes de revelar o quê) e ao entrar numa sala com
tripulante (entre "Há alguém aqui" e a revelação de quem é/com que arma). Fui checar o
`aventureiro.p.bas` pra separar o que é confirmável na fonte do que é reconstrução por sensação:

- **Confirmado no original**: linhas 5160-5200. `5160 PRINT ... "EXAMINANDO A SALA..."` é seguido
  por `5170 FOR I=U TO O` / `5180 NEXT I` — um loop vazio de 1 a 8 (`U=1`, `O=8`, linhas 26-28) só
  pra passar tempo — antes de `5200 PRINT ,,"VOCE ENCONTROU... "`. É um delay deliberado, sem
  nenhum outro efeito. No ZX81 (Z80 a 3.25MHz, BASIC interpretado, modo SLOW com tela redesenhada
  via software) 8 iterações vazias plausivelmente já dava a sensação de ~1s.
- **Não confirmado como delay explícito**: nem a pausa entre "Você encontrou..." e a revelação
  (`5200`→`5250`/`5410` é sequencial, sem FOR/NEXT no meio), nem a pausa entre "HA ALGUEM," (`6150`)
  e "TWIN REPORTA..." (`6270`) — aí o que existe são várias chamadas `RAND USR` (rotina em código de
  máquina, decodificando nome/arma/frase da memória) que naturalmente eram lentas em hardware real,
  mas não é um `FOR/NEXT` proposital. Ou seja: a sensação de pausa aí é real e bem lembrada, só não
  é uma pausa "de propósito" no sentido de código — é o tempo de processamento genuíno do ZX81.

Dado que o pedido é recriar a sensação de jogar o original (não simular ciclos de CPU), a proposta é
tratar as três pausas como equivalentes na prática — todas ~1s — mas deixar registrado que só a
primeira tem lastro direto no código-fonte; as outras duas são reconstrução de fidelidade à
experiência, não ao BASIC literal.

## Entregáveis

- **Mecanismo de pausa no buffer de mensagens** (`combat.h`/`types.h`): hoje `Resultado` é só um
  buffer de strings (`mensagens[MAX_MENSAGENS_RESULTADO]`) que `narrar()` em
  [game.c:58-62](../../aventureiro/src/game.c#L58-L62) despeja de uma vez, sem timing entre linhas.
  Adicionar um array paralelo (ex. `bool pausa_apos[MAX_MENSAGENS_RESULTADO]`) marcando quais
  mensagens devem ser seguidas de uma pausa antes da próxima. Um helper em `combat.c` tipo
  `marcar_pausa(Resultado *r)` seta o flag no último índice preenchido, chamado logo depois do
  `log_msg(...)` que deve pausar.
- **Aplicar a pausa em `narrar()`** (`game.c`): depois de `ui_log("%s", res->mensagens[i])`, se
  `res->pausa_apos[i]`, chamar uma nova função de UI (ex. `ui_pausar_dramatico()` em `ui.c`,
  provavelmente só um `napms(1000)` do ncurses — a janela de log já foi refrescada pelo `ui_log`
  anterior, então a mensagem fica visível durante a espera).
- **Pontos de uso**:
  - `comando_examinar_sala` (`combat.c`): marcar pausa depois da mensagem de luz/escuro (linha
    ~462/471) e antes de `"Voce encontrou..."` — este é o ponto com lastro direto na fonte.
  - Mesma função: marcar pausa depois de `"Voce encontrou..."`, antes da revelação (Nada/item) —
    reconstrução, não fidelidade literal (ver acima).
  - `narrar_sala` (`combat.c`, tripulante): hoje o Pacote 15 deixou tudo numa mensagem só
    (`"Ha alguem aqui: %s, armado com %s. ..."`). Separar de volta em duas mensagens — primeira só
    "Ha alguem aqui." (equivalente à linha 6150 isolada), pausa, depois a revelação completa (nome +
    arma + frase, equivalente à linha 6270) — isso também aproxima mais da estrutura real do
    original (dois PRINTs distintos, não um só).
- **Não deve afetar** `tests/smoke_test.py`: o fuzzer manda ~200 comandos aleatórios com timeout de
  5s por sessão; se cada Examinar/entrada-em-sala-com-tripulante passar a levar +1-2s reais, o
  orçamento de tempo do fuzzer explode. Adicionar uma forma de desligar as pausas em modo de teste
  (ex. variável de ambiente tipo `AVENTUREIRO_SEM_PAUSAS=1` checada em `ui_pausar_dramatico()`, ou
  flag `--sem-pausas` em `main.c` reaproveitando o padrão do `--seed`) e usar isso em
  `tests/smoke_test.py`.

## Critério de aceite

Jogando manualmente: Examinar sala mostra a mensagem de luz, pausa perceptível (~1s), "Você
encontrou...", pausa (~1s), revelação. Entrar em sala com tripulante mostra "Há alguém aqui.", pausa
(~1s), depois nome/arma/frase. `tests/smoke_test.py` roda no modo sem pausas e continua passando sem
estourar o timeout.
