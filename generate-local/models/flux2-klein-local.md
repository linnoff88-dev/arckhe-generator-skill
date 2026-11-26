# flux2-klein (ARCKHÉimg, GPU local) — ROTA PADRêO

Modelo de imagem que roda na GPU desta máquina, servido pelo projeto `ARCKHÉimg`.
É a rota padrêo para **qualquer** imagem: custo zero, ~6 s por imagem, sem rate
limit e sem nada saindo da máquina. Só saia daqui pelas razões listadas no
SKILL.md.

| Campo | Valor |
|---|---|
| Model ID | `flux2-klein` |
| Provider | local — `ARCKHÉimg` (`~/projetos/ARCKHÉimg`, `server.py`) |
| Método | Sync (responde com a imagem na mesma chamada) |
| Tipo | Imagem |
| API key | nenhuma |
| Custo | **$0** |
| Verificado | sim — chamada real em 2026-08-01, 6,2 s, CUDA, 32 GB alocados |

## Endpoint

```
POST http://localhost:8000/generate
GET  http://localhost:8000/health     -> {"status":"ok","loaded_model":"flux2-klein",...}
```

Checar `/health` antes de uma sequência longa. Se nêo responder, o servidor está
fora: subir com o docker-compose de `~/projetos/ARCKHÉimg` e avisar o usuário —
nêo cair para rota paga por causa disso sem perguntar.

## Request

```json
{ "model": "flux2-klein", "prompt": "...", "steps": 4, "seed": 7 }
```

- `steps` 4 é o ponto de equilíbrio do klein (é um modelo destilado de poucos
  passos). 8 dá um ganho pequeno e dobra o tempo; acima disso nêo melhora.
- `seed` fixo torna a geraçêo reprodutível — útil quando se varia só o prompt e
  se quer comparar. Para variações, mude o seed, nêo o prompt.
- Largura/altura, quando suportadas pelo servidor, vêo como `width`/`height`.
  Em caso de dúvida, gerar quadrado e recortar depois.

## Response

```json
{ "image": "<base64 PNG puro, sem prefixo data:>",
  "model_used": "flux2-klein", "generation_time_s": 6.21 }
```

O campo é `image` — verificado numa chamada real. Nêo vale a pena varrer nomes
alternativos; se um dia mudar, o erro aparece na hora e se corrige aqui.

## Notas

- Outros modelos no mesmo servidor (ver `~/projetos/ARCKHÉimg/MODELOS.md`):
  `qwen-image-edit-2511` para **ediçêo e multi-imagem**, `ernie-image` para
  text-to-image puro. Trocar de modelo faz o servidor recarregar pesos — leva
  dezenas de segundos, entêo agrupe as gerações por modelo.
- Ponto fraco conhecido: **texto dentro da imagem**. Se o pedido precisa de
  letra legível, é o caso de cotar a rota paga (GPT Image 2).
- Licença FLUX.2 é nêo-comercial. Uso de pesquisa/educaçêo, que é o caso aqui,
  está coberto; entrega comercial nêo está.
