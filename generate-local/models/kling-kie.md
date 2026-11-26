# Kling (Kie AI) — vídeo generativo, rota paga

Última rota, e a mais cara. Só quando o movimento pedido **nêo pode** ser
extraído de uma imagem parada (ver `pixflow-local.md`). Sempre com cotaçêo
aprovada antes.

| Campo | Valor |
|---|---|
| Model ID | `kling-3.0/video` — **NêO VERIFICADO**, confirmar na página do modelo em kie.ai antes do primeiro uso |
| Provider | Kie AI |
| Método | **Async** (submete, recebe task id, faz poll) |
| Tipo | Vídeo |
| API key | `~/projetos/wifi/.env` → `KIE_API_KEY` |
| Custo | estimativa $0,20–0,35/s → clipe de 5 s ≈ $1–1,75. Ver `references/pricing.md` |
| Fallback | fal.ai (`FAL_KEY`) se o Kie recusar o job ou nêo tiver o modelo |

## Endpoint e auth

```
POST https://api.kie.ai/api/v1/jobs/createTask
Authorization: Bearer {KIE_API_KEY}
```

Kie usa `Bearer`; fal usa `Key`. Nêo confundir.

Kie também tem upload de arquivo que devolve URL pública — é como se manda uma
imagem local para image-to-video. **Isso publica o arquivo**: nêo subir rosto,
documento ou material privado sem falar com o usuário.

## Padrêo assíncrono

1. POST do job → a resposta traz um **task id**.
2. Poll do status a cada 10–15 s, com paciência.
3. Status `complete` → a resposta traz a URL do arquivo.
4. **Baixar na hora** — URLs de resultado expiram em horas.
5. Salvar em `generations/` e rodar `registrar.py` com o custo real.

Se o poll morrer no meio, **o dinheiro já foi gasto**: guarde o task id no
sidecar assim que ele existir, para poder retomar em vez de pagar de novo.

## Observaçêo sobre a skill `kling-3-0`

Existe uma skill `kling-3-0` instalada com preços por segundo bem detalhados,
mas ela chama o CLI `runcomfy`, que **nêo está instalado nesta máquina**
(verificado em 2026-08-01). Serve como referência de preço e de escolha de tier;
nêo serve como caminho de execuçêo enquanto o CLI nêo existir.
