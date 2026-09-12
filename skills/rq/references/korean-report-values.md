# 한국어 보고서 표시값

내부 판단과 집계에는 정식 값을 유지한다. 사용자가 정식 토큰을 명시적으로 요청하지 않으면 한국어 보고서에는 아래 한국어 표시값만 출력한다.

| 분류 | 정식 값 | 한국어 표시값 |
|---|---|---|
| 종합 상태 | `PASS`, `WARNING`, `FAIL`, `NEEDS_REVIEW`, `NO_CHANGES_FOUND`, `ERROR` | `통과`, `경고`, `실패`, `검토 필요`, `관련 변경 없음`, `오류` |
| 검토 깊이 | `LIGHT`, `HEAVY` | `경량 검토`, `심층 검토` |
| 증거 경계 | `CODE`, `RUNTIME`, `MIXED` | `코드 증거`, `실행 증거`, `혼합 증거` |
| 증거 영역 | `CODE`, `TEST`, `MIGRATION`, `OPERATION`, `DEPLOYMENT`, `DATA`, `MANUAL` | `코드`, `테스트`, `마이그레이션`, `운영 실행`, `배포`, `데이터`, `수동 검증` |
| 기준 상태 | `satisfied`, `satisfied with risk`, `not satisfied`, `unknown`, `OUT_OF_SCOPE`, `SEPARATE_GATE`, `not applicable` | `충족`, `위험 동반 충족`, `미충족`, `확인 불가`, `범위 밖`, `별도 게이트`, `해당 없음` |
| 범위와 영향 | `true`, `false`, `included`, `none` | `포함`, `제외`, `종합 판정 반영`, `영향 없음` |
| 불리언과 심각도 | `yes`, `no`; `critical`, `high`, `medium`, `low`, `none` | `예`, `아니요`; `치명적`, `높음`, `중간`, `낮음`, `없음` |

게이트 종류, 연결 상태, 표의 값과 제목을 일관되게 번역한다. 같은 보고서에서 한국어 표시값과 정식 토큰을 섞지 않는다.
