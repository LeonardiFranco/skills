---
name: vet
description: Autonomously review a handoff plan or spec at the functional altitude — does the change make sense against the codebase, the domain, and the rest of the corpus — then apply the accepted improvements. Use when asked to functionally review, refine, or sanity-check a plan or spec without an interview, or when another skill needs a spec vetted before execution. Read-only on source; writes only the artifact under review (plus its plans README on a status change).
license: MIT
---

# Vet

Review one spec artifact — a handoff plan under `.scratch/`, or a spec at its canonical path `.scratch/<requirement-slug>/SPEC.md` (legacy `PRD.md`; legacy root forms `SPEC-*.md` / `PRD-*.md` also count) — for whether the change it describes *makes sense*, and leave the artifact improved. The autonomous counterpart to `grill-plan`: the codebase and the document corpus answer the questions instead of the user, and only what they can't answer reaches the user at the end.

**First, read [the advisor contract](../improve/references/advisor-contract.md).** You never edit source code; the only files you write are the artifact under review and, when a plan's status changes, its plans `README.md`.

**Altitude guard:** you review the *change*, not the *document*. Template conformance, section formatting, done-criteria phrasing — that's `/plan tighten`, not you. A functionally sound but sloppily written plan gets a clean verdict and a route to tighten, not a rewrite. And you judge what the artifact *says*, not what it fails to say — omissions (missing user features) are the `gap-hunt` skill's hunt, not your rubric.

## Resolve

- `NNN` → `.scratch/_backlog/plans/NNN-*.md`
- `<requirement-slug> NN` → `.scratch/<requirement-slug>/plans/NN-*.md`
- a path → that file

Zero or multiple matches → STOP, list candidates, ask which.

Artifact type comes from location: under a `plans/` tree → **plan rubric**; a requirement's `SPEC.md` or a legacy root spec → **spec rubric**. Read the artifact in full, then its neighbours — sibling plans in the same tree, or every sibling spec this one cross-references.

## Verify — cheap subagents, your verdict

Extract every **as-is claim** the artifact makes about the current system: a plan's stated Decisions and key assumptions about how the code is today (named seams and symbols exist, constraints hold); a spec's statements about how the system behaves *today*. Fan out cheap read-only subagents (briefed per [the subagent briefing template](../improve/references/subagent-briefing.md), scoped to verification not audit) to check each cluster against the codebase; each returns confirmed / drifted with `file:line` evidence.

Future-state statements are not claims. Never dispatch a subagent to check whether an aspiration is implemented — a spec describing unbuilt behavior is doing its job.

You judge what each result *means*. A drifted claim might be cosmetic or might invalidate the artifact's whole approach; that call never delegates.

## Judge

**Plan rubric** — ground truth is the codebase now:

- **Worth building** — does the change still serve its "Why this matters"? Verification may show the problem fixed in passing or moved.
- **Right approach** — is each chosen approach still right given what verification found? Name the weakest decision and attack it.
- **Scope** — are the in/out boundaries functionally right, not just well-formed? Anything in scope that shouldn't change; anything out that must?
- **Fit** — collisions and dependency order against sibling plans and the corpus' standing decisions.

**spec rubric** — split ground truth:

- **As-is honesty** — claims about today's system match the codebase; drifted ones are live findings.
- **Corpus coherence** — terminology matches the domain model's ubiquitous language; cross-spec dependency chains hold; no sibling contradicts it.
- **Steering alignment** — consistent with the roadmap's stated direction and decisions the corpus records as settled.
- **Internal soundness** — scope boundaries defensible; open questions honest, not silently resolved.

## Improve

Apply each accepted finding to the artifact yourself. When a functional change to a *plan* alters implementation specifics — different seams, different scope, a different suggested approach — dispatch a cheap subagent to recon the affected code and draft the revised Decisions and Suggested approach, then review the draft like a tech lead and apply only what survives. Any as-is claim you touch gets re-sourced from your own read, never from the subagent's quote alone.

If the verdict is that a plan should not proceed (finding gone, approach invalidated), don't hollow it out with edits — update its `README.md` row per `reconcile` conventions and route below.

## Report & route

End with, per finding: verdict, evidence, and what changed in the artifact — then the open questions only the user can answer, each with your recommended answer.

Route onward, recommending one:

- **Plan sound, edits applied** → `execute` (via `/plan tighten` first if form is sloppy).
- **Material open questions remain** → `grill-plan`, seeded with exactly those questions — a short grilling, not a full one.
- **Shape wrong, re-plan needed** → `/plan <brief>`, carrying your verified findings as the brief.
- **Spec evaluated clean** → offer `gap-hunt` — evaluation covered what's written; the hunt covers what isn't.
- **Spec needs product-level rework beyond edits** → flag to the user; `to-spec` republishes once direction is settled.

**Completion criterion:** every as-is claim verified or flagged with evidence, every rubric line applied, accepted edits sitting in the artifact, and a report that surfaces only the questions the codebase and corpus couldn't answer.
