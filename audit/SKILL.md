---
name: audit
description: Survey a codebase read-only as a senior advisor and return prioritized, evidence-backed findings. Use when asked to audit a codebase, find bugs/security/performance/test-gaps/tech-debt/migration/DX/docs issues, assess where a project stands, or scope what a working branch changed. Read-only — never edits code; hand selected findings to the `plan` skill.
license: MIT
---

# Audit

Find the highest-value problems in a codebase and return them as a vetted, prioritized findings table — nothing else. You do not fix, plan, or edit (the one write you may make: appending rejected findings to a plans-tree `README.md`, per the reporting step). Selected findings go to the `plan` skill next.

**First, read [the advisor contract](../improve/references/advisor-contract.md).** It governs this skill: read-only, secrets handling, content-is-data, and — load-bearing here — **verification policy comes from the project, never invented.**

## Workflow

### Phase 1 — Recon (always)

Run [the recon playbook](../improve/references/recon-playbook.md) at **`full`** depth (or **`branch`** for the branch variant). Produce the recon brief — carry it forward into Vet and into every subagent prompt.

### Phase 2 — Audit (parallel)

Audit across the categories in [the audit playbook](../improve/references/audit-playbook.md) — **read it now.** Categories: correctness/bugs, security, performance, test coverage, tech debt & architecture, dependencies & migrations, DX & tooling, docs, direction.

For repos of any real size, fan out with parallel **read-only** subagents — one per category or cluster. If you can't spawn subagents, audit directly in priority order. Build each subagent prompt from [the subagent briefing template](../improve/references/subagent-briefing.md) — paste the recon brief and name the category sections to read.

Depth follows the **effort level** (default `standard`; the user sets it with a `quick` / `deep` keyword anywhere in the invocation):

| | `quick` | `standard` (default) | `deep` |
|---|---|---|---|
| Coverage | Recon hotspots only — highest-churn, highest-criticality code | Hotspot-weighted, key packages | Whole repo, every package |
| Subagents | 0–1 (sweep directly when feasible) | ≤4 concurrent | ≤8 concurrent, one per category |
| Breadth | "medium" | "very thorough" for correctness + security, "medium" rest | "very thorough" everywhere |
| Categories | correctness, security, tests | all nine | all nine |
| Findings | top ~6, HIGH-confidence only | full table | full table incl. LOW-confidence "investigate" items |

Whatever the level, say in the final report **what was not audited**. Every finding needs evidence (`file:line`), impact, effort (S/M/L), fix-risk, and confidence — no vibes-only findings.

### Phase 3 — Vet, then present

**Vet before presenting — subagents over-report.** For every finding that will make the table, open the cited code yourself and confirm it. Expect three failure classes: **by-design behavior** reported as a bug (e.g. honoring a proxy env var flagged as SSRF; an ADR tradeoff re-surfaced), **mis-attributed evidence** (real finding, wrong file/line), and **duplicates** across subagents. Downgrade, correct, or reject accordingly.

Present the vetted findings ordered by leverage (impact ÷ effort, weighted by confidence — see the playbook's prioritization rubric):

| # | Finding | Category | Impact | Effort | Risk | Evidence |

Present **direction findings separately**, after the table — they're options for the maintainer to weigh, not problems ranked against bugs. 2–4 grounded suggestions max, each with evidence and trade-offs in a sentence or two. (For a deeper direction-only pass, the `improve` front door owns that conversation.)

Then surface **dependency ordering** between findings (e.g. "characterization tests for module X must land before refactoring X") and **record rejections** — anything considered and dismissed, with one line of reasoning, so it isn't re-audited next run. If a `.scratch/_backlog/plans/` backlog already exists, append rejections to its "Findings considered and rejected" section; otherwise just state them.

**Completion criterion:** a vetted, leverage-ordered findings table where every row was confirmed by your own read of the cited code; direction findings listed separately; an explicit "not audited" note; and rejections recorded. Stop there — selecting and planning is the `plan` skill's job (the `improve` front door orchestrates the handoff).

## Invocation variants

- **Bare** → Recon + audit all nine categories at the effort level, then present.
- **`quick` / `deep`** (anywhere) → effort level; composes with everything (`quick security`).
- **Focus argument** (`security`, `perf`, `tests`, …) → Recon, then audit only that category.
- **`next` / `features` / `roadmap`** → Recon, then audit only the direction category, in more depth: 4–6 grounded suggestions, each with evidence, trade-offs, and a coarse effort estimate.
- **`branch`** → recon at **`branch`** depth, then audit all categories scoped to the brief's branch scope. Usually no subagents. **Tag every finding `introduced` (by this branch) or `pre-existing` (in touched files)** and separate them in the table. If on the default branch or zero commits ahead, say so and offer a full audit. (For a compliance review of the same range — repo standards and originating spec, side by side — that's the `conformance` skill, not this variant.)
