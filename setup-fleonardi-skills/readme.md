# Agent skills configuration

Human-facing layout for `/setup-fleonardi-skills`. **Not copied into the repo.** Scope, invocation, and precedence: [SKILL.md](./SKILL.md); `.scratch/` layout: [scratch-readme.md](./scratch-readme.md).

## Files

| File | Purpose |
|------|---------|
| `issue-tracker.md` | Where issues live and how to create, query, and update them; when configured, a **PR host** section (host commands for `/publish-pr` and `/review-pr`). Plans/changes live under `openspec/changes/`. |
| `triage-labels.md` | Triage role → label/tag mapping |
| `domain.md` | Where `CONTEXT.md` and ADRs live |
| `preferences.example.md` | Template for personal `local/preferences.md` — copy, don't commit |

### Personal only (`docs/agents/local/`)

| File | Purpose |
|------|---------|
| `preferences.md` | Environment notes. Read via `AGENTS.md`; skip if missing. |
| `issue-tracker.md` | Overrides team issue tracker config |
| `triage-labels.md` | Overrides team triage labels |
| `domain.md` | Overrides team domain doc layout |

Edit config files directly. Re-run setup only to switch issue trackers or reset from scratch.

## Canonical consumer preamble

Consumer skills carry this identical wording (specifics of which configs they need may follow it):

> Before acting, read the config this skill needs from `docs/agents/` — prefer
> `docs/agents/local/<name>.md` over `docs/agents/<name>.md` when both exist. If
> missing or incomplete, ask the user to run `/setup-fleonardi-skills`.
