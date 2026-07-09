# Pacote 17 — mapa contínuo no canto da tela

**Tamanho:** M · **Depende de:** [Pacote 14](14-mapa-ascii.md)

## Objetivo

Hoje (Pacote 14) o mapa é um overlay: apertar `M` limpa o log e desenha o grid por cima, esperando
uma tecla pra voltar ao jogo (`mostrar_mapa()` em [game.c:171](../../aventureiro/src/game.c#L171),
`ui_limpar_log()` + `ui_desenhar_mapa()` + `ui_aguardar_tecla()`). Sugestão do usuário: manter o mapa
sempre visível, sendo atualizado ao vivo num canto da tela, sem precisar pedir com `M`.

## Entregáveis

- **Layout**: reservar uma região fixa da tela (ex. canto direito) pra uma nova `WINDOW*` de mapa em
  `ui.c`, ao lado das janelas de HUD/log já existentes (`janela_hud`, `janela_log` em
  [ui.c:16-17](../../aventureiro/src/ui.c#L16-L17)). Definir tamanho mínimo de terminal necessário
  (o grid pode não caber em terminais estreitos — decidir o que fazer: esconder o painel, avisar, ou
  exigir tamanho mínimo como já deve existir pro HUD/log).
- **Redesenho incremental**: chamar o desenho do mapa (reaproveitar a lógica de
  `ui_desenhar_mapa`/Pacote 14) toda vez que `visitada` mudar — ou seja, nos mesmos pontos que hoje
  marcam `Celula.visitada = true` (`entrar_em_sala` em `combat.c`, nascimento na Sala de Teleporte em
  `game.c:182`) — sem esperar o jogador apertar nada.
- **Decidir o destino do comando `M`**: remover (já que o mapa está sempre visível) ou manter como
  atalho pra dar zoom/full-screen no mapa. Definir com o usuário se ficar em dúvida; default sugerido:
  remover `M` e simplificar `ui_ler_comando()`/a ajuda (`mostrar_ajuda()` em
  [game.c:150-164](../../aventureiro/src/game.c#L150-L164)) de volta pra só `H`.
- Continua sem revelar tipo/conteúdo de salas não visitadas — mesma regra de fidelidade do Pacote 14.

## Critério de aceite

Jogando manualmente: o painel de mapa aparece sempre, atualiza sozinho ao entrar numa sala nova
(sem apertar `M`), e HUD/log continuam funcionando normalmente ao lado. Terminal pequeno demais tem
comportamento definido (não corrompe a tela). `tests/smoke_test.py` (pexpect+pyte) atualizado pra
cobrir o painel sempre visível.
