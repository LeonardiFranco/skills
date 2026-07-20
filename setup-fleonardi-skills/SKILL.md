---
name: setup-fleonardi-skills
description: Configure this repo for the engineering skills — issue tracker, triage labels, and domain doc layout. Default writes personal config to docs/agents/local/; pass team to write committed team config under docs/agents/.
disable-model-invocation: true
---

# Setup fleonardi Skills

Scaffold the per-repo configuration that the engineering skills assume:

- **Issue tracker** — where issues live (GitHub, GitLab, Azure DevOps, local markdown, or custom)
- **PR host** — where pull requests live and the commands to drive them (may differ from the
  tracker: a repo can track issues in local markdown yet host PRs on Azure DevOps). Consumed by
  `/execute`'s publish-on-approve tail and `/review-pr`
- **Triage labels** — the strings used for the five canonical triage roles
- **Domain docs** — where `CONTEXT.md` and ADRs live, and the consumer rules for reading them
- **Preferences** (personal only) — optional `local/preferences.md` for environment notes; team repo commits `docs/agents/preferences.example.md` only
- **Scratch workspace** — gitignored `.scratch/` tree with master index and empty backlog plans index

**Steering boundary:** `AGENTS.md` and `CLAUDE.md` are team-owned project steering. This skill **never edits them** — author or refresh `AGENTS.md` with `/init-agents`.

**Config boundary:** skill wiring lives under `docs/agents/`. By default this skill writes **personal** config to `docs/agents/local/` (gitignored). Pass **`team`** to write **committed** team defaults to `docs/agents/` instead.

Invoke as:

- **`/setup-fleonardi-skills`** — personal config → `docs/agents/local/` (default)
- **`/setup-fleonardi-skills team`** — team config → `docs/agents/` (commit these)

This is a prompt-driven skill, not a deterministic script. Explore, present what you found, confirm with the user, then write.

## Process

### 0. Resolve scope from invocation

| Invocation | Write target | Gitignored? |
|------------|--------------|-------------|
| `/setup-fleonardi-skills` (no args) | `docs/agents/local/` | yes |
| `/setup-fleonardi-skills team` | `docs/agents/` (not `local/`) | no — commit |

Consumer skills prefer `docs/agents/local/<name>.md` over `docs/agents/<name>.md` when both exist. Full layout: [readme.md](./readme.md) in this skill folder (not written to the repo).

Ensure `.gitignore` contains:

```
docs/agents/local/
.scratch/
```

### 1. Explore

Look at the current repo to understand its starting state. Read whatever exists; don't assume:

- `git remote -v` and `.git/config` — GitHub? GitLab? Azure DevOps remote?
- `AGENTS.md` and `CLAUDE.md` at the repo root — read for project context only; **do not plan edits**. Note whether `AGENTS.md` has a **Local overrides** section pointing at `docs/agents/local/preferences.md` (agents load personal prefs only via that pointer).
- `CONTEXT.md` and `CONTEXT-MAP.md` at the repo root
- `docs/adr/` and any `src/*/docs/adr/` directories
- `docs/agents/` and `docs/agents/local/` — prior setup output? (`preferences.md` is personal-only)
- `.scratch/` — see `.scratch/README.md`: `<requirement>/` (spec, tickets, issues, plans) and `_backlog/plans/` for cross-cutting advisor work
- `.azuredevops/` or Azure DevOps references in CI — sign of Azure DevOps Boards

### 2. Present findings and ask

Summarise what's present and what's missing. State which scope you're writing (**personal** vs **team**) from the invocation. Walk the user through the three decisions **one at a time**.

**Section A — Issue tracker.**

> Explainer: The "issue tracker" is where issues live for this repo. Skills like `plan`, `to-tickets`, `triage`, `to-spec`, and `wayfinder` read from and write to it — they need to know whether to call `gh issue create`, `az boards work-item create`, write markdown under `.scratch/`, or follow some other workflow. Each tracker template includes a **Wayfinding operations** section for `/wayfinder`.

Default posture: infer from the repo. If `.azuredevops/` or existing team `docs/agents/issue-tracker.md` mentions Azure DevOps, propose that. If a `git remote` points at GitHub, propose GitHub. If GitLab, propose GitLab. A code host doesn't rule out Linear — many teams host code on GitHub but track work in Linear, so still offer it. Otherwise offer:

- **GitHub** — `gh` CLI
- **GitLab** — `glab` CLI
- **Azure DevOps Boards** — `az boards` with the `azure-devops` extension
- **Linear** — Linear MCP server (no CLI; OAuth-gated tools)
- **Local markdown** — specs in `.scratch/<requirement>/SPEC.md`, tickets in `.scratch/<requirement>/tickets.md`, issues in `.scratch/<requirement>/issues/`
- **Other** — freeform prose from the user

For **GitHub** or **GitLab** only, ask whether external PRs/MRs are a triage surface (default: no).

**Section A2 — PR host.**

