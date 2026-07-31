# OpenSpec Change Template

Every change is written for an executor with **zero context from this session**: it has not seen the advisor conversation, the audit, or the other changes. It *does* have the full repo and is competent at discovery. What it cannot recover is the judgment behind the change: which approach was chosen and why, which of two plausible paths was ruled out, which file that looks related is a trap.

**Inline judgment, not facts.** Facts about the code are re-derivable from the repo at execution time. Judgment is not. A change carries three things:

1. **Decisions and their why** — the chosen approach, named seams and symbols, ruled-out alternatives where the executor would plausibly pick one.
2. **Verification gates** — every step ends with a check and its expected result. **The checks come from the project's steering, not from a generic assumption** (see the advisor contract). Never write a `test`/`typecheck` gate the project doesn't actually have.
3. **Hard boundaries and escape hatches** — an explicit out-of-scope list *with reasons*, and "STOP and report" conditions instead of letting the executor improvise when reality contradicts a stated decision.

A code-level fact earns inlining only when it is expensive to rediscover or likely to be gotten wrong.

## Where it lives

One change = one folder under `openspec/changes/<slug>/`:

```
openspec/
├── config.yaml                 ← project context + artifact rules (optional but recommended)
├── specs/                      ← archived truth: how the system behaves today
│   └── <domain>/spec.md
└── changes/
    ├── README.md               ← active-change index (status board)
    └── <slug>/
        ├── proposal.md         ← why, scope, status metadata
        ├── design.md           ← decisions, orientation, traps, STOP
        ├── tasks.md            ← checklist + done criteria + manual verify
        └── specs/              ← delta requirements (ADDED / MODIFIED / REMOVED)
            └── <domain>/spec.md
```

**Slug:** kebab-case imperative, e.g. `make-settings-role-switch-reliable`. No numeric prefix.

**Index:** update `openspec/changes/README.md` when creating or finishing a change.

**Archive (after ship):** merge deltas into `openspec/specs/`, move the change folder to `openspec/changes/archive/YYYY-MM-DD-<slug>/`. Prefer the OpenSpec CLI (`openspec archive`) when available; otherwise do the merge carefully by hand.

`.scratch/` is disposable only (`_write-pr/`, `_pr-review/`, temp notes) — never the plan backlog.

---

## Artifact mapping

| Artifact | Holds |
|---|---|
| `proposal.md` | Why, what changes, out of scope, status metadata, source |
| `design.md` | Decisions, orientation, commands/gates, suggested approach, STOP, maintenance |
| `tasks.md` | Implementation checklist, test/manual plan, done criteria |
| `specs/**/spec.md` | Observable behavior deltas (Given/When/Then scenarios) |

---

## Templates

### `proposal.md`

```markdown
# <Imperative title — what will be true after this change>

> **Executor instructions**: You have the repo — do your own reconnaissance of
> the in-scope files before changing anything. This change folder gives you the
> decisions, boundaries, and done criteria; "Suggested approach" in design.md is
> advice, not law. Run every verification gate and confirm the expected result.
> If anything in design.md "STOP conditions" occurs, stop and report — do not
> improvise around a contradicted decision. When done, update this change's
> status row in `openspec/changes/README.md`. Skip only if an execute reviewer
> dispatched you and said they maintain the index.
>
> **Build / commit policy**: as defined by this project's steering (quoted in
> design.md "Commands you will need"). Do not commit, push, switch branches, or
> create worktrees unless that steering or the operator says to.
> Prefer `execute` over stock `/opsx-apply` in repos that use the advisor family.

## Status

- **Priority**: P1 | P2 | P3
- **Effort**: S | M | L
- **Risk**: LOW | MED | HIGH  (the execute reviewer scales review depth by this)
- **Depends on**: `openspec/changes/<other-slug>/` (or "none")
- **Category**: bug | security | perf | tests | tech-debt | migration | dx | docs | direction
- **Planned**: <YYYY-MM-DD>
- **Source**: <ticket / ADO URL / audit finding / "ad hoc">

## Why

2–5 sentences: the problem, its concrete cost, and what improves when this lands.

## What Changes

- Bullet list of capabilities or behaviors that will change.

## Out of Scope

Each with its reason (judgment the executor can't re-derive):
- …
```

