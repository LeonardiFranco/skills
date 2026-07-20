# Issue tracker: GitHub

Issues and specs for this repo live as GitHub issues. Use the `gh` CLI for all operations.

## Conventions

- **Create an issue**: `gh issue create --title "..." --body "..."`. Use a heredoc for multi-line bodies.
- **Read an issue**: `gh issue view <number> --comments`, filtering comments by `jq` and also fetching labels.
- **List issues**: `gh issue list --state open --json number,title,body,labels,comments --jq '[.[] | {number, title, body, labels: [.labels[].name], comments: [.comments[].body]}]'` with appropriate `--label` and `--state` filters.
- **Comment on an issue**: `gh issue comment <number> --body "..."`
- **Apply / remove labels**: `gh issue edit <number> --add-label "..."` / `--remove-label "..."`
- **Close**: `gh issue close <number> --comment "..."`

Infer the repo from `git remote -v` — `gh` does this automatically when run inside a clone.

## Pull requests as a triage surface

**PRs as a request surface: no.** _(Set to `yes` if this repo treats external PRs as feature requests; `/triage` reads this flag.)_

When set to `yes`, PRs run through the same labels and states as issues, using the `gh pr` equivalents:

- **Read a PR**: `gh pr view <number> --comments` and `gh pr diff <number>` for the diff.
- **List external PRs for triage**: `gh pr list --state open --json number,title,body,labels,author,authorAssociation,comments` then keep only `authorAssociation` of `CONTRIBUTOR`, `FIRST_TIME_CONTRIBUTOR`, or `NONE` (drop `OWNER`/`MEMBER`/`COLLABORATOR`).
- **Comment / label / close**: `gh pr comment`, `gh pr edit --add-label`/`--remove-label`, `gh pr close`.

GitHub shares one number space across issues and PRs, so a bare `#42` may be either — resolve with `gh pr view 42` and fall back to `gh issue view 42`.

## When a skill says "publish to the issue tracker"

Create a GitHub issue.

## When a skill says "publish a plan to the issue tracker"

Mirror the plan file 1:1 as a GitHub issue — the plan file stays the source of truth; the issue is distribution.

1. Preflight: `gh auth status` and a GitHub remote. If either fails, skip publish and say why.
2. **Sensitive plans**: before publishing plans describing vulnerabilities, credential locations, or other sensitive findings, run `gh repo view --json visibility`. If the repo is public, warn and get explicit confirmation first.
3. Per plan: `gh issue create --title "<plan title>" --body-file <plan file>`.
4. Apply labels `plan` and the plan Status `Category` value only if they already exist (skip rather than fail).
5. Record the returned issue URL in the plan's Status block and the plans README.

## When a skill says "fetch the relevant ticket"

Run `gh issue view <number> --comments`.

## Wayfinding operations (`/wayfinder`)

- **Map**: a GitHub issue labelled `wayfinder:map`; its body holds Notes, Decisions so far, and Fog. Open tickets are **not** listed on the map — find them by query.
- **Ticket**: a child issue of the map (link via GitHub sub-issue / parent-child or reference the map issue number in the ticket body). Body holds only `## Question` until resolution.
- **Type labels**: `wayfinder:research`, `wayfinder:prototype`, `wayfinder:grilling`, `wayfinder:task`
- **Claiming**: assign yourself **before any work** (`gh issue edit <number> --add-assignee @me`) — the assignee *is* the claim; then re-read (`gh issue view <number> --json assignees`) to confirm the assignee is you, and back off to another ticket if it isn't. Never reassign or unassign someone else's claim, however stale — the human releases claims
- **Blocking**: GitHub native blocked-by issue links
- **Frontier query**: list open, **unassigned** child issues of the map, unblocked per native semantics — e.g. `gh issue list --state open --search "no:assignee" --json number,title,labels,assignees` filtered to children of the map
- **Resolution**: post the answer as a comment, close the issue, append a one-line gist to the map body's Decisions so far
- **Create-then-wire**: create all ticket issues first, then add blocked-by links in a second pass (issue numbers must exist before they can reference each other)
