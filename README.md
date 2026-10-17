# GPT Image 2.5 Flare API (`gpt-image-2.5-ext`)

<!-- conv-kit:v1 -->

<p align="center">
  <img src="assets/badges/price.svg" alt="observed unit price"> <img src="assets/badges/billing.svg" alt="billing model"> <img src="assets/badges/compat.svg" alt="OpenAI-compatible endpoint">
</p>

<p align="center">
  <img src="assets/01-product-hero-perfume.jpg" width="820" alt="Sample render via the APIMart image API">
</p>

> **from $0.010625 per image** on the APIMart route — одна OpenAI-совместимая конечная точка на `https://api.apimart.ai/v1`, ежемесячный тариф не нужен. *(данные на 2026-10-12)*

**[Получить API-ключ](https://go.apimart.ai/k-d66c7b)** · **[Актуальные цены](https://go.apimart.ai/k-253ff3)** · **[Страница модели](https://go.apimart.ai/k-1c9d7d)** · [⚡ Быстрый старт за 60 секунд](#quickstart)

**Почему команды используют GPT Image 2.5 Flare (`gpt-image-2.5-ext`) через APIMart**

- **Один ключ — весь каталог.** Тот же base URL `https://api.apimart.ai/v1` и тот же заголовок `Authorization` дают доступ к GPT Image 2.5 Flare (`gpt-image-2.5-ext`) и ещё 300+ моделям для изображений, видео и языка — менять нужно поле `model`, а не клиент.
- **Минимум $1, оплата по факту.** Ни подписки, ни предоплаченного тарифа, который нужно заранее рассчитать: пополняйте от $1 и расходуйте на вызовы.
- **Сумма списания возвращается в ответе.** Каждая завершённая задача сообщает списанную сумму (`cost` / `credits_cost`); задача со статусом `failed` возвращается на баланс.
- **Асинхронность по умолчанию.** Отправьте задачу, получите `task_id`, опрашивайте `GET /v1/tasks/{id}` — пакетная обработка и повторы это обычная работа с очередью, а не отдельная интеграция.

<!-- /conv-kit:v1 -->



GPT Image 2.5 Flare API on APIMart: generate and edit images with Flare, up to 16 references and PNG, JPEG or WebP. Save 20% or more with membership.

## Model id and routes

| Route | `model` value | Billing | Notes |
| --- | --- | --- | --- |
| Per-image | `gpt-image-2.5-ext` | per delivered image, by resolution | see the [model page](https://go.apimart.ai/k-1c9d7d) for the exact tiers |

Endpoint: `POST https://api.apimart.ai/v1/images/generations`, then poll `GET /v1/tasks/{task_id}`.

## Pricing

<!-- pricing:model:start -->
| Output | List price | Effective price |
| --- | --- | --- |
| flare@1K | 0.010625 | 0.0085 |
| flare@2K | 0.0175 | 0.03 |
| flare@4K | 0.02625 | 0.04 |
| sunburst@1K | 0.010625 | 0.0085 |
| sunburst@2K | 0.0175 | 0.03 |
| sunburst@4K | 0.02625 | 0.04 |


<!-- pricing:model:end -->

Prices are a snapshot; the [pricing page](https://go.apimart.ai/k-253ff3) and [`data/model.json`](data/model.json) are refreshed by CI, and a completed task reports the exact amount in its `cost` field.

## Request parameters

| Field | Type | Default | Notes |
| --- | --- | --- | --- |
| `model` | string | required | the model id shown in the route table above |
| `prompt` | string | required | what to generate, or what to change when editing |
| `size` | string | `auto` / `1:1` | aspect ratio, or `auto` where the route supports it |
| `resolution` | string | `1K` | output resolution tier — cost scales with it |
| `n` | integer | `1` | number of images per task, where the route allows more than one |
| `image_urls` | string[] | — | reference images as public HTTP(S) URLs or base64 data URLs |
| `nsfw_check` | boolean | `false` | runs `omni-moderation-latest` before submitting |

The table lists the fields accepted by the APIMart `images/generations` endpoint. Check the [model page](https://go.apimart.ai/k-1c9d7d) for anything this model adds on top.

## Quickstart

```bash
curl --request POST --url https://api.apimart.ai/v1/images/generations \
  --header "Authorization: Bearer $APIMART_API_KEY" --header 'Content-Type: application/json' \
  -d '{"model":"gpt-image-2.5-ext","prompt":"A bamboo forest at sunrise, slow push-in","size":"1:1","resolution":"1K"}'
```

```python
import os, time, requests

BASE = "https://api.apimart.ai/v1"
HEADERS = {"Authorization": f"Bearer {os.environ['APIMART_API_KEY']}", "Content-Type": "application/json"}

created = requests.post(f"{BASE}/images/generations", headers=HEADERS, timeout=60, json={
    "model": "gpt-image-2.5-ext", "prompt": "A bamboo forest at sunrise, slow push-in",
}).json()
task_id = created["data"]["id"]

while True:
    task = requests.get(f"{BASE}/tasks/{task_id}", headers=HEADERS, timeout=60).json()["data"]
    if task["status"] in ("completed", "failed"):
        break
    time.sleep(5)
print(task.get("cost"), task["result"]["images"][0]["url"])
```

Runnable versions: [`examples/`](examples). The task lifecycle is `pending → processing → completed | failed`.

## FAQ

**Какой id у модели GPT Image 2.5 Flare?**

`gpt-image-2.5-ext`. Смотрите таблицу маршрутов выше.

**Как тарифицируется?**

**from $0.010625 per image** on the APIMart route. Завершённая задача указывает точную сумму в поле `cost`; неудачная задача возвращается на баланс.

**How do I get a key?**

Use the [Get an API key](https://go.apimart.ai/k-d66c7b) link — $1 minimum top-up, no plan required.

<!-- related-repos:auto -->
## Related repositories

- **Same model, this language** — the other half of this pair: [gpt-image-2-5-flare-prompts-ru](https://github.com/sm08ni5/gpt-image-2-5-flare-prompts-ru)
- **Same model, other languages** — 10 more language builds: English `gpt-image-2-5-flare-api` · 中文 `gpt-image-2-5-flare-api-cn` · Deutsch [gpt-image-2-5-flare-api-de](https://github.com/m4izuw9fi76/gpt-image-2-5-flare-api-de) · Español [gpt-image-2-5-flare-api-es](https://github.com/xctnd3o4yv/gpt-image-2-5-flare-api-es) · Français `gpt-image-2-5-flare-api-fr` · Relay/gateway `gpt-image-2-5-flare-api-gateway` · Bahasa [gpt-image-2-5-flare-api-id](https://github.com/zcb3y0r0v468wg/gpt-image-2-5-flare-api-id) · 日本語 `gpt-image-2-5-flare-api-ja` · 한국어 [gpt-image-2-5-flare-api-ko](https://github.com/ikhyz/gpt-image-2-5-flare-api-ko) · Português `gpt-image-2-5-flare-api-pt`
- **More models in this language** — [midjourney-api-ru](https://github.com/sfpxvicw/midjourney-api-ru) · [gemini-2-5-flash-image-preview-api-ru](https://github.com/zwtdva8/gemini-2-5-flash-image-preview-api-ru) · [nano-banana-api-ru](https://github.com/ggpbi40ss/nano-banana-api-ru) · [gemini-3-1-flash-image-preview-api-ru](https://github.com/lu60av/gemini-3-1-flash-image-preview-api-ru) · [nano-banana-2-api-ru](https://github.com/jnp3hlry/nano-banana-2-api-ru) · [gemini-3-pro-image-preview-api-ru](https://github.com/dbxz24/gemini-3-pro-image-preview-api-ru)
- **Topic hubs** — every build of this model, all languages: [gpt-image-2-5-flare](https://github.com/topics/gpt-image-2-5-flare) · [gpt-image-2-5-flare-api](https://github.com/topics/gpt-image-2-5-flare-api)

This repository is one build in a cross-linked set of API guides for the same model family; each language build links back to the others.
<!-- /related-repos:auto -->
## Attributed links (how this repository is measured)

| Purpose | Attributed link | Target |
| --- | --- | --- |
| Model page | <https://go.apimart.ai/k-1c9d7d> | `apimart.ai/ru/model/gpt-image-2-5-flare` |
| Live pricing | <https://go.apimart.ai/k-253ff3> | `apimart.ai/ru/pricing` |
| Get an API key | <https://go.apimart.ai/k-d66c7b> | `apimart.ai/ru/keys` |

Outbound APIMart links are minted through the promo link API; hand-made tracking parameters are rejected by
`tools/check_links.py` in CI.

## Disclosure

GPT Image 2.5 Flare is a third-party model served through APIMart; this repository documents how to call it and publishes
model ids and prices, and does not claim official status. Model names, prices and documentation belong to their
respective owners.

## License

MIT — see [LICENSE](LICENSE).
