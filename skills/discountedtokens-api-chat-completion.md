---
name: discountedtokens-chat-completion
description: Call a resold GPT-5.x model through the DiscountedTokens OpenAI-compatible chat completion endpoint using a prepaid key.
api: DiscountedTokens API
generated: '2026-09-17'
method: generated
source: openapi/discountedtokens-api-openapi.json
operations:
  - listModels
  - createChatCompletion
---

# DiscountedTokens: chat completion

Generate a chat completion from a resold frontier model (GPT-5.x) through the OpenAI-compatible
surface at `https://discountedtokens.com/v1`. Billing is prepaid and usage-based.

## Prerequisites

- A prepaid key. Buy credits (no signup required) at https://discountedtokens.com/guest.
- Base URL: `https://discountedtokens.com/v1`.
- Auth: `Authorization: Bearer <key>`.

## Steps

1. **Pick a model (optional, free).** `GET /models` (operationId `listModels`) returns the live
   catalogue with per-token prices. Valid ids today: `gpt-5.6-sol`, `gpt-5.6-terra`, `gpt-5.5`.
   This read is unbilled — use it to preview cost before a billed call.

2. **Create the completion.** `POST /chat/completions` (operationId `createChatCompletion`) with:
   ```json
   {"model":"gpt-5.6-terra","messages":[{"role":"user","content":"..."}],"max_tokens":256,"stream":false}
   ```
   `model` and `messages` are required. Set `stream: true` for OpenAI-compatible SSE streaming.

3. **Handle billing/auth errors.** `401 {"error":{"message":"Missing API key"}}` — the key is
   absent or unrecognized. `402` — prepaid credits are exhausted; top up at `/guest` and retry.
   See `errors/discountedtokens-api-problem-types.yml`.

## Notes

- There is no idempotency key; a retried timed-out request may be billed twice
  (`conventions/discountedtokens-api-conventions.yml`). Prefer a fresh preview via `/models` over
  blind retries.
- The completion cannot be reversed or refunded through the API. Successful requests are billed.
- To use the stock OpenAI SDK, override `base_url` to `https://discountedtokens.com/v1`.
