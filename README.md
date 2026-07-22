# DUNGSIL's Agent Skills

개인용 [에이전트 스킬](https://agentskills.io/home) 모음집

## 설치
```
pnpm dlx skills add dungsil/skills -g
```

## 스킬 목록

## 공통

|            스킬 명          | 설명          |
|:--------------------------:|-------------|
| [requirement-quality-gate] | 요구사항 품질 게이트 |


### Java/Kotlin/Spring

> [!IMPORTANT] 
>  아래의 스킬들은 개인의 개발 방식과 선호를 전제로 하므로, 범용적으로 사용하기에는 적합하지 않습니다.


|         스킬 명         | 설명                |
|:--------------------:|-------------------|
|  [java-code-design]  | Java 프로젝트 설계 지침   |
| [kotlin-code-design] | Kotlin 프로젝트 설계 지침 |
| [writing-java-tests] | Java 테스트 코드 작성 지침 |
|  [writing-javadoc]   | Javadoc 작성 지침     |
|    [writing-kdoc]    | KDoc 작성 지침        |



## 개발/검증

```bash
bun install
bun run validate:skills
```

`bun run lint`와 `bun run validate:skills`는 모두 `intent validate skills --check`를 실행합니다. 현재 CI의 통과 기준도 같은 형식/스키마 검증입니다.

각 스킬의 `evals/evals.json`은 스킬 의도를 검토하기 위한 평가 케이스와 기대 출력 기준입니다. 스킬 동작을 바꾸는 경우 관련 eval의 `prompt`, `expected_output`, `assertions`를 함께 갱신하고, 실패 사례는 해당 assertion이 어떤 계약을 지키지 못했는지 드러나게 작성합니다.

## 라이선스
이 저장소의 스킬과 스크립트는 [MIT-0](./LICENSE) 혹은 [Unlicense](./UNLICENSE)에 따라 배포됩니다.


<!-- 링크 -->
[requirement-quality-gate]: skills/requirement-quality-gate
[java-code-design]: skills/java-code-design
[kotlin-code-design]: skills/kotlin-code-design
[writing-java-tests]: skills/writing-java-tests
[writing-javadoc]: skills/writing-javadoc
[writing-kdoc]: skills/writing-kdoc
