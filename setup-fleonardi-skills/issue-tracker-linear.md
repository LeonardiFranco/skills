# Issue tracker: Linear

Issues and specs for this repo live in **Linear**. Linear has no first-class CLI like `gh`/`glab`; the engineering skills reach it through the **Linear MCP server** (`mcp.linear.app`).

- **Workspace**: `<workspace>`
- **Team**: `<Team Name>` — issue prefix `<KEY>` (identifiers look like `KEY-123`)

Linear identifies an issue by a team-scoped key like `ENG-123`, not a repo-wide number. Always reference issues by that full identifier.

## One-time setup

The Linear MCP tools are OAuth-gated and load only after authentication:

1. Call `mcp__claude_ai_Linear__authenticate` and share the returned URL with the user.
2. After they authorize, pass the callback URL to `mcp__claude_ai_Linear__complete_authentication`.
3. The operational tools then load automatically. If a tool named below isn't loaded, find it with `ToolSearch` (`query: "Linear <verb>"`) — Linear may rename tools, so let discovery settle the exact name.

## Conventions

These are the official Linear MCP tools; confirm a name with `ToolSearch` if a call is rejected.

- **Create an issue**: `create_issue` — `title`, `description` (markdown), `team`, optional `labels`/`state`/`parentId`.
- **Read an issue**: `get_issue` by identifier (`ENG-123`).
- **List issues**: `list_issues` filtered by `team`, `label`, `state`, or `assignee`; `list_my_issues` for the current user's queue.
- **Comment**: `create_comment` with the issue id and markdown `body`; `list_comments` to read the thread. Append history as comments — don't mutate the description.
- **Labels**: `update_issue` with the `labels` set. Linear **replaces** the set, it doesn't append — read the current labels first and rewrite the whole set so unrelated ones survive.
- **Close**: Linear has no separate close — completion is a workflow state. `update_issue` with `state` set to a completed/canceled state (e.g. `Done`, `Canceled`).
- **Resolve workspace strings**: `list_teams`, `list_issue_labels`, `list_issue_statuses` — use these to get the exact team/label/state strings this workspace uses before creating or filtering.

## Triage state

Triage roles (`triage-labels.md`) map to Linear **labels**, updated via `update_issue` (rewrite the whole label set, per above). Linear also has a native per-team **Triage** inbox; if this team uses it, unevaluated incoming issues land there, which is the natural home for the `needs-triage` role.

## Pull requests

Linear doesn't host pull requests — code review lives in the GitHub/GitLab repo and links back to Linear issues via Linear's git integration. If external PRs are a triage surface for this repo, that belongs to the code host's tracker config, separate from Linear issue triage.

## When a skill says "publish to the issue tracker"

Create a Linear issue with `create_issue` on the configured team. For a spec or multi-ticket breakdown, create one parent issue and attach each slice as a sub-issue (`parentId`).

## When a skill says "publish a plan to the issue tracker"

Mirror the plan file 1:1 as a Linear issue — the plan file stays the source of truth; the issue is distribution.

1. Preflight: Linear MCP authenticated. If not, skip publish and say why.
2. **Sensitive plans**: before publishing plans describing vulnerabilities, credential locations, or other sensitive findings, warn and get explicit confirmation when the workspace or team is publicly visible.
3. Per plan: `create_issue` with the plan title, full plan file contents as `description`, on the configured team.
4. Apply labels `plan` and the plan Status `Category` value only if they already exist (skip rather than fail).
5. Record the returned identifier (e.g. `KEY-123`) in the plan's Status block and the plans README.

## When a skill says "fetch the relevant ticket"

Call `get_issue` with the identifier (`ENG-123`). The user will normally pass the identifier or a Linear URL containing it.

## Wayfinding operations (`/wayfinder`)

- **Map**: a Linear issue labelled `wayfinder:map`; its description holds Notes, Decisions so far, and Fog. Open tickets are **not** listed on the map — find them by query.
- **Ticket**: a sub-issue of the map (`parentId` set to the map issue). Description holds only `## Question` until resolution.
- **Type labels**: `wayfinder:research`, `wayfinder:prototype`, `wayfinder:grilling`, `wayfinder:task`
- **Claiming**: assign yourself **before any work** (`update_issue` with `assignee`) — the assignee *is* the claim; then re-read (`get_issue`) to confirm the assignee is you, and back off to another ticket if it isn't. Never reassign someone else's claim, however stale — the human releases claims
- **Blocking**: Linear `blockedBy` / `blocks` relations between issues
- **Frontier query**: `list_issues` filtered to sub-issues of the map in an open state with **no assignee**, unblocked per native semantics
- **Resolution**: `create_comment` with the answer, move to a completed state, append a one-line gist to the map description's Decisions so far
- **Create-then-wire**: create all ticket issues first, then add blocking relations in a second pass
