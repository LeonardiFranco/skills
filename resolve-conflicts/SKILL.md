---
name: resolve-conflicts
description: "Use when you need to resolve an in-progress git merge/rebase conflict."
---

# Resolve Merge Conflicts

1. **See the current state** of the merge/rebase. Check git history, and the conflicting files.

2. **Find the primary sources** for each conflict. Understand deeply why each change was made, and what the original intent was. Read the commit messages, check the PRs, check original issues/tickets.

3. **Resolve each hunk.** Preserve both intents where possible. Where incompatible, pick the one matching the merge's stated goal and note the trade-off. Do **not** invent new behaviour. Always resolve; never `--abort`.

4. **Verify the way this project verifies — don't assume a typecheck/test/format loop exists.** Read the project's steering (`AGENTS.md` / `CLAUDE.md`, CI config) for how it checks work, and follow that. For a read-first project with no automated suite, that means reading the merged result for correctness and naming what the human should verify — not fabricating a green build. Fix anything the merge broke.

5. **Finish the merge/rebase.** Stage everything and complete it, using the project's commit-message conventions. If rebasing, continue until all commits are rebased. Don't push — that's the user's call.
