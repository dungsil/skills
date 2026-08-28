# Report Delivery

Resolve the review target before the report destination. Report delivery must not change what code or state is being verified.

## Review Target

Classify the user's requested review target first:

- `HOSTED_CHANGE`: an explicit pull request or merge request, or a branch/change review that the user wants reported on its corresponding hosted change.
- `STATE`: a named ref such as `main` or `origin/main`, the whole repository state, or an issue's implementation on that state.

Use the exact target the user named. An issue number identifies requirements and the report key; it does not imply `HOSTED_CHANGE`. For `STATE`, do not search open changes, ask for a pull-request or merge-request number, or switch evidence to a change diff.

## Tracker Resolution

1. Read the repository-root `AGENTS.md`.
2. If it points to project issue-tracker guidance, normally `docs/agents/issue-tracker.md`, read that file and use its explicit tracker type.
3. Classify an explicit GitHub tracker as `GITHUB`, an explicit GitLab tracker as `GITLAB`, and Local Markdown or every missing, unreadable, other, or unclear definition as `LOCAL_OR_UNKNOWN`.

Do not infer GitHub or GitLab only from a git remote, installed integration, available CLI, or review URL. Those signals can locate a report target after the project guidance selects a hosted tracker, but they do not override that guidance.

Use the known issue number as the report key. Otherwise derive a short kebab-case key from the requirement.

## Destination

| Review target | Tracker class | Full report destination |
|---|---|---|
| `STATE` | Any | `.agents/reports/rq/<report-key>.md` |
| `HOSTED_CHANGE` | `LOCAL_OR_UNKNOWN` | `.agents/reports/rq/<report-key>.md` |
| `HOSTED_CHANGE` | `GITHUB` | Conversation comment on the verified corresponding pull request |
| `HOSTED_CHANGE` | `GITLAB` | Note or comment on the verified corresponding merge request |

For a GitHub or GitLab `HOSTED_CHANGE`:

- Resolve the target from an explicit pull-request or merge-request reference first, then from the reviewed branch or change when the provider confirms exactly one match.
- Never post the report to an issue, another open change, or a guessed target. Ask for the target when none or more than one remains possible.
- Start the report comment with `<!-- rq-report: <report-key> -->`. If that marker already exists on the target, update that comment instead of adding a duplicate.
- Do not also create the local report file unless the user or project instructions request a second copy.

For every local destination, create missing parent directories and keep the existing local-file behavior. A local report for `STATE` is the intended destination, not a fallback for an unresolved hosted target.

## Follow-up and Failure

Keep the initial report, an adversarial addendum, and a revised full report in the same artifact:

- Local: append the addendum to the file or replace that file for a revised report.
- GitHub or GitLab: edit the marked comment to append the addendum or replace its report body for a revised report.

If the local file cannot be saved, the hosted target cannot be resolved, or the comment cannot be created or updated, set the overall status to `ERROR`. Do not claim successful delivery or silently fall back to another destination. The final chat response must state only:

- the `ERROR` status
- the risk that the detailed report was not delivered
- the action needed to check the path or pull-request/merge-request target, authentication, and write permission before retrying

After successful delivery, the final chat response still follows the compact output contract in `SKILL.md`.
