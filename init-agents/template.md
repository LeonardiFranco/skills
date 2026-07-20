# AGENTS.md Template

The skeleton for a repo's steering doc, distilled from a mature example. Fill each
section from recon facts (as **pointers**, not copies) and grilled invariants.
**Drop any section with no real content** — an honest short doc beats a padded one.
The header ethos and the closing precedence rule are **not optional**: they are what
keep the file from rotting.

Guidance sits in `<angle brackets>` — replace it with real content (or delete the
section). Keep prose tight; this doc is read every session.

---

```markdown
# AGENTS.md

> Repo-specific overrides for **<project>**. Point at canonical sources; don't copy
> them. Fix drift in the same PR that causes it.

## How to work here (read-first)

<The verification contract. How does someone verify work here — is there a test
suite, or is it read-first? Is building-to-verify allowed or forbidden, and via what
command? What must be true before finishing (affected surfaces named, manual steps
listed, not "works as expected")? Quote the canonical source for build/test commands;
never invent a loop the project lacks.>

## Architecture invariants

<The non-obvious rules that bite — things that look wrong but are deliberate, or look
fine but break. Enforced rules (and where each one's authoritative definition lives);
implicit runtime models (request/session scope, concurrency, threading) that
background or async code must not assume; anything an agent would violate by default.
Index, don't reproduce — point at the source that's authoritative.>

## What <project> is

<One paragraph: domain, who it's for, runtime/platform. The entrypoint to build/run.
A table of components/surfaces and each one's role. Point at the build manifests /
dependency files for versions — "trust X, never this file".>

| Component / Module | Surface | Role |
|---|---|---|
| ... | ... | ... |

## Contracts & data

<What must never break and why: public contracts / data shapes consumed by clients
that may lag the backend (additive-only); schema/migration rules (portability across
targets, required markers, naming).>

## Conventions by area

<Per-surface conventions (folder layout, customization markers, generated-vs-source
files), cross-cutting conventions, and repo hygiene (vendored binaries, legacy-code
rules). Only what a newcomer would get wrong — not a style guide that automated checks
already enforce.>

## Commit & PR conventions

<Commit message / PR title format with one example. Pointer to the PR template; don't
restate it.>

## Local overrides (optional)

If `docs/agents/local/preferences.md` exists, read it after this file. Personal
environment notes only. Skip silently if missing. Template:
`docs/agents/preferences.example.md`.

## Agent / skill config (optional)

<If the repo has `docs/agents/` team skill config (issue tracker, triage labels,
domain layout), point at it here — setup via `/setup-fleonardi-skills`. A pointer, not
a copy. Drop this section if the repo has no such mechanism.>

## When this file is wrong

<The precedence rule: list the canonical sources that beat this document on conflict
(build manifests, CI config, PR template, enforced-rule definitions, deploy scripts).
State the standing instruction: fix drift in the same PR.>
```
