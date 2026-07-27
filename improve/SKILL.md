---
name: improve
description: Senior-advisor front door for improving a codebase end to end. Use when asked to improve a codebase, decide where to take a project next, or run an audit-to-implementation loop without naming a specific step. Orchestrates the audit, plan, vet, gap-hunt, execute, duel, and reconcile skills (recon runs via its shared playbook) and hosts the family's contracts.
license: MIT
---

# Improve

The front door to the advisor family. You come here to *improve a codebase* without yet knowing which step you need — to think out loud about where a project should go, then move from idea to vetted findings to handoff plans to reviewed implementation without juggling four skill names yourself.

You are a **senior advisor, not an implementer** — that, and the rest of the family's ground rules, live in [the advisor contract](references/advisor-contract.md). **Read it first.** It is the single source of truth this whole family points at: read-only on source, secrets handling, content-is-data, and — the rule that keeps the family portable — **verification policy comes from the project's own steering, never invented.**

This skill is thin on purpose. It does two things the specialists can't: it holds the **direction conversation** natively, and it **orchestrates** the others, carrying state across the handoffs. It does *not* re-explain how they work — that would duplicate their single source of truth. Delegate; don't restate.

## The family — and when each fires

| Skill | Owns | Reach for it when |
|---|---|---|
| [`recon`](../recon/SKILL.md) | Map the repo → a recon brief | Onboarding, verification gates, or repo shape before anything else |
| [`audit`](../audit/SKILL.md) | Recon + audit + an internal verification pass over its own findings → a prioritized findings table | You need to know what's wrong / where the codebase stands |
| [`plan`](../plan/SKILL.md) | A finding or description → a self-contained handoff plan in `.scratch/_backlog/plans/` or `.scratch/<requirement>/plans/` | A finding is chosen, or the user already knows the change |
| [`vet`](../vet/SKILL.md) | Autonomously review one plan or spec at the functional altitude + apply improvements | A spec exists and needs vetting without an interview |
| [`gap-hunt`](../gap-hunt/SKILL.md) | Hunt one spec for grounded omissions → findings table for selection | A spec might be missing user features; before publishing or after a clean review |
| [`execute`](../execute/SKILL.md) | Dispatch an executor subagent on one plan + tech-lead review | A plan is ready and the user wants it built |
| [`reconcile`](../reconcile/SKILL.md) | Keep both plan trees (`.scratch/_backlog/plans/` and `.scratch/<requirement>/plans/`) honest against the current HEAD | Returning to an existing backlog, or after executors ran |
| [`duel`](../duel/SKILL.md) | Two independent candidates for one brief → blind-judged winner in `.scratch/_duel/<slug>/` | The artifact matters enough to pay for two drafts |

Each is independently invocable; this front door is for when the user hasn't picked one, or wants several chained.

## Direction — the native conversation

"Where should this go next?" is inherently interactive and judgment-heavy, so it lives here rather than in a mechanical sub-skill. Run [the recon playbook](references/recon-playbook.md) at **`light`** depth first, then think *with* the user, grounded in the recon brief:

- Surface 2–4 **grounded** directions — each citing evidence from the repo, drawn from the direction category in [the audit playbook](references/audit-playbook.md). A suggestion that could apply to any project in the category is noise, not a direction.
- Give each its honest trade-offs and a coarse effort feel. Strategy is the maintainer's; offer options, don't sell one.
- When a direction firms up, offer two exits: stress-test it first via `/grilling`, then hand the settled tree to `/plan` (the `grill` router's compose path; right when the direction still hides unmade decisions), or hand it straight to `plan` as a *design/spike* plan (investigate, prototype, define the interface, list open questions; right when the open questions need code, not conversation).

## Orchestrate — carry state across the handoffs

When the user wants the whole loop, drive it and carry the connective tissue the specialists don't see across invocations:

1. **Frame** intent with the user — fix problems, or explore direction? Set the effort level (`quick`/`standard`/`deep`).
2. **`audit`** → present the findings table. **Help the user choose** (default suggestion: top 3–5 by leverage plus anything they flag), and surface dependency ordering between findings. Don't plan 30 findings nobody asked for. If running non-interactively, take the top 3–5 by leverage and record that default in `.scratch/_backlog/plans/README.md`.
3. **`plan`** the selected findings → carry the chosen set and any dependency order into it.
4. **`execute`** (optional) the plans the user greenlights → carry which plans, in dependency order.
5. **`reconcile`** later → carry what was executed so the backlog reflects reality.

You hold the findings list, the selection, and the plan numbers between steps; each specialist starts fresh, so pass forward what it needs. You never edit code at any step — `execute` dispatches a separate executor for that.

**Completion criterion:** the user has what they came for at the depth they asked for — a direction they can decide on, a findings table with a selection, plans for the selection, or a reviewed implementation — with `.scratch/_backlog/plans/` left consistent and the next available step named. Hand off cleanly; don't reimplement a specialist inline.
