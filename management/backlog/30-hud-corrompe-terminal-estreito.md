# Pacote 30 — HUD/mensagens em terminal estreito (agora prioritário: Termux é 53×29)

**Tamanho:** M · **Depende de:** [Pacote 7](07-ui.md), [Pacote 26](26-barra-comandos.md)

## Objetivo

Achado incidental durante a verificação do Pacote 28 (redimensionamento): ao testar o jogo num
terminal bem estreito (30 colunas), o HUD (`janela_hud`, [ui.c](../../aventureiro/src/ui.c)) sai
corrompido — texto de uma linha vaza pra dentro da próxima, embaralhando com a moldura e com o log
logo abaixo:

```
┌────────────────────────────┐
│ Vida: 20    Energia: 200   D
inArma: Pistola Laser
    Escudo: desligado  Medicam
```

Confirmado que **não é causado pelo Pacote 28**: reproduz igual lançando o jogo direto em 30x24 (sem
nenhum resize envolvido) — testado num worktree limpo antes de qualquer mudança de resize. Causa:
`janela_hud` é criada em `ui_iniciar()`/`recriar_janelas()` sem nenhuma checagem de largura mínima
(diferente do painel de mapa e da barra de comandos, que têm `cabe_painel`/`cabe_barra` e viram no-op
limpo em terminal pequeno demais, Pacotes 17/26). O conteúdo impresso (`ui_desenhar_hud`,
`mvwprintw(janela_hud, 2, 2, "Arma: %-24s  Escudo: %s  Medicamentos: %d", ...)`) tem até 66 colunas de
texto - numa janela mais estreita que isso (sem `scrollok`), o ncurses quebra o texto que não coube
pra linha de baixo **dentro da mesma janela de 4 linhas**, sobrepondo o HUD com ele mesmo.

### Por que isso subiu de prioridade

Isso era tratado como bug de canto (30 colunas é um caso extremo). Mas o alvo real de uso via SSH é o
Termux no celular, e o Termux roda **53 colunas x 29 linhas** por padrão nesse aparelho — bem dentro da
faixa afetada. Print de confirmação (jogo rodando ao vivo em 53 cols): a segunda linha do HUD já
estoura e quebra sozinha (`Escudo: deslig` cortado no meio da palavra, `Medicamentos: 5` empurrado pra
uma terceira linha sem moldura), e a barra de comandos já caiu pra `BARRA_MINIMA`
(`0123456789 (H=ajuda)`, ver `escolher_texto_barra`) - ou seja, a 53 cols o jogo já está na pior
variante de tudo que existe hoje, e mesmo assim ainda corrompe.

Ou seja: não é mais um edge case a tolerar, é a largura de tela do uso principal em campo.

## Entregáveis

- **Layout de HUD em 3 linhas para terminal estreito**, no mesmo espírito tiered de
  `escolher_texto_barra` (Pacote 26) e `cabe_painel` (Pacote 17): abaixo de um limiar de largura (a
  decidir exatamente - candidatos 55 ou 60 colunas, considerando que Termux = 53), trocar as 2 linhas
  atuais (`Vida/Energia/Dinheiro` + `Arma/Escudo/Medicamentos`) por 3 linhas mais curtas que cabem sem
  quebra automática do ncurses, ajustando `ALTURA_HUD` dinamicamente (hoje fixo em 4, incluindo
  bordas) e recalculando `altura_disponivel` pro log/mapa em `recriar_janelas`.
- **Mensagens curtas em modo estreito, incluindo nomes de personagens e objetos**: sob o mesmo
  limiar, narrações longas em `ui_log`/`Resultado::mensagens` e também nomes de arma/personagem/item
  exibidos no HUD e no log devem ter variante abreviada (mesma ideia das 3 variantes de
  `BARRA_COMPLETA`/`BARRA_ABREVIADA`/`BARRA_MINIMA`) em vez de depender do wrap do ncurses, que já
  causa embaralhamento no HUD e pode fazer o mesmo em qualquer outra janela.
- **Reintroduzir um comando de mapa em tela cheia** (o antigo pseudo-comando `m`/`M` do Pacote 14,
  removido no Pacote 17 quando o mapa virou painel lateral sempre visível - ver nota em
  [ui.h:55](../../aventureiro/src/ui.h#L55)). Motivo: em terminal estreito o painel lateral
  (`janela_mapa`) frequentemente nem cabe (`cabe_painel` falso quando `COLS - largura_painel <
  LARGURA_MINIMA_LOG`, e `largura_painel` cresce com o tamanho do labirinto explorado) - sem painel e
  sem comando, o jogador fica sem nenhuma forma de ver o mapa. Reativar `M` como fallback quando
  `cabe_painel` for falso (não precisa ser exclusivo do modo estreito por largura de coluna - é o
  mesmo sintoma).
- Definir e documentar o limiar exato de colunas que ativa o "modo estreito" (HUD 3 linhas + mensagens
  curtas) - decidir entre 55/60 na sessão, testando com o tamanho real do Termux (53x29).
- Aplicar a mesma decisão em `recriar_janelas` (Pacote 28) pra que isso também não regrida num
  resize que encolha abaixo do limiar.
- `tests/smoke_test.py`: verificação num terminal estreito (30 cols, o caso original) e em 53x29
  (o caso real do Termux) confirmando que o HUD não sobrepõe suas próprias linhas em nenhum dos dois.

### Sidetrack: símbolos do mapa (já que o comando `M` volta a ser mexido aqui)

Fora do problema principal, mas como o grid ASCII (`ui_desenhar_mapa`,
[ui.c:404-453](../../aventureiro/src/ui.c#L404)) vai ser tocado de qualquer forma:

- Trocar o `.` (ponto na linha de base) usado hoje pra sala visitada por um ponto centralizado
  verticalmente - candidato `·` (U+00B7 MIDDLE DOT), glifo padrão pra isso, com bom suporte em fontes
  monoespaçadas de terminal (inclusive Termux).
- Marcar com um `x` centralizado as salas cujo item já foi coletado. O campo `item_coletado`
  ([types.h:105](../../aventureiro/src/types.h#L105)) já existe mas hoje não é usado no desenho do
  mapa. Candidato `×` (U+00D7 MULTIPLICATION SIGN, desenhado centralizado no quadrado do caractere,
  diferente da letra latina "x"); alternativa `✕` (U+2715) se o `×` não ficar visualmente distinto o
  bastante do "você" (`@`) na legenda. Testar a renderização no Termux antes de bater o martelo -
  fontes de terminal variam no suporte a esses glifos.
- Atualizar a legenda (`ui.c:450-452`) com o novo símbolo.

Isso é cosmético e não bloqueia o critério de aceite abaixo - só entra se sobrar tempo na sessão.

## Critério de aceite

Lançando o jogo (ou redimensionando pra) um terminal mais estreito que o limiar decidido: o HUD muda
pro layout de 3 linhas sem corromper/sobrepor texto, as mensagens de log (e nomes de arma/item/
personagem) saem em versão curta, e o mapa continua acessível via comando `M` quando o painel lateral
não couber. Especificamente em 53x29 (Termux) o HUD fica legível e completo, sem truncar palavras no
meio. Terminais com largura confortável (painel de mapa cabendo) continuam iguais a hoje, sem `M`
sendo necessário.
`tests/smoke_test.py` continua passando, incluindo os novos casos (30 cols e 53x29).
