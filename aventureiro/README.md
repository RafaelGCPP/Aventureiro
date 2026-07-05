# aventureiro (build em progresso)

Remake de "O Aventureiro" (ZX81 BASIC) em C + ncurses. Ver
[../handover_aventureiro_c.md](../handover_aventureiro_c.md) para o contexto completo da engenharia
reversa e [../management/README.md](../management/README.md) para o backlog de construção.

## Decisões travadas (Pacote 0)

Resolvidas para destravar a implementação sem esperar validação — revisáveis no
[Pacote 10](../management/backlog/10-polimento.md), e nesse meio tempo todas ajustáveis via
`data/config.json` sem recompilar:

- **Grid**: 8x8, igual ao original.
- **Fórmulas de dano/cura/loot**: valores razoáveis livres; não se tentou recuperar as constantes
  ofuscadas do BASIC original.
- **Fuga/perseguição de inimigos**: versão simplificada — sala vizinha aleatória, sem perseguição
  multi-sala como nas linhas 6800-6920 do original.
- **Mapa visual**: nenhum ASCII map — 100% textual, como o original.
- **Save/load**: não implementado nesta fase — uma partida = uma sessão contínua.

## Estrutura

```
aventureiro/
  data/     -> config.json, rooms.json, weapons.json, crew.json (Pacote 1)
  src/      -> código C + cJSON vendorizado (Pacotes 2+)
  Makefile
```

## Build

```
make        # compila o que existir até o momento
make clean
make test   # smoke tests automatizados, a partir do Pacote 9
```
