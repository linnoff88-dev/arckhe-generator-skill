# generator-skill

<div align="center">
  <img src="capa/logo.svg" alt="Arckhé AI" height="48" />
  <p><strong>Arckhé AI</strong></p>
</div>


**ðŸ‡§ðŸ‡· [PortuguÃªs](README.md) Â· ðŸ‡ºðŸ‡¸ [English](README.en.md) Â· ðŸ‡ªðŸ‡¸ [EspaÃ±ol](README.es.md)**

![Uma skill, todo o fluxo: /generate roteia para o modelo mais econÃ´mico, usa referÃªncias reais, gera imagem ou vÃ­deo, salva tudo numa pasta Ãºnica e registra prompt, modelo e parÃ¢metros](assets/hero-fluxo.png)

## ðŸ“– Guia de uso

Guia completo (landing + passo a passo): **https://inematds.github.io/generator-skill/guia/**

Duas versÃµes da skill `/generate` â€” um comando para o agente gerar imagens e
vÃ­deos sob demanda, escolher a rota de menor custo, controlar gasto e salvar
tudo num lugar sÃ³.

SÃ£o duas porque a decisÃ£o mais importante da skill â€” **qual Ã© a rota mais
barata** â€” tem respostas opostas dependendo da mÃ¡quina.

## Qual usar

| | [`generate-local/`](generate-local/) | [`generate-api/`](generate-api/) |
|---|---|---|
| **Para** | mÃ¡quina com modelo rodando nela | qualquer mÃ¡quina, tudo via API |
| **Rota mais barata** | local, **$0** (flux2-klein na GPU) | modelo barato pago, ~$0,02 |
| **Modelo de cobranÃ§a** | hardware jÃ¡ pago, custo marginal zero | pay-as-you-go por chamada |
| **Rota paga** | exceÃ§Ã£o â€” duas razÃµes sÃ³ | Ã© o Ãºnico caminho |
| **Imagem padrÃ£o** | flux2-klein local, ~6 s | modelo barato de imagem |
| **Imagem com texto legÃ­vel** | fal.ai, pago (local erra letra) | modelo top, pago |
| **VÃ­deo padrÃ£o** | render 2.5D local (`pixflow-motion`), $0 | modelo generativo, $0,20â€“0,35/s |
| **PortÃ£o de custo** | existe, quase nunca dispara | dispara em toda execuÃ§Ã£o |
| **"Rascunhe barato, finalize caro"** | sem sentido â€” Ã© o mesmo modelo | Ã© a regra que mais economiza |
| **Livro-caixa** | existe, quase sempre zerado | Ã© o coraÃ§Ã£o da skill |
| **Depende de** | servidor `inemaimg` no ar (porta 8000) | `FAL_KEY` + `KIE_API_KEY` num `.env` |
| **Teto de aviso** | $20/mÃªs | configurÃ¡vel, padrÃ£o $50/mÃªs |

O que **nÃ£o** muda entre as duas, porque Ã© o que faz o sistema durar: uma receita
Markdown por modelo, biblioteca plana, log JSON ao lado de cada arquivo,
referÃªncia real em vez de descrita, e as regras dentro do `SKILL.md` â€” que o
agente relÃª a cada uso.

## Os cinco passos

O fluxo Ã© o mesmo nas duas versÃµes; muda sÃ³ para onde o passo 1 aponta.

1. **Roteia para o modelo mais econÃ´mico** que dÃ¡ conta da tarefa, e lÃª a receita
   daquele modelo antes de chamar.
2. **Usa referÃªncias reais** â€” logotipos, rostos, estilo â€” carregadas de `refs/`.
   Descrever um logo em palavras devolve logo errado toda vez.
3. **Gera** imagem ou vÃ­deo. Se o modelo for assÃ­ncrono, consulta o status e
   baixa na hora, porque URL de resultado expira.
4. **Salva tudo numa pasta Ãºnica e plana**, sem subpastas, com nome previsÃ­vel.
5. **Registra prompt, modelo, parÃ¢metros e custo** num JSON ao lado do arquivo.

## Por que agregador, e por que pay-as-you-go

![Modelos criativos (nano banana, kling, veo, seedance, gpt image) alcanÃ§Ã¡veis por duas rotas: um agregador por assinatura mensal, ou agregadores pay-as-you-go como Fal AI, Wavespeed AI e Kie AI](assets/rotas-assinatura-vs-payg.png)

Os mesmos modelos sÃ£o alcanÃ§Ã¡veis por caminhos com contas muito diferentes.
Plataformas de **assinatura** cobram um valor fixo por mÃªs â€” previsÃ­vel, e boa
quando o uso Ã© alto e constante. Agregadores **pay-as-you-go** (fal.ai, Kie AI)
cobram por chamada, com uma chave e uma fatura sÃ³ para dezenas de modelos.

A versÃ£o `generate-api` assume pay-as-you-go, por trÃªs razÃµes: comeÃ§a em zero,
o custo Ã© atribuÃ­vel a cada geraÃ§Ã£o (Ã© o que alimenta o livro-caixa), e nÃ£o se
paga mensalidade em mÃªs parado. Se o volume subir a ponto de a assinatura sair
mais barata, o cÃ¡lculo Ã© simples â€” some o mÃªs no `--saldo` e compare.

Na versÃ£o `generate-local` a discussÃ£o nÃ£o existe: a GPU jÃ¡ estÃ¡ paga, e a rota
paga sÃ³ entra nas duas exceÃ§Ãµes documentadas.

## Instalar

```bash
cp -r generate-local ~/.claude/skills/generate               # mÃ¡quina com GPU
cp -r generate-api  <workspace>/.claude/skills/generate      # mÃ¡quina sem modelo local
```

As duas declaram `name: generate`. Instale **uma** por workspace, ou renomeie.

## DocumentaÃ§Ã£o

- [`generate-local/README.md`](generate-local/README.md) e
  [`generate-api/README.md`](generate-api/README.md) â€” cada versÃ£o em detalhe.
- [`doc/guia-skill-generate-ptbr.md`](doc/guia-skill-generate-ptbr.md) â€” guia
  tÃ©cnico traduzido: autenticaÃ§Ã£o por provider, padrÃ£o assÃ­ncrono, tabela de
  modelos, faixas de custo.
- [`doc/analise-generate-skill-guide.md`](doc/analise-generate-skill-guide.md) â€”
  anÃ¡lise do material original: o que ele acerta e o que faltava (orÃ§amento
  acumulado, preÃ§o versionado, retomada de job assÃ­ncrono â€” os trÃªs estÃ£o
  implementados aqui).

## Aviso que vale para as duas

IDs de modelo, preÃ§os e endpoints mudam com frequÃªncia. Tudo que nÃ£o foi
confirmado por uma chamada real estÃ¡ marcado **NÃƒO VERIFICADO** nas receitas.
Confirme na documentaÃ§Ã£o atual do provider antes de usar para valer.

---

<div align="center">
  <img src="capa/logo.svg" alt="Arckhé AI" height="32" />
  <p><strong>Arckhé AI</strong></p>
</div>