# Pacote 6a — `combat.h/.c`: comandos básicos (sem combate)

**Tamanho:** M · **Depende de:** [Pacote 4](04-map.md), [Pacote 5](05-player.md)

## Objetivo

Primeira metade dos 10 comandos da seção 2.2 do [handover](../../handover_aventureiro_c.md): os que
não envolvem troca de dano. Lógica pura de jogo (ainda sem I/O de terminal — recebem input já
parseado, devolvem resultado/mensagens para um buffer, não para stdout).

Este pacote foi separado do 6b porque combat é o módulo de maior risco de estourar o orçamento de
uma sessão — ver nota em [management/README.md](../README.md).

## Entregáveis

Uma função por comando, cada uma comentada com a referência de linha do BASIC original (ex:
`/* equivalente às linhas 1004 do original */`), conforme pedido na seção 1 do handover:

- **Mover** (linha 1004) — anda N/S/L/O entre salas conectadas.
- **Trocar de arma** (linha 2502) — escolhe entre as armas já obtidas.
- **Escudo** (linha 3501) — liga/desliga o escudo (reduz dano, consome energia).
- **Usar medicamento** (linha 4010) — recupera vida, se tiver medicamento e não estiver com vida cheia.
- **Situação** (linha 4510) — mostra vida, energia, dinheiro, armas.
- **Examinar a sala** (linha 5001) — procura itens; em salas escuras, arrisca acidente.
- **Acionar teleporte** (linha 5510) — só funciona na Sala de Teleporte — termina a partida com
  sucesso, mantendo o que foi coletado.

Sub-rotinas de apoio usadas por estes comandos:
- **Entrar em sala nova** (linha 6002) — descreve a sala, sorteia se há tripulante e o que ele carrega.
- **Acidente no escuro** (linha 7000) — chance de se machucar ao examinar uma sala escura sem lanterna.
- **Imprimir direção** (linha 6400) — converte código numérico de direção em texto (Norte/Sul/Leste/Oeste).

Mensagens de resultado escritas num buffer/callback (`ui_log`-like) para desacoplar de ncurses —
nesta fase o "log" pode ser só um `printf` de teste.

## Critério de aceite

Smoke test simula uma sequência fixa de comandos (script de teste, não interativo) cobrindo mover,
examinar, trocar de arma, escudo, medicamento, situação e teleporte. O log resultante é plausível
(energia muda ao ligar escudo, teleporte só funciona na sala certa, medicamento só cura se tiver
o item).
