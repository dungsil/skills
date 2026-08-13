# DUNGSIL's Agent Skills

개인용 [에이전트 스킬](https://agentskills.io/home) 모음집

## 설치
```
pnpm dlx skills add dungsil/skills -g
```

## 스킬 목록

## 공통

| 스킬 명                    | 설명                 |
|----------------------------|----------------------|
| [rq] | 요구사항 품질 게이트 |
| [opencode-models] | Tailscale provider 모델 자동 탐지·등록 (opencode 설정) |

## Vibe

| 스킬 명          | 설명                                        | 출처                                                                     |
|------------------|---------------------------------------------|--------------------------------------------------------------------------|
| [vibe-init]      | [0단계] 바이브 코딩 프로젝트 기본 구조 정의 | [mattpocock/skills] - setup-matt-pocock-skills (MIT)                     |
| [vibe-goal]      | [전체] 계획→구현→게이트 완주 오케스트레이터 |                                                                          |
| [vibe-plan]      | [1단계] 기능 구현 인터뷰 및 스펙/이슈 생성  | [mattpocock/skills] - grill-with-docs, to-spec, to-tickets, triage (MIT) |
| [vibe-deep-plan] | [1단계] 대규모 기능 구현                    | [mattpocock/skills] - wayfinder, research, prototype (MIT)               |
| [vibe-implement] | [2단계] 스펙/티켓 기반 구현                 | [mattpocock/skills] - implement, tdd, resolving-merge-conflicts (MIT)    |
| [vibe-review]    | [개선] 코드 리뷰                            | [mattpocock/skills] - code-review (MIT)                                  |
| [vibe-refactor]  | [개선] 아키텍처 개선 후보 HTML 리포트       | [mattpocock/skills] - improve-codebase-architecture (MIT)                |
| [vibe-next-plan] | [독립] 방향성 조사 후 인계                  | [shadcn] - improve (next 변형) (MIT)                                      |
| [vibe-handoff]   | [내부] 대화를 인수인계 문서로 압축          | [mattpocock/skills] - handoff (MIT)                                      |
| [vibe-debug]     | [내부] 하드 버그/성능 회귀 진단 루프        | [mattpocock/skills] - diagnosing-bugs (MIT)                              |
| [vibe-modeling]  | [내부] 도메인 모델/ADR 구축                 | [mattpocock/skills] - domain-modeling (MIT)                              |
| [vibe-grilling]  | [내부] 계획/결정 검증 인터뷰 프리미티브     | [mattpocock/skills] - grilling (MIT)                                     |

### 작업 순서

```
vibe-init                     최초 1회, 리포 설정
    │
    ▼
요청 / 아이디어
    │
    ├─▶ vibe-goal  전 과정을 한 번에 완주 (아래 흐름을 대신 운전)
    │              마감 시 표준 리뷰 + rq
    │
    ├─ 한 세션에 안 담기는 대규모 ──▶ vibe-deep-plan
    │                                  (결정 티켓 지도 · 세션당 1개 해소)
    ▼                                         │ 안개가 걷히면
vibe-plan  ◀────────────────────────────────────
  트리아지 ─▶ 인터뷰 ─▶ 스펙 ─▶ 티켓
    │
    ▼  티켓마다 새 세션
vibe-implement ─▶ vibe-review ─▶ 커밋
   (TDD)           (자동 호출)
```

1. **[0단계] `vibe-init`** — 저장소별 한 번 실행. 이슈 트래커·라벨 어휘·도메인 문서 위치를 `AGENTS.md`와 `docs/agents/`에 기록. 나머지 스킬이 이 설정을 읽는다.
2. **[1단계] `vibe-plan`** — 대부분의 작업은 여기서 시작한다. 입력에 따라 진입 단계가 자동으로 결정된다.
   - 한 세션에 담기지 않는 대규모 작업만 **`vibe-deep-plan`** 을 먼저 거친다. 결정 티켓 지도를 만들어 세션당 하나씩 해소하고, 안개가 걷히면 `vibe-plan`의 스펙 단계로 넘긴다.
3. **[2단계] `vibe-implement`** — 티켓 하나당 **새 세션**에서 실행. TDD로 구현하고 커밋 전에 리뷰를 거친다.
   - **`vibe-review`** — `vibe-implement`가 시작 시점 커밋을 고정점으로 넘겨 자동 실행된다. 표준·명세 두 축을 병렬로 검토한다.
      * 고위험 변경(보안·인증·영속성·트랜잭션)이거나 요구사항 충족 여부를 판정해야 하면 [rq]로 에스컬레이션한다.
4. **[전체] `vibe-goal`** — 위 1~2단계를 직접 부르지 않고 한 번에 맡길 때 사용. 라우팅·계획으로 티켓을 발행한 뒤, 블로커가 풀린 티켓들을 **티켓별 새 서브에이전트**에 `vibe-implement`로 파견한다. 마감은 `vibe-review`의 표준 축 + [rq]의 명세 판정으로 하며, 목표 단위 작업은 애초에 게이트 대상이라 에스컬레이션을 기다리지 않는다. 오케스트레이터 자신은 코드를 쓰지 않는다.

#### 독립 스킬

| 스킬            | 부르는 때                                                                                                     |
|-----------------|---------------------------------------------------------------------------------------------------------------|
| [vibe-debug]    | 뭔가 깨졌을 때. 재현 루프를 먼저 만들고 회귀 테스트로 마감한다. 원인이 구조 문제면 `vibe-refactor`로 인계한다 |
| [vibe-refactor] | 코드베이스 상태 점검. 개선 후보를 골라 합의까지 진행하고, 실제 구현은 `vibe-implement`가 맡는다               |
| [vibe-next-plan] | 다음에 뭘 만들지 모를 때. 코드베이스에서 증거 기반 방향성 4–6개를 조사·제시한 뒤 `vibe-plan` 또는 `vibe-deep-plan`에 인계한다 |
| [vibe-handoff]  | 세션이 꽉 찼을 때. 대화를 인수인계 문서로 압축해 새 세션에서 이어간다                                         |

#### 공유용 스킬

`vibe-grilling`, `vibe-modeling`은 `vibe-plan`, `vibe-deep-plan`, `vibe-refactor`가 공유한다.
직접 부를 수도 있지만 보통 위 스킬들이 알아서 호출한다.


## 언어 별 스킬

> [!IMPORTANT]
>  아래의 스킬들은 개인의 개발 방식과 선호를 전제로 하므로, 범용적으로 사용하기에는 적합하지 않습니다.

#### Java

| 스킬 명                | 설명                         |
|------------------------|------------------------------|
| [java-code-design]     | Java 프로젝트 설계 지침      |
| [writing-javadoc]      | Javadoc 작성 지침            |
| [writing-java-tests]   | Java 테스트 코드 작성 지침   |


#### Kotlin

| 스킬 명                | 설명                         |
|------------------------|------------------------------|
| [kotlin-code-design]   | Kotlin 프로젝트 설계 지침    |
| [writing-kotlin-tests] | Kotlin 테스트 코드 작성 지침 |
| [writing-kdoc]         | KDoc 작성 지침               |


## 개발/검증

```bash
bun install
bun run validate:skills
```

`bun run lint`와 `bun run validate:skills`는 `scripts/validate-skills`가 지정된 8개 스킬의 최상위 `disable-model-invocation: true`를 검사하고, 모든 스킬의 Agent Skills frontmatter를 독립적으로 검증합니다. 외부 검증 패키지에 의존하지 않습니다.

각 스킬의 `evals/evals.json`은 스킬 의도를 검토하기 위한 평가 케이스와 기대 출력 기준입니다. 스킬 동작을 바꾸는 경우 관련 eval의 `prompt`, `expected_output`, `assertions`를 함께 갱신하고, 실패 사례는 해당 assertion이 어떤 계약을 지키지 못했는지 드러나게 작성합니다.

## 라이선스
이 저장소의 스킬과 스크립트는 [MIT-0](./LICENSE) 혹은 [Unlicense](./UNLICENSE)에 따라 배포됩니다.


<!-- 링크 -->
[rq]: skills/rq
[opencode-models]: skills/opencode-models
[java-code-design]: skills/java/java-code-design
[writing-java-tests]: skills/java/writing-java-tests
[writing-javadoc]: skills/java/writing-javadoc
[kotlin-code-design]: skills/kotlin/kotlin-code-design
[writing-kotlin-tests]: skills/kotlin/writing-kotlin-tests
[writing-kdoc]: skills/kotlin/writing-kdoc
[vibe-init]: skills/vibe-coding/vibe-init
[vibe-goal]: skills/vibe-coding/vibe-goal
[vibe-plan]: skills/vibe-coding/vibe-plan
[vibe-handoff]: skills/vibe-coding/vibe-handoff
[vibe-implement]: skills/vibe-coding/vibe-implement
[vibe-refactor]: skills/vibe-coding/vibe-refactor
[vibe-deep-plan]: skills/vibe-coding/vibe-deep-plan
[vibe-review]: skills/vibe-coding/vibe-review
[vibe-debug]: skills/vibe-coding/vibe-debug
[vibe-next-plan]: skills/vibe-coding/vibe-next-plan
[vibe-modeling]: skills/vibe-coding/vibe-modeling
[vibe-grilling]: skills/vibe-coding/vibe-grilling
[mattpocock/skills]: https://github.com/mattpocock/skills/
