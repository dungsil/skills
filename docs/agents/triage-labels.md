# Labels

The skills speak in terms of canonical roles. This file maps those roles to the strings stored in local work-item files.

## Triage state roles

| Canonical role | Value in local tracker | Meaning |
| --- | --- | --- |
| `needs-triage` | `상태:분류필요` | Maintainer needs to evaluate this issue |
| `needs-info` | `상태:정보필요` | Waiting on reporter for more information |
| `ready-for-agent` | `상태:에이전트작업` | Fully specified, ready for an AFK agent |
| `ready-for-human` | `상태:사람작업` | Requires human implementation |
| `wontfix` | `상태:처리안함` | Will not be actioned |

## Planning labels

| Canonical role | Value in local tracker | Meaning |
| --- | --- | --- |
| map | `상태:초안` | Decision map index |
| research | `유형:조사` | Background primary-source research |
| prototype | `유형:프로토타입` | Throwaway artifact for a human decision |
| grilling | `유형:인터뷰` | One-question-at-a-time conversation |
| task | `유형:작업` | Manual work required before a decision |

## Axes

`상태:` answers what state a work item is in; `유형:` answers how a decision ticket is resolved. A work item carries at most one value per axis. `상태:초안` is mutually exclusive with every triage state, and decision tickets carry only one `유형:` value.
