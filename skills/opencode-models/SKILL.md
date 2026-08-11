---
name: opencode-models
description: Discovers new models available on the Tailscale OpenAI-compatible provider endpoint, researches their official API specs and pricing, and registers or updates them in the opencode global config (provider.tailscale.models) with cost, limit, reasoning, tool_call, and attachment metadata. Adds service_tier "fast" with fast-tier pricing for OpenAI models. Use when new models appear on the Tailscale endpoint, when asked to register or update opencode model metadata, or when model pricing in the opencode config needs to be refreshed.
---

# opencode Models Registrar

Keep the opencode global config's Tailscale provider model registry in sync with the models actually served by the endpoint. The workflow is: discover → diff → research → register → validate.

Never fabricate prices or specs. A model with no official API pricing is registered with `name` only plus an explanatory comment.

## Key Context

- **Config file**: the active opencode global config — `~/.config/opencode/opencode.jsonc` (Windows: `%USERPROFILE%\.config\opencode\opencode.jsonc`; in some setups `D:\data\xdg\config\opencode\opencode.jsonc`). If a project `opencode.json`/`.opencode/opencode.json` also defines the provider, update the file the user designates.
- **Provider shape** (already present in the config):
  ```jsonc
  "provider": {
    "tailscale": {
      "name": "Tailscale",
      "npm": "@ai-sdk/openai-compatible",
      "options": { "baseURL": "https://<tailscale-endpoint>/v1" },
      "models": { "<model-id>": { ... } }
    }
  }
  ```
- The endpoint is OpenAI-compatible. Model list: `GET {baseURL}/models` (read `baseURL` from the config's provider options — do not hardcode).

## Workflow

1. **Discover** — fetch `{baseURL}/models` and collect every served model ID. If the request fails, ask the user for the current model list instead; never guess.
2. **Diff** — read the current `provider.tailscale.models` keys in the config. Identify: (a) newly served models not yet registered, (b) registered models no longer served (flag for removal, do not silently delete), (c) models whose metadata is missing/outdated.
3. **Research** — for each new or outdated model, identify its vendor family from the ID/name, then research OFFICIAL specs in parallel (see Vendor Pricing Sources). Capture: input/output/cache_read price per 1M tokens, context window, max output tokens, reasoning support, tool_call support, attachment (vision) support, and whether a fast service tier exists. **Do not fabricate.** Report "NOT FOUND" for anything unverifiable.
4. **Register** — add or update the model entry following the Metadata Schema and Special Rules below. Preserve unrelated config fields and `$schema` exactly.
5. **Validate** — parse the resulting JSONC (comment-aware) to confirm it is valid, and confirm every model has a `name`. Report what was added/changed and remind the user to restart opencode.

## Metadata Schema

Per opencode's config schema (`https://opencode.ai/config.json`), a model entry accepts:

| Field | Shape | Notes |
|---|---|---|
| `name` | string | Required, display name |
| `cost` | `{ input, output, cache_read?, cache_write?, context_over_200k? }` | USD per 1M tokens; `input`+`output` required; use standard (short-context) tier unless fast tier is the default |
| `limit` | `{ context, output, input? }` | Token counts; `context`+`output` required — omit the whole `limit` if `output` is unpublished |
| `reasoning` | boolean | Reasoning/thinking support |
| `tool_call` | boolean | Function/tool calling support |
| `attachment` | boolean | Vision/image input support (omit when unconfirmed) |
| `options` | object | Free-form; for `@ai-sdk/openai-compatible` every key is forwarded **verbatim** into the request body |
| `family`, `release_date`, `modalities`, `status`, `experimental` | per schema | Optional enrichments |

## Special Rules

1. **OpenAI models (`gpt-*`) get `service_tier: "fast"` by default**: add `"options": { "service_tier": "fast" }` and use **fast-tier pricing** (typically 2× standard). The key must be snake_case `service_tier` — `@ai-sdk/openai-compatible` spreads unknown option keys verbatim into the request body, and OpenAI's API rejects camelCase `serviceTier`.
2. **No official API pricing** (research-preview-only models, CLI-only models, e.g. `gpt-5.3-codex-spark` before GA): register `name` only, add a `//` comment explaining why, and if a documented base model exists (e.g. `gpt-5.3-codex`), use its pricing with a comment noting the substitution.
3. **Unpublished max output** (e.g. Grok models): omit `limit` entirely rather than inventing a value.
4. **Long-context tiering**: if a vendor prices prompts ≥200K tokens higher (Gemini, Grok, GPT-5.6 family), record the standard sub-200K rate in `cost` and add a comment with the higher tier.
5. **Unverifiable vendor mapping**: if the model ID's vendor cannot be determined, ask the user before researching.

## Vendor Pricing Sources (official only)

| Family prefix | Vendor | Official source |
|---|---|---|
| `opus`, `sonnet`, `haiku`, `fable`, `mythos` | Anthropic | `platform.claude.com/docs/en/about-claude/pricing`, `anthropic.com/pricing` |
| `gpt` | OpenAI | `developers.openai.com/api/docs/pricing`, model pages `/api/docs/models/<id>` |
| `grok`, `composer` | xAI | `docs.x.ai/developers/pricing`, `docs.x.ai/developers/models` |
| `gemini` | Google | `ai.google.dev/gemini-api/docs/pricing` |
| `glm` | Zhipu/Z.ai | `docs.z.ai/guides/overview/pricing`, `docs.z.ai/guides/llm/<model>` |
| `kimi`, `moonshot` | Moonshot AI | `platform.kimi.ai` (formerly platform.moonshot.ai) pricing docs |
| `deepseek` | DeepSeek | `api-docs.deepseek.com/quick_start/pricing` |
| `qwen`, `llama`, `mistral`, other | — | Vendor official pricing page; if none, report NOT FOUND |

Research `cost.cache_read` when the vendor publishes a cached-input rate. Record the research date in a comment.

## JSONC Validation Snippet

After editing, validate with a comment-aware parse (plain `JSON.parse` fails on `//` comments and trailing commas):

```js
const fs = require('fs');
const s = fs.readFileSync('<config-path>', 'utf8');
let out = '', inStr = false, esc = false;
for (let i = 0; i < s.length; i++) {
  const c = s[i];
  if (inStr) { out += c; if (esc) esc = false; else if (c === '\\') esc = true; else if (c === '"') inStr = false; }
  else if (c === '"') { inStr = true; out += c; }
  else if (c === '/' && s[i+1] === '/') { while (i < s.length && s[i] !== '\n') i++; }
  else if (c === '/' && s[i+1] === '*') { i += 2; while (i < s.length && !(s[i] === '*' && s[i+1] === '/')) i++; i++; }
  else out += c;
}
const j = JSON.parse(out);
console.log('JSON valid; models:', Object.keys(j.provider.tailscale.models).length);
```

## Final Report

Summarize: newly registered models, updated metadata (with prices and sources), models flagged for removal, and any model skipped due to missing official pricing. Note that the config is loaded at startup only — **the user must quit and restart opencode** for changes to take effect.
