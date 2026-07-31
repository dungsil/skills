# Korean Report Values

Keep canonical values for internal reasoning and aggregation. In a Korean report, output only the Korean display values below unless the user explicitly asks for canonical tokens.

| Category | Canonical values | Korean display values |
|---|---|---|
| Overall status | `PASS`, `WARNING`, `FAIL`, `NEEDS_REVIEW`, `NO_CHANGES_FOUND`, `ERROR` | `통과`, `경고`, `실패`, `검토 필요`, `관련 변경 없음`, `오류` |
| Review tier | `LIGHT`, `HEAVY` | `경량 검토`, `심층 검토` |
| Evidence boundary | `CODE`, `RUNTIME`, `MIXED` | `코드 증거`, `실행 증거`, `혼합 증거` |
| Evidence domain | `CODE`, `TEST`, `MIGRATION`, `OPERATION`, `DEPLOYMENT`, `DATA`, `MANUAL` | `코드`, `테스트`, `마이그레이션`, `운영 실행`, `배포`, `데이터`, `수동 검증` |
| Criterion status | `satisfied`, `satisfied with risk`, `not satisfied`, `unknown`, `OUT_OF_SCOPE`, `SEPARATE_GATE`, `not applicable` | `충족`, `위험 동반 충족`, `미충족`, `확인 불가`, `범위 밖`, `별도 게이트`, `해당 없음` |
| Scope and impact | `true`, `false`, `included`, `none` | `포함`, `제외`, `종합 판정 반영`, `영향 없음` |
| Boolean and severity | `yes`, `no`; `critical`, `high`, `medium`, `low`, `none` | `예`, `아니요`; `치명적`, `높음`, `중간`, `낮음`, `없음` |

Translate gate types, mapping statuses, table values, and section labels consistently. Do not mix Korean display values with canonical tokens in the same report.
