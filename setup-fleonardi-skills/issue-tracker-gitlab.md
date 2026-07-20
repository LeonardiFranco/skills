# Issue tracker: GitLab

Issues and specs for this repo live as GitLab issues. Use the [`glab`](https://gitlab.com/gitlab-org/cli) CLI for all operations.

## Conventions

- **Create an issue**: `glab issue create --title "..." --description "..."`. Use a heredoc for multi-line descriptions. Pass `--description -` to open an editor.
- **Read an issue**: `glab issue view <number> --comments`. Use `-F json` for machine-readable output.
- **List issues**: `glab issue list -F json` with appropriate `--label` filters.
- **Comment on an issue**: `glab issue note <number> --message "..."`. GitLab calls comments "notes".
- **Apply / remove labels**: `glab issue update <number> --label "..."` / `--unlabel "..."`. Multiple labels can be comma-separated or by repeating the flag.
- **Close**: `glab issue close <number>`. `glab issue close` does not accept a closing comment, so post the explanation first with `glab issue note <number> --message "..."`, then close.
- **Merge requests**: GitLab calls PRs "merge requests". Use `glab mr create`, `glab mr view`, `glab mr note`, etc. — the same shape as `gh pr ...` with `mr` in place of `pr` and `note`/`--message` in place of `comment`/`--body`.

Infer the repo from `git remote -v` — `glab` does this automatically when run inside a clone.

## Merge requests as a triage surface

**MRs as a request surface: no.** _(Set to `yes` if this repo treats external merge requests as feature requests; `/triage` reads this flag.)_

When set to `yes`, MRs run through the same labels and states as issues, using the `glab mr` equivalents:

- **Read an MR**: `glab mr view <number> --comments` and `glab mr diff <number>` for the diff.
- **List external MRs for triage**: `glab mr list -F json`, then keep only MRs whose author is not a project member/owner (a contributor's MR, not a maintainer's in-flight work).
- **Comment / label / close**: `glab mr note`, `glab mr update --label`/`--unlabel`, `glab mr close`.

Unlike GitHub, GitLab numbers issues and MRs separately, so `#42` is unambiguous once you know which surface the maintainer means.

## When a skill says "publish to the issue tracker"

Create a GitLab issue.

## When a skill says "publish a plan to the issue tracker"

Mirror the plan file 1:1 as a GitLab issue — the plan file stays the source of truth; the issue is distribution.

1. Preflight: `glab auth status` and a GitLab remote. If either fails, skip publish and say why.
2. **Sensitive plans**: before publishing plans describing vulnerabilities, credential locations, or other sensitive findings, check project visibility. If the project is public, warn and get explicit confirmation first.
3. Per plan: `glab issue create --title "<plan title>" --description "$(cat <plan file>)"` (use a heredoc for multi-line bodies).
4. Apply labels `plan` and the plan Status `Category` value only if they already exist (skip rather than fail).
5. Record the returned issue URL in the plan's Status block and the plans README.

## When a skill says "fetch the relevant ticket"

Run `glab issue view <number> --comments`.

## Wayfinding operations (`/wayfinder`)

- **Map**: a GitLab issue labelled `wayfinder:map`; its description holds Notes, Decisions so far, and Fog. Open tickets are **not** listed on the map — find them by query.
- **Ticket**: a child issue linked to the map (reference the map issue in the description or use GitLab issue links). Description holds only `## Question` until resolution.
- **Type labels**: `wayfinder:research`, `wayfinder:prototype`, `wayfinder:grilling`, `wayfinder:task`
- **Claiming**: assign yourself **before any work** (`glab issue update <number> --assignee <you>`) — the assignee *is* the claim; then re-read to confirm the assignee is you, and back off to another ticket if it isn't. Never reassign or unassign someone else's claim, however stale — the human releases claims
- **Blocking**: GitLab blocked-by issue links (`/blocks`, `/blocked_by` in descriptions or native linking)
- **Frontier query**: list open, **unassigned** child issues of the map, unblocked per native semantics
- **Resolution**: post the answer as a note, close the issue, append a one-line gist to the map description's Decisions so far
- **Create-then-wire**: create all ticket issues first, then add blocked-by links in a second pass
