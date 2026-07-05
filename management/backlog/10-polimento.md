# Pacote 10 — Polimento e rastreabilidade

**Tamanho:** S · **Depende de:** [Pacote 9](09-main-testes.md)

## Objetivo

Passar por todos os módulos garantindo que a filosofia da seção 5 do
[handover](../../handover_aventureiro_c.md) foi cumprida: comentários "porquê" + referência de linha
do BASIC original em toda decisão de design não óbvia, nada hardcoded que devia estar em `config.json`.

## Entregáveis

- Revisão módulo a módulo comparando com as seções 2-4 do handover.
- Atualização do `README.md` do repo com instruções de build/play.
- Resolução final, com nota explícita no código, das perguntas da seção 7 do handover que ainda
  estejam como "default" do [Pacote 0](00-scaffold.md) (confirmar se o usuário quer mudar algum
  antes de considerar o projeto pronto).

## Critério de aceite

Revisão manual linha a linha não encontra `#define` de balanceamento nem constante mágica sem
comentário de origem.
