# Pacote 31 — Mensagens e nomes abreviados em terminal estreito

**Tamanho:** M · **Depende de:** [Pacote 30](30-hud-corrompe-terminal-estreito.md) (usa o mesmo
limiar de largura definido lá)

## Objetivo

Desmembrado do [Pacote 30](30-hud-corrompe-terminal-estreito.md) durante o planejamento: o pacote 30
resolve a corrupção do HUD em si (layout de 3 linhas), mas o mesmo relatório original também notava
que narrações longas em `ui_log`/`Resultado::mensagens`, e nomes de arma/personagem/item exibidos no
HUD e no log, dependem do wrap automático do ncurses pra caber na tela - o mesmo mecanismo que causava
o embaralhamento do HUD pode se repetir em qualquer outra janela sem `scrollok` ou com texto mais
largo que o esperado, principalmente em 53 colunas (Termux).

Diferente do Pacote 30, aqui não há um bug reproduzido e confirmado - é preventivo, com superfície bem
maior: um levantamento rápido encontrou **~56 pontos** de `ui_log`/`Resultado::mensagens` espalhados
pelo código (não só em [ui.c](../../aventureiro/src/ui.c)), além dos nomes vindos da
`BaseDeDados` (armas, personagens, itens) que hoje só têm uma variante de texto. Por isso virou pacote
próprio em vez de entrar junto no 30.

## Entregáveis

- Mensagens curtas em modo estreito, incluindo nomes de personagens e objetos: sob o limiar definido
  no Pacote 30, narrações longas em `ui_log`/`Resultado::mensagens` e também nomes de arma/personagem/
  item exibidos no HUD e no log devem ter variante abreviada (mesma ideia das 3 variantes de
  `BARRA_COMPLETA`/`BARRA_ABREVIADA`/`BARRA_MINIMA` do Pacote 26) em vez de depender do wrap do
  ncurses.
- Levantar os ~56 pontos de chamada de `ui_log`/`Resultado::mensagens` e decidir, por grupo, a
  estratégia: truncar na hora de renderizar (mais simples, um único ponto de corte) vs. manter
  variantes curtas dedicadas por nome na `BaseDeDados` (mais fiel, mais trabalho de dados). Documentar
  a decisão aqui antes de implementar - é o principal risco de o pacote estourar orçamento.
- `tests/smoke_test.py`: cobrir pelo menos um caso de narração longa e um nome de arma/personagem
  longo em 53x29, confirmando que não há mais texto vazando de uma janela pra outra.

## Critério de aceite

Em terminal mais estreito que o limiar do Pacote 30 (especialmente 53x29, Termux), nenhuma mensagem de
log nem nome de arma/personagem/item quebra no meio da palavra ou vaza pra fora da janela onde foi
impresso. Terminais com largura confortável continuam mostrando o texto completo, sem abreviação.
`tests/smoke_test.py` continua passando, incluindo os novos casos.

---

## Pivô de implementação (registrado antes de codar, não só depois)

Investigando por que `ui_log` embaralhava texto em 53 cols (achado no fechamento do Pacote 30: a linha
de título "...tripulação poderá ter as mais" cortava virando "oderá ter as mais" numa linha separada),
a causa raiz **não é a mesma** do HUD: `janela_log` **tem** `scrollok` (ao contrário do `janela_hud`
do Pacote 30), então o wrap por coluna do ncurses não sobrepõe janelas nem corrompe nada - só quebra
palavras no meio dentro da própria janela (feio, mas não é a mesma classe de bug). Isso muda a resposta
certa pros dois entregáveis originais:

- **Abreviar as ~56 chamadas de `ui_log`/`Resultado::mensagens` uma por uma** (o plano original) seria
  escrever dezenas de variantes curtas de texto narrativo à mão - trabalho de redação, não de
  engenharia, e ainda assim específico a cada string (frágil a qualquer texto novo). Em vez disso,
  `ui_log` (`ui.c`) agora quebra **entre palavras** (nunca no meio de uma) na largura real da janela a
  cada chamada - resolve as ~56 chamadas de uma vez, sem tocar no conteúdo de nenhuma, e também cobre
  nomes de arma/personagem/item citados dentro de uma frase (uma "palavra" só quebra sozinha se for
  maior que a própria janela, o que não acontece com nome nenhum hoje).
