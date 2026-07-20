# Handoff Plan Template

Every plan is written for an executor with **zero context from this session**: it has not seen the advisor conversation, the audit, or the other plans. It *does* have the full repo and is competent at discovery — finding files, reading current state, following conventions it can see. What it cannot recover is the judgment behind the plan: which approach was chosen and why, which of two plausible paths was ruled out, which file that looks related is a trap.

**Inline judgment, not facts.** Facts about the code — line numbers, excerpts, exact current shapes — are re-derivable from the repo at execution time, always fresher than anything you paste, and rot the moment the code moves. Judgment is not re-derivable; a plan that omits it fails. So a plan carries three things:

1. **Decisions and their why** — the chosen approach, named seams and symbols, ruled-out alternatives where the executor would plausibly pick one.
2. **Verification gates** — every step ends with a check and its expected result. **The checks come from the project's steering, not from a generic assumption** (see the advisor contract). If the project forbids building-to-verify or has no automated suite, the gates are whatever it prescribes — grep/read-based checks or manual steps — and the plan says so. Never write a `test`/`typecheck` gate the project doesn't actually have.
3. **Hard boundaries and escape hatches** — an explicit out-of-scope list *with reasons*, and "STOP and report" conditions instead of letting the executor improvise when reality contradicts a stated decision.

A code-level fact earns inlining only when it is expensive to rediscover or likely to be gotten wrong: the non-obvious constraint, the misleading near-duplicate, the convention exception. Routine "here's what the file looks like today" does not — the executor will read the file anyway.

File naming (pick one tree per plan):

- **Backlog** (audit / cross-cutting): `.scratch/_backlog/plans/NNN-short-slug.md` — update `.scratch/_backlog/plans/README.md`
- **Requirement-scoped** (tied to a spec): `.scratch/<requirement>/plans/NN-short-slug.md` — update that requirement's `plans/README.md`

See `.scratch/README.md` for the master index.

---

## Template

```markdown
# Plan NNN: <Imperative title — what will be true after this plan>

> **Executor instructions**: You have the repo — do your own reconnaissance of
> the in-scope files before changing anything. This plan gives you the
> decisions, boundaries, and done criteria; "Suggested approach" is advice,
> not law. Run every verification gate and confirm the expected result. If
> anything in "STOP conditions" occurs, stop and report — do not improvise
> around a contradicted decision. When done, update this plan's status row in
> the `README.md` in the **same directory as this plan file** (backlog or
> requirement-scoped — see `.scratch/README.md`). Skip only if an execute
> reviewer dispatched you and said they maintain the index.
>
> **Build / commit policy**: as defined by this project's steering (quoted in
> "Commands you will need"). Do not commit, push, switch branches, or create
> worktrees unless that steering or the operator says to.

## Status

- **Priority**: P1 | P2 | P3
- **Effort**: S | M | L
- **Risk**: LOW | MED | HIGH  (the execute reviewer scales review depth by this)
- **Depends on**: `.scratch/_backlog/plans/NNN-*.md` (or "none")
- **Category**: bug | security | perf | tests | tech-debt | migration | dx | docs | direction
- **Planned**: <YYYY-MM-DD>
- **Source**: <what this plan implements — a ticket (`tickets.md#<title>`), a spec, an audit finding, or an issue URL; "ad hoc" when none>

## Why this matters

2–5 sentences: the problem, its concrete cost, and what improves when this
lands. Intent is what lets the executor make a correct judgment call when a
detail is off — and what the reviewer judges the diff against.

## Decisions

The judgment this plan is built on — everything the executor cannot re-derive
from the repo:

- The chosen approach, one decision per bullet, each with its one-line why.
- Named modules, symbols, and seams (names are stabler than line numbers).
- Ruled-out alternatives *where the executor would plausibly pick one*:
  "Do X, not the tempting Y, because Z."
- Non-obvious constraints and traps: the misleading near-duplicate, the
  convention exception, the contract whose shape must not change.
- Documented vocabulary or design constraints to honor, quoted from the
  intent/design docs found in recon (the `CONTEXT.md` terms to use in names,
  the ADR this work must stay consistent with) — the executor has not read
  those docs.

## Orientation

One line per relevant file — role only, no excerpts:

- `path/SomeUnit.ext` — owns X; the change lands here.
- `path/Exemplar.ext` — error handling follows the Result pattern; match it.

## Commands you will need

Sourced from this project's steering during recon — quoted, not guessed. Mark
which are human-only gates per that steering.

