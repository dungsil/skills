# Tailscale Provider Model Registry — Baseline (2026-08-11)

현재 `opencode.jsonc`의 `provider.tailscale.models`에 등록된 모델과 메타데이터. 신규 등록/갱신 시 이 표를 기준으로 diff한다. 가격은 USD/1M tokens, 표준(short-context) 티어 기준. OpenAI 모델은 `service_tier: "fast"` 기본 적용 + fast 티어 가격.

## Anthropic

| Model ID | name | input | output | cache_read | context | max output | notes |
|---|---|---|---|---|---|---|---|
| `opus-5` | Claude Opus 5 | 5 | 25 | — | 1,000,000 | 128,000 | reasoning/tool_call/attachment |
| `sonnet-5` | Claude Sonnet 5 | 2 | 10 | — | 1,000,000 | 128,000 | intro price permanent (2026-08-10) |
| `haiku-4.5` | Claude Haiku 4.5 | 1 | 5 | — | 200,000 | 64,000 | |
| `fable-5` | Claude Fable 5 | 10 | 50 | — | 1,000,000 | 128,000 | |

## OpenAI (fast tier 기본)

| Model ID | name | fast input | fast output | fast cache_read | context | max output | notes |
|---|---|---|---|---|---|---|---|
| `gpt-5.6-sol` | GPT-5.6 Sol | 10 | 60 | 1.0 | 1,050,000 | 128,000 | 2× standard |
| `gpt-5.6-terra` | GPT-5.6 Terra | 4 | 24 | 0.4 | 1,050,000 | 128,000 | 2× standard |
| `gpt-5.6-luna` | GPT-5.6 Luna | 0.4 | 2.4 | 0.04 | 1,050,000 | 128,000 | 2× standard |
| `gpt-5.3-codex-spark` | GPT-5.3 Codex Spark | 3.5 | 28 | 0.35 | 400,000 | 128,000 | base gpt-5.3-codex fast 가격 적용 |

## xAI

| Model ID | name | input | output | cache_read | context | max output | notes |
|---|---|---|---|---|---|---|---|
| `grok-4.5` | Grok 4.5 | 2 | 6 | 0.3 | 500,000 | 미공개 | limit 생략 |
| `composer-2.5` | Composer 2.5 | 1 | 2 | 0.2 | 256,000 | 미공개 | grok-build-0.1 기반 추정, limit 생략 |

## Google

| Model ID | name | input | output | cache_read | context | max output | notes |
|---|---|---|---|---|---|---|---|
| `gemini-3.1-pro-preview` | Gemini 3.1 Pro Preview | 2 | 12 | 0.2 | 1,048,576 | 65,536 | |
| `gemini-3.6-flash` | Gemini 3.6 Flash | 1.5 | 7.5 | 0.15 | 1,048,576 | 65,536 | |
| `gemini-3.5-flash-lite` | Gemini 3.5 Flash Lite | 0.3 | 2.5 | 0.03 | 1,048,576 | 65,536 | |

## 기타

| Model ID | name | input | output | cache_read | context | max output | notes |
|---|---|---|---|---|---|---|---|
| `glm-5.2` | GLM 5.2 | 1.4 | 4.4 | 0.26 | 1,000,000 | 128,000 | text-only (attachment 없음) |
| `kimi-k3` | Kimi K3 | 3 | 15 | 0.3 | 1,048,576 | 131,072 | |
| `kimi-k3-fast` | Kimi K3 Fast | 4.5 | 22.5 | 0.45 | 1,048,576 | 131,072 | Fireworks 라우터 |
| `deepseek-v4-flash` | Deepseek V4 Flash | 0.14 | 0.28 | 0.0028 | 1,000,000 | 384,000 | |
| `deepseek-v4-pro` | Deepseek V4 Pro | 0.435 | 0.87 | 0.003625 | 1,000,000 | 384,000 | |

## 알려진 함정

- `service_tier` (snake_case)만 OpenAI가 수용 — `serviceTier`는 400/무시. `@ai-sdk/openai-compatible`은 options 키를 그대로 request body에 전달.
- OpenAI fast 티어 = 표준 2× (2026-07-30 가격 인하 이후 기준). 이전 `openai.com/api-priority-processing/` 페이지의 terra $5/$30, luna $2/$12는 구버전.
- Anthropic Sonnet 5: docs 표는 $3/$15 (9/1~)로 표기하나 2026-08-10 뉴스 수정으로 $2/$10 영구 확정 — 뉴스가 최신 공식 입장.
- max output 미공개 모델(grok 계열)은 `limit` 전체 생략 (스키마가 context+output 둘 다 요구).
- Composer 2.5는 Cursor 전용 모델이라는 제3자 정보가 있으나, 사용자 확인으로 grok-build-0.1 값 사용 중.
