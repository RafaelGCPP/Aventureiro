# Pacote 6b — `combat.h/.c`: combate

**Tamanho:** M · **Depende de:** [Pacote 6a](06a-combat-basico.md)

## Objetivo

Segunda metade dos 10 comandos da seção 2.2 do [handover](../../handover_aventureiro_c.md): os que
envolvem troca de dano ou interação social com o tripulante. Mesmo estilo do 6a — lógica pura,
sem I/O de terminal, mensagens via buffer/callback.

## Entregáveis

Uma função por comando, cada uma comentada com a referência de linha do BASIC original:

- **Atacar** (linha 1505) — combate contra o tripulante da sala.
- **Fugir** (linha 2010) — foge para uma sala vizinha aleatória (versão simplificada, decisão do
  [Pacote 0](00-scaffold.md): sem perseguição multi-sala).
- **Comunicar-se** (linha 3010) — Subornar / Irritar / ser Amigável com o tripulante.

Sub-rotinas de apoio:
- **Contra-ataque do inimigo** (linha 6505) — o tripulante revida depois de um ataque que não o mata;
  pode matar o jogador.
- **Inimigo foge** (linha 6800) — tripulante não-agressivo pode entrar em pânico e fugir para outra
  sala (versão simplificada).

Regras de morte/vitória da seção 2.4 do handover devem estar refletidas aqui: vida (`BP`) chegando a
zero = jogador perde tudo.

## Critério de aceite

Smoke test simula combate completo (atacar até matar ou morrer, fugir, comunicar-se com sucesso e
fracasso) e o log resultante é plausível: vida muda nas direções certas, morte zera o jogador,
contra-ataque só acontece se o tripulante sobreviver ao ataque do jogador.