| Purpose   | Command                | Expected on success                          |
|-----------|------------------------|----------------------------------------------|
| Build     | `<from project>`       | <e.g. exit 0 — or "human gate, do not run">   |
| Verify    | `<from project>`       | <expected result>                            |

If the project prescribes no automated verification, replace this table with
the project's actual verification path (manual steps, or read/grep checks like
`grep -rn "<old pattern>" <dir>` → no matches) and state that explicitly.

## Suggested executor toolkit

(Optional — include only when relevant skills/tools plausibly exist in the
executor's environment; skip otherwise.) Skills to invoke and for what;
reference docs worth reading first, by path or URL.

## Scope

**In scope** (the only files you may create or modify):
- `path/SomeUnit.ext` (MODIFY)
- `path/NewUnit.ext` (CREATE)

**Out of scope** (do NOT touch, even though they look related — each with its
reason; the reason is judgment the executor can't re-derive):
- `path/LegacyUnit.ext` — deprecated; changing it risks pinned callers.
- Any change to a public contract/DTO shape — clients depend on it.

## Suggested approach

An ordered sketch of how to get there — small verifiable moves, sequenced so
the codebase is never broken between them when possible (add the new path,
switch callers, then remove the old path). Advisory: the executor finds the
concrete edit path itself and may deviate when reality disagrees, provided
every deviation is documented and the done criteria still hold.

**Verify** after each move: `<check from the Commands table>` → <expected result>

## Test plan

- New tests to write, in which file, covering which cases (happy path, the
  specific bug/regression this fixes, named edge cases) — **only if the project
  has a test suite**. Name the existing test to model the structure on.
- If the project has no automated suite, give the manual verification steps
  instead: what to open, what action to take, the expected outcome.
- Verification: `<the project's check>` → expected result, including any new tests.

## Done criteria

**This is the contract** — the review verifies these outcomes, not compliance
with the suggested approach. Machine-checkable where the project allows it;
manual otherwise. ALL must hold:

- [ ] <project's verification check> passes (or: human gate confirmed by operator)
- [ ] New tests for <X> exist and pass (only if the project has a suite)
- [ ] `grep -rn "<old pattern>" <dir>` returns no matches
- [ ] No files outside the in-scope list are modified (`git status`)
- [ ] Plans-tree `README.md` status row updated (same directory as this plan file)

## STOP conditions

Stop and report back (do not improvise) if:

- Reality contradicts a stated decision — a seam, symbol, or constraint the
  plan names doesn't exist or doesn't behave as described.
- A verification gate fails twice after a reasonable fix attempt.
- The fix appears to require touching an out-of-scope file.
- You discover the assumption "<key assumption>" is false.

## Maintenance notes

For whoever owns this code after the change lands:

- What future changes will interact with this.
- What a reviewer should scrutinize.
- Any follow-up explicitly deferred out of this plan (and why).
```

---

## Index file: `.scratch/_backlog/plans/README.md`

Written once after all plans, updated by executors and `reconcile`:

```markdown
# Implementation Plans

Generated by the advisor family on <date>. Execute in the order below unless
dependencies say otherwise. Each executor: read the plan fully before starting,
honor its STOP conditions, and update your row when done.

## Execution order & status

| Plan | Title | Priority | Effort | Depends on | Status |
|------|-------|----------|--------|------------|--------|
| 001  | ...   | P1       | S      | —          | TODO   |
| 002  | ...   | P1       | M      | 001        | TODO   |

Status values: TODO | IN PROGRESS | DONE | BLOCKED (one-line reason) | REJECTED (one-line rationale).

## Dependency notes

- 002 requires 001 because <reason>.

## Findings considered and rejected

- <finding>: not worth doing because <one line>. (So nobody re-audits it.)
```

## Quality bar — check before finishing each plan

- Could a capable model that has never seen this session execute this with only the plan file and the repo? Every *decision* it needs must be in the file; every *fact* it needs must be findable in the repo from the names the plan gives.
- Is anything inlined that the executor could re-derive by reading the code? Cut it — it will rot. Keep only the facts that are expensive to rediscover or likely to be gotten wrong.
- Is every verification a check with an expected result (sourced from the project), not a judgment ("make sure it works")?
- Does every out-of-scope entry carry its reason, and does Decisions name the traps and ruled-out alternatives you actually saw?
- If the Source is a ticket, do the done criteria **cover** its acceptance criteria — each one mapped to a check or a named manual step, not restated verbatim?
- Are the STOP conditions specific to this plan's real risks, not boilerplate?
- Would a reviewer reading only "Why this matters" + "Done criteria" understand what they're approving?
- No secret values anywhere — locations and credential types only.