> Explainer: independent of where issues live, `/execute`'s publish-on-approve tail and
> `/review-pr` need the commands to drive this repo's pull requests — create a draft PR, read
> metadata, list and post comment threads, fetch the merge ref. Skills carry no host commands
> themselves; this section is where they resolve them at runtime.

Infer from `git remote -v` (`dev.azure.com` → Azure DevOps, `github.com` → GitHub, GitLab
likewise) and confirm. Written as a `## PR host: <name>` section **appended to
`issue-tracker.md`** (same file, distinct section — the tracker and the PR host are orthogonal
choices). Seeds: [pr-host-azure-devops.md](./pr-host-azure-devops.md),
[pr-host-github.md](./pr-host-github.md); other hosts: derive the same operation table from the
host's CLI and mark unverified commands for first-use verification. Also capture any team
description conventions (length caps, sections posted as comments). Every seed keeps the
**never vote/approve** rule — the CLI authenticates as the human.

No PR flow wanted, or no remote host? Record `PR host: none` so consumer skills stop cleanly
instead of guessing.

**Section B — Triage label vocabulary.**

> Explainer: When `/triage` processes an incoming issue it moves through a state machine — needs evaluation, waiting on reporter, ready for an AFK agent, ready for a human, or won't-fix. It applies labels (or your tracker's equivalent) that must match strings you've actually configured. If this repo already uses different names (e.g. `bug:triage` instead of `needs-triage`), map them here so triage applies the right ones instead of creating duplicates.

The five canonical roles: `needs-triage`, `needs-info`, `ready-for-agent`, `ready-for-human`, `wontfix`. Default: each role's string equals its name. Ask if any should map to different label/tag strings in the actual tracker.

**Section C — Domain docs.**

> Explainer: Some skills (`improve-architecture`, `tdd`) read a `CONTEXT.md` for the project's domain language and `docs/adr/` for past decisions. They need to know whether this repo has one global context or several (e.g. a monorepo with separate frontend/backend contexts) so they look in the right place.

- **Single-context** — one `CONTEXT.md` + `docs/adr/` at the repo root (most repos)
- **Multi-context** — `CONTEXT-MAP.md` at the root pointing to per-context `CONTEXT.md` files

### 3. Confirm and edit

Show drafts for:

- `docs/agents/preferences.example.md` (team template; skip if present and user-edited)
- The three config files in the write target (`issue-tracker.md`, `triage-labels.md`, `domain.md`) under `docs/agents/local/` **or** `docs/agents/` per scope
- `.scratch/README.md` and `.scratch/_backlog/plans/README.md` (if missing)

For **personal** scope only: offer to copy `preferences.example.md` → `docs/agents/local/preferences.md` if the user wants environment notes. Do not write personal content without asking. When a PR host was configured, also offer the **publish-on-approve opt-in** for `preferences.md` (consumed by `/execute`: on APPROVE — commit in the worktree, push, `write-pr`, publish a draft PR per the PR host section; never on REVISE/BLOCK; draft→active stays human).

### 4. Write

Determine **`$TARGET`**: `docs/agents/local` (default) or `docs/agents` (when invoked with `team`).

Write (or update):

- `docs/agents/preferences.example.md` (team template; skip if present and user-edited)
- Issue tracker seed → `$TARGET/issue-tracker.md` (pick github / gitlab / azure-devops / linear / local / custom)
- PR host section (Section A2) → **appended to** `$TARGET/issue-tracker.md`
- [triage-labels.md](./triage-labels.md) → `$TARGET/triage-labels.md`
- [domain.md](./domain.md) → `$TARGET/domain.md`

Seed templates in this folder: `issue-tracker-github.md`, `issue-tracker-gitlab.md`, `issue-tracker-azure-devops.md`, `issue-tracker-linear.md`, `issue-tracker-local.md`; PR host: `pr-host-azure-devops.md`, `pr-host-github.md`.

**Scratch workspace** (personal, gitignored — write regardless of personal vs team scope):

- [scratch-readme.md](./scratch-readme.md) → `.scratch/README.md` (skip if present and user-edited)
- [scratch-backlog-plans-readme.md](./scratch-backlog-plans-readme.md) → `.scratch/_backlog/plans/README.md` (skip if present and user-edited)

Create parent directories as needed. Do not delete existing plan files or requirement folders.

If files already exist in the target, update in place. Do not delete user additions outside the canonical three (`issue-tracker`, `triage-labels`, `domain`) or personal `preferences.md`.

### 5. Done

Tell the user:

- Which scope was written (`local` vs team) and where consumer skills will read from
- Personal config in `docs/agents/local/` is gitignored; team config in `docs/agents/` should be committed
- Copy `docs/agents/preferences.example.md` → `docs/agents/local/preferences.md` for personal environment notes
- Scratch workspace lives under `.scratch/` (gitignored); master index at `.scratch/README.md`
- Re-run with `team` to refresh committed team defaults, or without args to refresh personal overrides
- **Handshake:** if `AGENTS.md` lacks a **Local overrides** section pointing at
  `docs/agents/local/preferences.md`, tell the user to run **`/init-agents`** refresh — do
  not edit `AGENTS.md` yourself
