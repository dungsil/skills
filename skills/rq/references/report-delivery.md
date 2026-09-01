# Report Delivery

Resolve the evidence target and report input separately. Report delivery must not change what code or state is being verified.

## Evidence Target

Classify the user's requested evidence target first:

- `HOSTED_CHANGE`: an explicit pull request or merge request whose diff is being verified.
- `STATE`: a named ref such as `main` or `origin/main`, the whole repository state, or an issue's implementation on that state.

Use the exact target the user named. A remote issue may provide requirements and receive the report while `STATE` remains the evidence target. For `STATE`, do not search open changes, ask for a pull-request or merge-request number, or switch evidence to a change diff.

## Tracker Resolution

1. Read the repository-root `AGENTS.md`.
2. If it points to project issue-tracker guidance, normally `docs/agents/issue-tracker.md`, read that file and use its explicit tracker type.
3. Classify an explicit GitHub tracker as `GITHUB`, an explicit GitLab tracker as `GITLAB`, and Local Markdown or every missing, unreadable, other, or unclear definition as `LOCAL_OR_UNKNOWN`.

An explicit GitHub or GitLab issue, pull-request, or merge-request URL identifies its provider even when project guidance is missing. For short references such as `#18` or `!104`, use project guidance and repository context to resolve the provider and repository. Do not infer a remote report item only from a git remote, installed integration, available CLI, or current branch.

Use the known issue number as the report key. Otherwise derive a short kebab-case key from the requirement.

## Report Input

Identify whether the user supplied one remote item as an input. It may be the requirement source, the evidence target, or both:

- GitHub issue or pull request
- GitLab issue or merge request

Choose the destination by the role each supplied item has:

1. When one supplied pull request or merge request is the evidence target, post to that change even when a remote issue also supplied its requirements.
2. Otherwise, when one supplied remote issue is the requirement source, post to that issue even when a named ref or repository state is the evidence target.
3. If more than one item has the selected role and the user did not choose between them, ask which item should receive the report.

Do not replace an issue with a related change, replace a change with its issue, or search for another hosted item.

## Destination

| Report input | Full report destination |
|---|---|
| One explicit or resolved GitHub issue | Conversation comment on that issue |
| One explicit or resolved GitHub pull request | Conversation comment on that pull request |
| One explicit or resolved GitLab issue | Note or comment on that issue |
| One explicit or resolved GitLab merge request | Note or comment on that merge request |
| No remote issue, pull request, or merge request | `.agents/reports/rq/<report-key>.md` |

For every remote destination:

- Start the report comment with `<!-- rq-report: <report-key> -->`. If that marker already exists on the item, update that comment instead of adding a duplicate.
- Do not create a local report file unless the user or project instructions explicitly request a second copy.
- Post only to the supplied item. Never post to a related or guessed item.

For a local destination, create missing parent directories and keep the existing local-file behavior. Local storage is the intended destination only when no remote report item was supplied; it is not a fallback for failed remote delivery.

## Follow-up and Failure

Keep the initial report, an adversarial addendum, and a revised full report in the same artifact:

- Local: append the addendum to the file or replace that file for a revised report.
- GitHub or GitLab: edit the marked comment to append the addendum or replace its report body for a revised report.

If the local file cannot be saved, the remote item cannot be resolved, or the comment cannot be created or updated, set the overall status to `ERROR`. Do not claim successful delivery or silently fall back to another destination. The final chat response must state only:

- the `ERROR` status
- the risk that the detailed report was not delivered
- the action needed to check the path or issue/pull-request/merge-request target, authentication, and write permission before retrying

After successful delivery, the final chat response still follows the compact output contract in `SKILL.md`.
