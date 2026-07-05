# Pacote 1 — Dados de jogo em JSON

**Tamanho:** S · **Depende de:** — · Ver [handover_aventureiro_c.md](../../handover_aventureiro_c.md) seção 4

## Objetivo

Transcrever as tabelas da seção 4 do handover para os arquivos JSON definitivos. Sem C ainda — só dados.

## Entregáveis

- `data/rooms.json` — 12 tipos de sala (seção 4.1), incluindo a Sala de Teleporte marcada como especial.
- `data/weapons.json` — 10 armas (seção 4.2): nome, dano_max, chance_acerto, custo_energia.
- `data/crew.json` — 20 tripulantes (seção 4.3): nome, frase, vida, agressivo, id_arma.
- `data/config.json` — grid_size=8, vida/energia iniciais do jogador, chances de eventos (tripulante
  na sala, item na sala, acidente no escuro, portas extras no labirinto), todas com valores placeholder
  razoáveis e comentário (via chave `"_comment"`, já que JSON não tem comentário nativo) explicando
  o que cada uma controla.

## Critério de aceite

Todos os arquivos são JSON válido (`jq . data/*.json` sem erro). Contagens batem (12 salas, 10 armas,
20 tripulantes) e `id_arma` de cada tripulante referencia um índice válido de arma.
