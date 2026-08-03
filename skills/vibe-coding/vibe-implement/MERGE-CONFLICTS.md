# Resolving Merge or Rebase Conflicts

Policy for a separately requested or caller-owned in-progress merge or rebase. A caller-owned ticket agent never uses this process to integrate; its caller owns integration. Standalone completion never starts a merge automatically.

1. **Establish the operation and scope.** Inspect the current merge or rebase state, relevant history, and every conflicting file. Trace the primary intent to commit messages, pull requests, and original tickets. Name the intended operation and the exact target paths before editing.

2. **Stop on unsafe or ambiguous state.** Stop automatic integration and report the state if unrelated tracked or untracked changes appear, including user or secret files, or if primary intent is unclear. Never guess a resolution, stage everything, clean or discard user state, force continuation, or include user or secret files. Preserve the source and result branches and commits when the operation cannot safely finish.

3. **Resolve only clear, established intent.** For each named target path, retain compatible established intent from both sides. When intent is incompatible, choose only the behavior that matches the documented merge goal and record the trade-off; never invent behavior.

4. **Stage only named target paths.** Stage only the exact resolved target paths for this operation—never `git add -A`, unrelated files, user files, or secret files.

5. **Check and finish the intended operation.** Run the project's relevant checks. Continue only the operation inspected in step 1 (`git merge --continue` for a merge or `git rebase --continue` for a rebase), and only after those checks pass. If a new conflict, unsafe state, or uncertainty appears, stop and report rather than force continuation; preserve the source and result branches and commits.