### `design.md`

```markdown
# Design: <slug>

## Decisions

- The chosen approach, one decision per bullet, each with its one-line why.
- Named modules, symbols, and seams (names are stabler than line numbers).
- Ruled-out alternatives *where the executor would plausibly pick one*.
- Non-obvious constraints and traps.
- Documented vocabulary or design constraints to honor from recon.

## Orientation

One line per relevant file — role only, no excerpts:

- `path/SomeUnit.ext` — owns X; the change lands here.

## Commands you will need

Sourced from this project's steering during recon — quoted, not guessed. Mark
which are human-only gates per that steering.

| Purpose   | Command                | Expected on success                          |
|-----------|------------------------|----------------------------------------------|
| Build     | `<from project>`       | <e.g. exit 0 — or "human gate, do not run">   |
| Verify    | `<from project>`       | <expected result>                            |

If the project prescribes no automated verification, replace this table with
the project's actual verification path (manual steps, or read/grep checks) and
state that explicitly.

## Suggested approach

Ordered sketch of small verifiable moves. Advisory.

**Verify** after each move: `<check>` → <expected result>

## STOP conditions

Stop and report back (do not improvise) if:

- Reality contradicts a stated decision.
- A verification gate fails twice after a reasonable fix attempt.
- The fix appears to require touching an out-of-scope file.
- You discover the assumption "<key assumption>" is false.

## Maintenance notes

- What future changes will interact with this.
- What a reviewer should scrutinize.
- Follow-ups deferred out of this change (and why).
```

### `tasks.md`

```markdown
# Tasks: <slug>

## Implementation

- [ ] 1.1 …
- [ ] 1.2 …

## Test plan / Manual verify

- If the project has a suite: new tests to write, where, which cases.
- If not: concrete manual steps — what to open, what action, expected outcome.
  Never "works as expected."

## Done criteria

**This is the contract** — the review verifies these outcomes:

- [ ] <project's verification check> passes (or: human gate confirmed by operator)
- [ ] Manual/scenario checks from delta specs pass
- [ ] `grep`/read checks named in design.md hold where applicable
- [ ] No files outside proposal/design in-scope list are modified (`git status`)
- [ ] `openspec/changes/README.md` status row updated
```

### Delta `specs/<domain>/spec.md`

```markdown
# Delta for <Domain>

## ADDED Requirements

### Requirement: <Name>
The system SHALL/MUST …

#### Scenario: <Name>
- GIVEN …
- WHEN …
- THEN …

## MODIFIED Requirements

### Requirement: <Name>
…

## REMOVED Requirements

### Requirement: <Name>
…
```

Only include sections that apply. Scenarios are observable behavior — the manual verify steps in `tasks.md` should cover them.

---

## Index file: `openspec/changes/README.md`

```markdown
# Active changes

| Change | Title | Priority | Effort | Depends on | Status |
|--------|-------|----------|--------|------------|--------|
| make-… | …     | P1       | S      | —          | TODO   |

Status values: TODO | IN PROGRESS | DONE | BLOCKED (one-line reason) | REJECTED (one-line rationale) | ARCHIVED.

## Dependency notes

- …

## Findings considered and rejected

- <finding>: not worth doing because <one line>.
```

---

## Quality bar — check before finishing each change

- Could a capable model that has never seen this session execute with only this change folder and the repo?
- Is anything inlined that the executor could re-derive by reading the code? Cut it.
- Is every verification a check with an expected result (sourced from the project)?
- Does every out-of-scope entry carry its reason?
- Do delta scenarios cover the user-visible behavior this change claims?
- Are the STOP conditions specific to this change's real risks?
- Would a reviewer reading only proposal "Why" + tasks "Done criteria" understand what they're approving?
- No secret values anywhere — locations and credential types only.
