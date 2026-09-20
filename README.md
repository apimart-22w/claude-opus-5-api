# Claude Opus 5 API (claude-opus-5)

<!-- conv-kit:v1 -->

<p align="center">
  <img src="assets/badges/price.svg" alt="observed unit price"> <img src="assets/badges/billing.svg" alt="billing model"> <img src="assets/badges/compat.svg" alt="OpenAI-compatible endpoint">
</p>

> **$4 / $20 per million tokens** (input / output, effective) — one OpenAI-compatible endpoint at `https://api.apimart.ai/v1`, no monthly plan required. *(observed 2026-09-17)*

**[Get an API key](https://go.apimart.ai/k-95e9da)** · **[Live pricing](https://go.apimart.ai/k-5ec63e)** · **[Model page](https://go.apimart.ai/k-a8cf8d)** · [⚡ 60-second quickstart](#quickstart)

**Why teams call Claude Opus 5 (`claude-opus-5`) through APIMart**

- **One key, entire catalog.** The same `https://api.apimart.ai/v1` base URL and `Authorization` header reach Claude Opus 5 (`claude-opus-5`) and 300+ other image, video and language models — switch the `model` field, not your client.
- **$1 minimum, pay as you go.** No subscription and no prepaid plan to size up front: top up from $1 and spend it on calls. There is no free quota to burn through first, so the price in this table is the price you pay.
- **The charge comes back in the response.** Every call reports the amount billed (`cost` / `credits_cost`), so a spend number is read per call instead of guessed at month end.
- **Drop-in OpenAI shape.** `POST /v1/chat/completions` with the same request body your client already sends; only `base_url` and `model` change.

<!-- /conv-kit:v1 -->

Claude Opus 5 on APIMart is an Anthropic flagship route available through the OpenAI-compatible chat endpoint: per-million-token billing, prompt-cache write and read rates, and long-form output confirmed by real calls.

## Model ids

| Model id | Tier | Typical use |
| --- | --- | --- |
| `claude-opus-5` | flagship | hardest prompts, long-form reasoning |

Endpoint: `POST https://api.apimart.ai/v1/chat/completions` (OpenAI-compatible). **Streaming is the default** — pass
`stream: false` when you want one JSON object back.

## Pricing (per million tokens)

<!-- pricing:token:start -->
| Token direction | List price / 1M | Effective price / 1M |
| --- | --- | --- |
| cache_write | free | free |
| cache_write_1h | $10.00 | $8.00 |
| cache_write_5m | $6.25 | $5.00 |
| cached_input | $0.5 | $0.4 |
| input | $5.00 | $4.00 |
| output | $25.00 | $20.00 |

<!-- conv-kit:v1:scale -->
### What that costs at scale

| Spend | Cost |
| --- | --- |
| 1M input tokens | $4.00 |
| 10M input tokens | $40.00 |
| 1M input + 250K output (mixed, at the effective output rate) | see the pricing table above |

Linear at the observed per-unit rate, no volume discount assumed. Snapshot 2026-09-17; re-check the live table before committing a budget.
<!-- /conv-kit:v1:scale -->


<!-- pricing:token:end -->

The effective column is what you pay after the default group discount; [`data/model.json`](data/model.json) is refreshed
daily by CI, and the `usage` block in every response tells you exactly which tokens were billed.

## Verified capabilities

| Capability | Verified behaviour |
| --- | --- |
| Non-streaming chat | `stream: false` returns `usage` including cached and cache-write token counts |
| Streaming | SSE by default; stop at `[DONE]` |
| System prompts | `system` role accepted and respected |
| Long output | multi-thousand-character answers complete with `finish_reason: stop` |
| Prompt caching | cache write (5m / 1h) and cache read rates are listed separately in the pricing table |

## Quickstart

```bash
curl -sS https://api.apimart.ai/v1/chat/completions \
  -H "Authorization: Bearer $APIMART_API_KEY" -H 'Content-Type: application/json' \
  -d '{"model":"claude-opus-5","stream":false,"messages":[{"role":"user","content":"Name three retry rules."}]}'
```

```python
import os, requests

BASE = "https://api.apimart.ai/v1"
HEADERS = {"Authorization": f"Bearer {os.environ['APIMART_API_KEY']}", "Content-Type": "application/json"}

payload = requests.post(f"{BASE}/chat/completions", headers=HEADERS, timeout=120, json={
    "model": "claude-opus-5", "stream": False,
    "messages": [{"role": "user", "content": "Name three retry rules."}],
}).json()
print(payload["choices"][0]["message"]["content"])
print(payload["usage"])
```

Streaming, tool calling and JSON mode examples are in [`examples/`](examples) (`t_curl.sh`, `python_chat.py`).

## Real call outputs

These rows are actual completions recorded from this route, with the token usage the API returned and the cost computed
from the effective rates above.

| Prompt | Response excerpt | Tokens (in/out) | Reported cost |
| --- | --- | --- | --- |
| `Compare per-image billing with token billing for a pipeline that generates 20,000 product ` | ## The structural difference  | | Per-image billing | Token billing | |---|---|---| | Unit of charge | One output image at a quality/size tier | Input tokens (prompt + reference images + history) + output tokens (image) … | 51 / 2658 | $0.0534 |
| `Write a six-step checklist for moving an OpenAI-compatible workload from one API gateway t` | # Migration Checklist: OpenAI-Compatible Workload → New API Gateway  ## 1. Inventory the surface you actually depend on  "OpenAI-compatible" is a spectrum, and gateways diverge at the edges.  - **Endpoints in use:** `/ch… | 44 / 3367 | $0.0675 |

Full transcripts (including longer answers) are in [`data/samples.json`](data/samples.json).

<!-- conv-kit:v1:fix -->
## First-call troubleshooting

| Symptom | Likely cause | Fix |
| --- | --- | --- |
| `401` / `invalid api key` | key missing, truncated, or a stray newline pasted into the header | Re-copy it from the console; the header is `Authorization: Bearer $APIMART_API_KEY` |
| balance / credit error | the account has no balance | Top up from $1 in the console — there is no free quota to fall back on |
| `429` | concurrent requests on one key | Back off, then retry the same request with the same `Idempotency-Key` |
| `400` / model not found | wrong route for the id: the per-unit alias needs its `version`, the official id must not send one | Copy the exact `model` value from the route table above |
| task ends `failed` | prompt rejected by the filter, or a reference image URL expired | Re-submit with a **new** `Idempotency-Key` and re-host the reference image |
| result URL stops working | result links expire | Download the file as soon as the task reports `completed` |
<!-- /conv-kit:v1:fix -->

## FAQ

**What is the Claude Opus 5 API model id?**

`claude-opus-5`. Sibling ids on the same endpoint include `claude-opus-4-8`, `claude-opus-4-7` and `claude-sonnet-4-6`, so model selection is a string change.

**How does prompt caching change the price?**

Cached input is billed far below fresh input, while writing the cache costs a premium for 5-minute or 1-hour retention. For repeated system prompts the cached path is where the savings are.

**Can I call it with the OpenAI SDK?**

Yes for the chat-compatible path; the native Claude Messages shape is documented separately if you need Anthropic-specific fields.

**Is long output billed differently?**

Output tokens are billed per million like input, so a long answer is simply more output tokens — the `usage` block in the response tells you exactly what was consumed.

## Related searches

- `claude opus 5 api`
- `claude opus 5 api pricing`
- `claude api`
- `anthropic api alternative`
- `llm api pricing comparison`
- `prompt caching api`
- `openai compatible api`

<!-- conv-kit:v1:cta -->
---

**Start with $1.** [Get an API key](https://go.apimart.ai/k-95e9da) → [check live pricing](https://go.apimart.ai/k-5ec63e) → [open Claude Opus 5 (`claude-opus-5`) in the model library](https://go.apimart.ai/k-a8cf8d). The first call is three steps: submit, poll `task_id`, read the charged amount off the response.
<!-- /conv-kit:v1:cta -->

## Attributed links (how this repository is measured)

| Purpose | Attributed link | Target |
| --- | --- | --- |
| Browse the model catalog | <https://go.apimart.ai/k-a8cf8d> | `apimart.ai/model` |
| Current pricing page | <https://go.apimart.ai/k-5ec63e> | `apimart.ai/pricing` |
| Get an API key | <https://go.apimart.ai/k-95e9da> | `apimart.ai/keys` |

Outbound APIMart links are minted through the promo link API; hand-made tracking parameters are rejected by
`tools/check_links.py` in CI.

## Disclosure

Claude Opus 5 is a third-party model served through APIMart; this repository publishes model ids, measured prices and
real call outputs, and does not claim official status. Model names, prices and documentation belong to their respective
owners. Endpoint reference: [https://docs.apimart.ai/en/api-reference/texts/general/claude-messages](https://docs.apimart.ai/en/api-reference/texts/general/claude-messages).

## Repository map

```text
README.md             model ids, token pricing, verified capabilities, real outputs
data/model.json       token rates for every tier (CI-refreshed)
data/samples.json     recorded completions with usage and computed cost
tools/snapshot.py     refresh pricing from the public payload
tools/check_links.py  attribution guard
examples/             curl and Python clients (streaming, tools, JSON mode)
.github/workflows/    daily price refresh + validation
```

## License

MIT — see [LICENSE](LICENSE).
