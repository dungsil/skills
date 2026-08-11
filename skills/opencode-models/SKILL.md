---
name: opencode-models
description: Discovers new models available on the Tailscale OpenAI-compatible provider endpoint, researches their official API specs and pricing, and registers or updates them in the opencode global config (provider.tailscale.models) with cost, limit, reasoning, tool_call, attachment metadata, and reasoning-effort variants. Adds service_tier "fast" with fast-tier pricing for OpenAI models, and registers official reasoning-effort levels as model variants. Use when new models appear on the Tailscale endpoint, when asked to register or update opencode model metadata or pricing, or when reasoning-effort variants need to be registered or refreshed.
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

## Effort Variants Registration

Every registered model that supports reasoning gets **reasoning-effort variants** based on the vendor's official API levels. This lets the user pick an effort level per model (via `agent.variant`, `command.variant`, or the TUI).

### Config shape

`provider.tailscale.models.<model>.variants` is an object keyed by variant name; each value is an AI SDK provider-options object:

```jsonc
"variants": {
  "low":    { "reasoningEffort": "low" },
  "medium": { "reasoningEffort": "medium" },
  "high":   { "reasoningEffort": "high" },
  "xhigh":  { "reasoningEffort": "xhigh" },
  "max":    { "reasoningEffort": "max" }
}
```

### How variants behave in opencode

- **Key naming**: use camelCase `reasoningEffort` for `@ai-sdk/openai-compatible` — the AI SDK serializes it to `reasoning_effort` on the wire. (Contrast: `service_tier` in `options` must be snake_case because it is spread verbatim.)
- **Merge order** (last wins): base defaults < `model.options` < `agent.options` < **variant**.
- **Auto-generation**: opencode already auto-generates `low`/`medium`/`high` (plus `max` for `deepseek-v4*`) for reasoning models on openai-compatible providers. Config-defined variants with the same name override them; `{ "name": { "disabled": true } }` removes one; new names are added.
- **Reference**: `agent.variant` / `command.variant` only apply when the model actually exposes that variant name — otherwise it silently falls back to default.
- **Models without official effort levels** (e.g. Claude Haiku, which only supports extended-thinking `budget_tokens`): register no variants and leave a `//` comment.

### Official effort levels (verified 2026-08-11)

| Family | Parameter (wire) | Official levels | Notes |
|---|---|---|---|
| OpenAI GPT-5.6 family | `reasoning_effort` | low, medium, high, xhigh, max | default medium; `none`/`minimal` exist but are disable-like |
| OpenAI gpt-5.3-codex | `reasoning_effort` | low, medium, high, xhigh | Responses API only |
| Anthropic Opus/Sonnet/Fable 5 | `output_config.effort` | low, medium, high, xhigh, max | adaptive thinking, default high; via openai-compatible use `reasoningEffort` |
| Anthropic Haiku 4.5 | — | none | extended-thinking budget only, no effort levels |
| xAI Grok 4.5 / grok-build | `reasoning_effort` | low, medium, high | default high, cannot disable |
| Gemini 3.1 Pro | `thinking_level` | low, medium, high | cannot disable |
| Gemini 3.6 Flash / 3.5 Flash Lite | `thinking_level` | minimal, low, medium, high | default medium / minimal |
| GLM 5.2 | `reasoning_effort` | low, medium, high, xhigh, max | default max |
| Kimi K3 / K3 Fast | `reasoning_effort` | low, high, max | always reasons, default max |
| DeepSeek V4 Flash / Pro | `reasoning_effort` | low, high, max | default high |

Register levels from this table using `reasoningEffort` as the option key. When a vendor's levels are a strict subset (e.g. Kimi/DeepSeek lack `medium`), register only the official values — do not invent intermediate levels.

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
