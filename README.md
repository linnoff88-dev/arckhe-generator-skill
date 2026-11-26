# generator-skill

<div align="center">
  <img src="capa/logo.svg" alt="Arckhé AI" height="48" />
  <p><strong>Arckhé AI</strong></p>
</div>


**ê°Å¸â¡Â§ê°Å¸â¡Â· [PortuguêÂªs](README.md) êÂ· ê°Å¸â¡Âºê°Å¸â¡Â¸ [English](README.en.md) êÂ· ê°Å¸â¡Âªê°Å¸â¡Â¸ [EspaêÂ±ol](README.es.md)**

![Uma skill, todo o fluxo: /generate roteia para o modelo mais econêÂ´mico, usa referêÂªncias reais, gera imagem ou vêÂ­deo, salva tudo numa pasta êÂºnica e registra prompt, modelo e parêÂ¢metros](assets/hero-fluxo.png)

## ê°Å¸ââ Guia de uso

Guia completo (landing + passo a passo): **https://linnoff88-dev.github.io/

Duas versêÂµes da skill `/generate` ê¢â¬â um comando para o agente gerar imagens e
vêÂ­deos sob demanda, escolher a rota de menor custo, controlar gasto e salvar
tudo num lugar sêÂ³.

SêÂ£o duas porque a decisêÂ£o mais importante da skill ê¢â¬â **qual êÂ© a rota mais
barata** ê¢â¬â tem respostas opostas dependendo da mêÂ¡quina.

## Qual usar

| | [`generate-local/`](generate-local/) | [`generate-api/`](generate-api/) |
|---|---|---|
| **Para** | mêÂ¡quina com modelo rodando nela | qualquer mêÂ¡quina, tudo via API |
| **Rota mais barata** | local, **$0** (flux2-klein na GPU) | modelo barato pago, ~$0,02 |
| **Modelo de cobranêÂ§a** | hardware jêÂ¡ pago, custo marginal zero | pay-as-you-go por chamada |
| **Rota paga** | exceêÂ§êÂ£o ê¢â¬â duas razêÂµes sêÂ³ | êÂ© o êÂºnico caminho |
| **Imagem padrêÂ£o** | flux2-klein local, ~6 s | modelo barato de imagem |
| **Imagem com texto legêÂ­vel** | fal.ai, pago (local erra letra) | modelo top, pago |
| **VêÂ­deo padrêÂ£o** | render 2.5D local (`pixflow-motion`), $0 | modelo generativo, $0,20ê¢â¬â0,35/s |
| **PortêÂ£o de custo** | existe, quase nunca dispara | dispara em toda execuêÂ§êÂ£o |
| **"Rascunhe barato, finalize caro"** | sem sentido ê¢â¬â êÂ© o mesmo modelo | êÂ© a regra que mais economiza |
| **Livro-caixa** | existe, quase sempre zerado | êÂ© o coraêÂ§êÂ£o da skill |
| **Depende de** | servidor `ARCKHêimg` no ar (porta 8000) | `FAL_KEY` + `KIE_API_KEY` num `.env` |
| **Teto de aviso** | $20/mêÂªs | configurêÂ¡vel, padrêÂ£o $50/mêÂªs |

O que **nêÂ£o** muda entre as duas, porque êÂ© o que faz o sistema durar: uma receita
Markdown por modelo, biblioteca plana, log JSON ao lado de cada arquivo,
referêÂªncia real em vez de descrita, e as regras dentro do `SKILL.md` ê¢â¬â que o
agente relêÂª a cada uso.

## Os cinco passos

O fluxo êÂ© o mesmo nas duas versêÂµes; muda sêÂ³ para onde o passo 1 aponta.

1. **Roteia para o modelo mais econêÂ´mico** que dêÂ¡ conta da tarefa, e lêÂª a receita
   daquele modelo antes de chamar.
2. **Usa referêÂªncias reais** ê¢â¬â logotipos, rostos, estilo ê¢â¬â carregadas de `refs/`.
   Descrever um logo em palavras devolve logo errado toda vez.
3. **Gera** imagem ou vêÂ­deo. Se o modelo for assêÂ­ncrono, consulta o status e
   baixa na hora, porque URL de resultado expira.
4. **Salva tudo numa pasta êÂºnica e plana**, sem subpastas, com nome previsêÂ­vel.
5. **Registra prompt, modelo, parêÂ¢metros e custo** num JSON ao lado do arquivo.

## Por que agregador, e por que pay-as-you-go

![Modelos criativos (nano banana, kling, veo, seedance, gpt image) alcanêÂ§êÂ¡veis por duas rotas: um agregador por assinatura mensal, ou agregadores pay-as-you-go como Fal AI, Wavespeed AI e Kie AI](assets/rotas-assinatura-vs-payg.png)

Os mesmos modelos sêÂ£o alcanêÂ§êÂ¡veis por caminhos com contas muito diferentes.
Plataformas de **assinatura** cobram um valor fixo por mêÂªs ê¢â¬â previsêÂ­vel, e boa
quando o uso êÂ© alto e constante. Agregadores **pay-as-you-go** (fal.ai, Kie AI)
cobram por chamada, com uma chave e uma fatura sêÂ³ para dezenas de modelos.

A versêÂ£o `generate-api` assume pay-as-you-go, por trêÂªs razêÂµes: comeêÂ§a em zero,
o custo êÂ© atribuêÂ­vel a cada geraêÂ§êÂ£o (êÂ© o que alimenta o livro-caixa), e nêÂ£o se
paga mensalidade em mêÂªs parado. Se o volume subir a ponto de a assinatura sair
mais barata, o cêÂ¡lculo êÂ© simples ê¢â¬â some o mêÂªs no `--saldo` e compare.

Na versêÂ£o `generate-local` a discussêÂ£o nêÂ£o existe: a GPU jêÂ¡ estêÂ¡ paga, e a rota
paga sêÂ³ entra nas duas exceêÂ§êÂµes documentadas.

## Instalar

```bash
cp -r generate-local ~/.claude/skills/generate               # mêÂ¡quina com GPU
cp -r generate-api  <workspace>/.claude/skills/generate      # mêÂ¡quina sem modelo local
```

As duas declaram `name: generate`. Instale **uma** por workspace, ou renomeie.

## DocumentaêÂ§êÂ£o

- [`generate-local/README.md`](generate-local/README.md) e
  [`generate-api/README.md`](generate-api/README.md) ê¢â¬â cada versêÂ£o em detalhe.
- [`doc/guia-skill-generate-ptbr.md`](doc/guia-skill-generate-ptbr.md) ê¢â¬â guia
  têÂ©cnico traduzido: autenticaêÂ§êÂ£o por provider, padrêÂ£o assêÂ­ncrono, tabela de
  modelos, faixas de custo.
- [`doc/analise-generate-skill-guide.md`](doc/analise-generate-skill-guide.md) ê¢â¬â
  anêÂ¡lise do material original: o que ele acerta e o que faltava (orêÂ§amento
  acumulado, preêÂ§o versionado, retomada de job assêÂ­ncrono ê¢â¬â os trêÂªs estêÂ£o
  implementados aqui).

## Aviso que vale para as duas

IDs de modelo, preêÂ§os e endpoints mudam com frequêÂªncia. Tudo que nêÂ£o foi
confirmado por uma chamada real estêÂ¡ marcado **NêÆO VERIFICADO** nas receitas.
Confirme na documentaêÂ§êÂ£o atual do provider antes de usar para valer.

---

<div align="center">
  <img src="capa/logo.svg" alt="Arckhé AI" height="32" />
  <p><strong>Arckhé AI</strong></p>
</div>