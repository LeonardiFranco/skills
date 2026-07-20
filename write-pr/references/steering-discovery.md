# Steering discovery

Disclosed reference for [`write-pr`](../SKILL.md). Read this during **Step 1 — Read steering**.

## PR template path

Search order:

1. **`AGENTS.md` / `CLAUDE.md` / `CONTRIBUTING.md`** — look for an explicit PR template path:
   - Phrases: `PR template`, `pull request template`, `PR description: follow`, `pull_request_template`
   - Paths in backticks or markdown links (e.g. `.azuredevops/pull_request_template.md`, `.github/pull_request_template.md`)
2. **Well-known locations** (only if steering is silent):
   - `.github/pull_request_template.md`
   - `.github/PULL_REQUEST_TEMPLATE.md`
   - `.azuredevops/pull_request_template.md`
   - `docs/pull_request_template.md`
3. **Fallback** — use [default-pr-template.md](./default-pr-template.md) in this skill folder.

If the declared path is missing on disk, tell the user and use the fallback.

## Default base branch

Search order:

1. Steering — phrases: `default branch`, `merge target`, `base branch`, `PR against`
2. User argument in the session
3. `git symbolic-ref refs/remotes/origin/HEAD` → `origin/<branch>`
4. Try in order: `dev`, `main`, `master` — first that `git rev-parse --verify` accepts

Diff: `git diff <base>..HEAD` and `git log <base>..HEAD --oneline`.

## Verification policy

Read from steering (especially `AGENTS.md`):

- Whether an automated test suite exists and what CI runs
- Whether build-to-verify is discouraged
- Required manual verification format (e.g. affected surfaces, concrete steps)

**Do not** invent test commands or claim green CI / "tests pass" when steering says otherwise.

## Plan integration (after execute)

When a `.scratch/**/plans/*.md` plan drove the work:

- Pull manual test plan and done criteria into the PR test section
- Match plan **Risk** unless the diff proves otherwise
- Note withdrawn or deferred steps in Notes / Additional Context

## Optional steering convention (for repo maintainers)

Repos can pin discovery in `AGENTS.md`:

```markdown
## Pull requests
- **PR template:** path/to/pull_request_template.md
- **Default base branch:** dev
```

No convention required — the search rules above still apply.
