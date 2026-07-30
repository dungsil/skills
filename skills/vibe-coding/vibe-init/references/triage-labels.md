# Labels

The skills speak in terms of canonical roles. This file maps those roles to the actual label strings used in this repo's issue tracker.

## Triage state roles

Five states an incoming request moves through, used by `vibe-plan`'s triage stage.

| Canonical role     | Label in our tracker | Meaning                                  |
| ------------------ | -------------------- | ---------------------------------------- |
| `needs-triage`     | `상태:분류필요`      | Maintainer needs to evaluate this issue  |
| `needs-info`       | `상태:정보필요`      | Waiting on reporter for more information |
| `ready-for-agent`  | `상태:에이전트작업`  | Fully specified, ready for an AFK agent  |
| `ready-for-human`  | `상태:사람작업`      | Requires human implementation            |
| `wontfix`          | `상태:처리안함`      | Will not be actioned                     |

When a skill mentions a role (e.g. "apply the AFK-ready triage label"), use the corresponding label string from this table.

Edit the right-hand column to match whatever vocabulary you actually use.

## Planning labels

Used by `vibe-deep-plan` when charting a decision map. The map label marks the effort's index issue; the type label records how a decision ticket gets resolved.

| Canonical role | Label in our tracker | Meaning |
| -------------- | -------------------- | ------- |
| map | `상태:초안` | This issue is a decision map — the effort's index, not work to do |
| research | `유형:조사` | Resolved by a background subagent reading primary sources |
| prototype | `유형:프로토타입` | Resolved by a throwaway artifact to react to |
| grilling | `유형:인터뷰` | Resolved by one-question-at-a-time conversation |
| task | `유형:작업` | Manual work that must happen before a decision can be made |

## The two axes

`상태:` answers "what state is this in", `유형:` answers "how does this get resolved". An issue carries **at most one label per axis**.

`상태:초안` sits on the same axis as the triage states, so it excludes them: a decision map is an in-flight planning artifact, never a request awaiting evaluation, and it is never triaged. Its decision tickets carry only a `유형:` label for the same reason. Triage states apply again once the map clears and `vibe-plan` publishes implementation tickets.
