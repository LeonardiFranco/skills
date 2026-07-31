---
name: vet
description: Autonomously review an OpenSpec change or spec at the functional altitude — does the change make sense against the codebase, the domain, and the rest of the corpus — then apply the accepted improvements. Use when asked to functionally review, refine, or sanity-check a change or spec without an interview, or when another skill needs a spec vetted before execution. Read-only on source; writes only the artifact under review (plus openspec/changes/README.md on a status change).
license: MIT
---

# Vet

Review one OpenSpec artifact — a change under `openspec/changes/<slug>/`, a main spec at `openspec/specs/<domain>/spec.md`, or a delta under a change's `specs/` — for whether the change it describes *makes sense*, and leave the artifact improved. The autonomous counterpart to `grilling`: the codebase and the document corpus answer the questions instead of the user, and only what they can't answer reaches the user at the end.

**First, read [the advisor contract](../improve/references/advisor-contract.md).** You never edit source code; the only files you write are the artifact under review and, when a change's status changes, `openspec/changes/README.md`.

**Altitude guard:** you review the *change*, not the *document*. Template conformance — that's `/plan tighten`, not you. A functionally sound but sloppily written change gets a clean verdict and a route to tighten, not a rewrite. And you judge what the artifact *says*, not what it fails to say — omissions are the `gap-hunt` skill's hunt, not your rubric.

## Resolve

- `<slug>` → `openspec/changes/<slug>/` (review the whole folder; edit the relevant artifacts)
- a path under `openspec/` → that file or folder
- legacy `.scratch/.../plans/` path → if it still exists, review it but recommend migrating to an OpenSpec change via `/plan`

Zero or multiple matches → STOP, list candidates, ask which.

Artifact type: under `openspec/changes/` → **change rubric**; under `openspec/specs/` → **spec rubric**. Read the artifact in full, then its neighbours — sibling active changes, or sibling domain specs.

## Verify — cheap subagents, your verdict

Extract every **as-is claim** the artifact makes about the current system: a change's Decisions and key assumptions about how the code is today; a spec's statements about how the system behaves *today*. Fan out cheap read-only subagents (briefed per [the subagent briefing template](../improve/references/subagent-briefing.md), scoped to verification not audit) to check each cluster against the codebase; each returns confirmed / drifted with `file:line` evidence.

Future-state statements are not claims. Never dispatch a subagent to check whether an aspiration is implemented — a delta describing unbuilt behavior is doing its job.

You judge what each result *means*. A drifted claim might be cosmetic or might invalidate the approach; that call never delegates.

## Judge

**Change rubric** — ground truth is the codebase now:

- **Worth building** — does the change still serve its proposal "Why"?
- **Right approach** — is each chosen approach still right given what verification found?
- **Scope** — are the in/out boundaries functionally right?
- **Fit** — collisions and dependency order against sibling changes and archived specs.

**Spec rubric** — split ground truth:

- **As-is honesty** — claims about today's system match the codebase.
- **Corpus coherence** — terminology and cross-spec dependency chains hold.
- **Steering alignment** — consistent with settled project direction.
- **Internal soundness** — scope boundaries defensible; open questions honest.

## Improve

Apply each accepted finding to the artifact yourself. When a functional change to a *change folder* alters implementation specifics, dispatch a cheap subagent to recon the affected code and draft revised Decisions / Suggested approach, then review the draft and apply only what survives. Any as-is claim you touch gets re-sourced from your own read.

If the verdict is that a change should not proceed, don't hollow it out — update `openspec/changes/README.md` to REJECTED with one line and route below.

## Report & route

End with, per finding: verdict, evidence, and what changed in the artifact — then the open questions only the user can answer, each with your recommended answer.

Route onward, recommending one:

- **Change sound, edits applied** → `execute` (via `/plan tighten` first if form is sloppy).
- **Material open questions remain** → `grilling`, seeded with exactly those questions — a short grilling, not a full one.
- **Shape wrong, re-plan needed** → `/plan <brief>`, carrying your verified findings as the brief.
- **Spec evaluated clean** → offer `gap-hunt`.
- **Spec needs product-level rework beyond edits** → flag to the user; rewrite via `/plan` or edit the delta/main spec once direction is settled.

**Completion criterion:** every as-is claim verified or flagged with evidence, every rubric line applied, accepted edits sitting in the artifact, and a report that surfaces only the questions the codebase and corpus couldn't answer.