- **Nomes no HUD** (não passam por `ui_log`, são `mvwprintw` direto numa janela sem `scrollok`, igual
  ao HUD do Pacote 30) continuam de fato arriscados: um nome de arma futuro mais longo que o campo
  (`MAX_NOME` permite até 47 colunas; o mais longo hoje tem 16) reproduziria a mesma corrupção do
  Pacote 30. Resolvido com truncagem defensiva (`truncar_visivel_utf8`, `ui.c`) - corta com "…" no
  limite de cada variante de layout, UTF-8-aware. Nomes reais de hoje nunca são afetados.
- **Rótulos abreviados nas 3 variantes de barra (`BARRA_COMPLETA`/`ABREVIADA`/`MINIMA`)** eram o modelo
  mental original, mas esse padrão serve pra um punhado de strings FIXAS conhecidas de antemão - não
  escala pra texto narrativo livre. Descartado a favor da quebra por palavra.

**Achado no meio do caminho, fora do escopo original:** testando a quebra por palavra em terminal
estreito, apareceu uma corrupção diferente - texto de uma escrita bem anterior (de outro parágrafo)
vazando pra dentro de uma linha nova mais curta, só depois que `janela_log` rola (mais texto que a
altura disponível). Isolado reproduzindo os bytes brutos que o ncurses manda direto num `pyte.Stream`
(sem pty nem terminal real envolvido, só pra confirmar que não era artefato do jeito que o teste lê a
saída): o ncurses otimiza a saída assumindo que a linha recém-exposta pelo scroll já está em branco e
não reenvia o clear-to-EOL - só que a "janela virtual" que ele acha estar la' as vezes não bate com o
que o terminal (ou o `pyte` do teste) realmente tem. `redrawwin()` antes de cada `wrefresh(janela_log)`
força reescrita completa e contorna isso (ver comentário em `ui_log`, `ui.c`).

**Segundo achado, relatado pelo usuário jogando de verdade:** em exatamente 53 colunas (Termux),
examinar uma sala iluminada pulava uma linha em branco entre as duas metades da mensagem quebrada
("...para examinar a sala" / "sem usar a lanterna."). Causa: o primeiro pedaço dessa mensagem quebra
com **exatamente** 53 caracteres visíveis - preenche a janela ponta a ponta. Quando isso acontece, o
próprio ncurses já deixa o cursor pendente de quebra automática (assim que o próximo caractere for
escrito) - o `waddch(win, '\n')` que `escrever_com_quebra_de_palavra` mandava incondicionalmente
duplicava esse avanço. Corrigido: só emite o `\n` explícito quando a linha escrita é **menor** que a
largura da janela (`escrever_linha_com_avanco`, `ui.c`) - se preencheu exatamente, deixa o próprio
ncurses cuidar do avanço na próxima escrita.

## Entregado

- `escrever_com_quebra_de_palavra` (`ui.c`), usada por `ui_log`: quebra em palavras inteiras na largura
  atual de `janela_log`, sem tocar no texto de nenhuma das ~56 chamadas existentes.
- `redrawwin(janela_log)` antes do `wrefresh` em `ui_log`, corrigindo o vazamento de texto de escritas
  anteriores ao rolar (achado durante este pacote, não relacionado ao Pacote 30).
- `truncar_visivel_utf8` (`ui.c`) aplicada ao nome de arma do HUD nas duas variantes de layout (20
  colunas em ESTREITA, 24 em LARGA, casando com o padding existente) - defesa contra nome futuro maior
  que os 16 reais de hoje.
- `escrever_linha_com_avanco` (`ui.c`), usada por `escrever_com_quebra_de_palavra`: só emite `\n`
  explícito quando a linha escrita não preencheu a janela inteira, evitando o duplo avanço/linha em
  branco extra descrito acima.
- `tests/smoke_test.py`: `verificar_log_nao_quebra_palavra_nem_deixa_lixo` (tela de título em 30 e 53
  cols, contra o wrap correto calculado à mão), `verificar_nome_arma_longo_trunca_sem_corromper`
  (data-dir temporário com a arma inicial renomeada bem mais longa que `MAX_NOME` real de hoje, em 30 e
  53 cols) e `verificar_quebra_linha_nao_pula_linha_extra` (examinar a Sala de Teleporte, que nunca é
  escura, em 53 cols - determinístico, não depende de sorte) - todos rodados múltiplas vezes seguidas
  sem falha, mais o restante da suíte (painel, barra, fala do tripulante, resize, setas, HUD estreito,
  comando M, fuzz).
