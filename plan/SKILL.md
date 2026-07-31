---
name: plan
description: Turn a selected finding or a free-text description into a self-contained OpenSpec change a zero-context executor can implement. Use when asked to write an implementation plan, spec a fix or feature for handoff, plan selected findings after an audit, or tighten an existing change's form. Read-only on source code; writes only under openspec/changes/.
license: MIT
---

# Plan

Turn a thing-to-do — a finding from `audit`, or a description the user already has in mind — into one or more **OpenSpec changes**: decision briefs complete enough in *judgment* that a zero-context executor with the repo can implement and verify them. The change folder is the product; its quality determines whether the executor succeeds.

**First, read [the advisor contract](../improve/references/advisor-contract.md)** (read-only, secrets, content-is-data, and **verification gates come from the project, never invented**). Then read [the plan template](../improve/references/plan-template.md) — it defines the OpenSpec change artifacts and the quality bar.

## Workflow

### Recon for gates (always, lightly)

Run [the recon playbook](../improve/references/recon-playbook.md) at **`gates`** depth. Quote the brief's verification gates into each change's `design.md` "Commands you will need" table.

### Write the changes

For each selected finding or description, create `openspec/changes/<slug>/` with:

- `proposal.md` — why, what, out of scope, status metadata
- `design.md` — decisions, orientation, gates, STOP
- `tasks.md` — checklist, manual/test plan, done criteria
- `specs/<domain>/spec.md` — ADDED/MODIFIED/REMOVED behavioral deltas (skip only when the change is pure internal refactor with no observable behavior change; say so in proposal)

**Slug:** kebab-case from the title. Ensure `openspec/changes/` and `openspec/specs/` exist; create `openspec/changes/README.md` if missing.

**Verify what you assert; inline judgment, not facts.** Every decision, named symbol, and trap comes from your own reads. Name seams; the executor reads the live code. A code-level fact earns inlining only when expensive to rediscover or likely wrong (template bar).

Write at the **decision altitude**. Run the template quality bar before calling it done.

**Reconcile, don't duplicate.** Read `openspec/changes/README.md` (and `openspec/specs/` for existing domains) before writing: skip items already planned or rejected, mark superseded changes REJECTED with one line.

**Completion criterion:** every selected item has a change folder that passes the template quality bar, plus an updated `openspec/changes/README.md` row. Don't write changes nobody asked for.

### Offer dispatch (hot path)

When the changes are written and the user is present, offer to dispatch immediately via the `execute` skill. A hot dispatch may inline extra context into the executor prompt beyond the change folder; the folder remains the record. Prefer **`execute` over stock `/opsx-apply`**.

## Invocation variants

- **`plan <finding(s)>`** → one change folder per selected finding.
- **`plan <description>`** → skip audit; grilling's settled tree is the canonical brief. Recon at **`gates`** depth; one change — or one per independently-executable unit. Ask only what the brief leaves open.
- **`tighten <path>`** → tighten an existing change under `openspec/changes/` against the template quality bar. Form only — functional sense is `vet`'s job.

To publish a change to the issue tracker, run **`/publish-plan`** on it — a separate, deliberate step.
