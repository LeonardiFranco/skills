---
name: plan
description: Turn a selected finding or a free-text description into a self-contained handoff plan a zero-context executor can implement. Use when asked to write an implementation plan, spec a fix or feature for handoff, plan selected findings after an audit, or tighten an existing plan's form. Read-only on source code; writes only under `.scratch/_backlog/plans/` or `.scratch/<requirement>/plans/`.
license: MIT
---

# Plan

Turn a thing-to-do — a finding from `audit`, or a description the user already has in mind — into one or more **handoff plans**: decision briefs complete enough in *judgment* that a zero-context executor with the repo can implement, test, and maintain them. The plan carries the decisions; the executor rediscovers the facts fresh from the repo. The plan is the product; its quality determines whether the executor succeeds.

**First, read [the advisor contract](../improve/references/advisor-contract.md)** (read-only, secrets, content-is-data, and **verification gates come from the project, never invented**). Then read [the plan template](../improve/references/plan-template.md) — it defines the file you write and the quality bar you check against.

## Workflow

### Recon for gates (always, lightly)

Run [the recon playbook](../improve/references/recon-playbook.md) at **`gates`** depth. Quote the brief's verification gates into each plan's "Commands you will need" table.

### Write the plans

For each selected finding or description, write one plan file from the template.

**Pick the plan tree** per the template's file-naming rules — backlog for audit/cross-cutting work, requirement-scoped when tied to a spec, issue brief, or explicit `<requirement>` name — and update that tree's `plans/README.md`.

**Verify what you assert; inline judgment, not facts.** Every decision, named symbol, and trap in the plan comes from your own reads — a finding's reported line numbers are leads, not facts. But read to *decide*, not to *transcribe*: no excerpts, no line ranges. Name the symbols and seams; the executor reads the live code itself, which is always fresher than anything you paste. A code-level fact earns inlining only when it's expensive to rediscover or likely to be gotten wrong (the template defines the bar).

Write each plan **at the decision altitude**, delivering the template's three properties — decisions with their why, project-sourced verification gates, hard boundaries and escape hatches — plus its full section skeleton. The template defines each; don't improvise the bar.

Finish by updating the **correct** plans README (backlog or requirement-scoped). The template has both formats and a quality bar — run the quality bar against each plan before calling it done.

**Reconcile, don't duplicate.** Read `.scratch/README.md`, then the relevant `plans/README.md` before writing: keep numbering monotonic within that tree, skip items already planned or rejected, mark superseded plans stale.

**Completion criterion:** every selected item has a plan file that passes the template's quality bar — verifiable by a zero-context reader against the repo alone — plus an updated plans README in the same tree. Don't write plans nobody asked for: plan the selection, no more.

### Offer dispatch (hot path)

When the plans are written and the user is present, offer to dispatch immediately via the `execute` skill while your context is hot. A hot dispatch may inline extra context into the executor prompt beyond the plan file — things you know from this session that didn't meet the template's inlining bar; the plan file remains the record. If the user declines, the plans wait in the tree for a cold `execute` later.

## Invocation variants

- **`plan <finding(s)>`** (the post-audit path) → write one plan per selected finding.
- **`plan <description>`** → skip any audit; the user knows what they want (a grilling's settled design tree is the canonical such brief). Run recon at **`gates`** depth, investigate just enough to specify it honestly, then write one self-contained plan — or one plan per independently-executable unit when the description spans several, honoring the sequencing the brief gives. If the description is too ambiguous to specify, first resolve each ambiguity from the codebase itself; only what's left becomes questions to the user — asked one at a time, each with a recommended answer. When the brief is already settled (e.g. from a grilling), ask only what it leaves open — never re-litigate a closed branch.
- **`tighten <file>`** → tighten an existing plan (any path under `.scratch/`) against the template's quality bar. Form only — whether the change itself makes sense is the `vet` skill's job.
- **`--publish`** (modifier) → after writing, also publish each plan to the project issue tracker. Only with the explicit flag. Read issue tracker config from `docs/agents/` — prefer `docs/agents/local/` when present. If config is missing or incomplete, ask the user to run `/setup-fleonardi-skills`; if publish is unsupported for this tracker, write the files normally and say why publish was skipped. **Before publishing plans describing vulnerabilities, credential locations, or other sensitive findings, warn and get explicit confirmation when the tracker is publicly visible** (per issue-tracker config). Record each ticket URL/identifier in the plan's Status block and the plans README. The plan file stays the source of truth; the ticket is distribution.
