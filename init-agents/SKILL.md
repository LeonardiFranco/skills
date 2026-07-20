---
name: init-agents
description: Author or refresh a repo's AGENTS.md steering doc — recon the repo, grill the maintainer for what can't be inferred, and synthesize a thin doc that cites canonical sources instead of copying them.
disable-model-invocation: true
---

# Init Agents

Author (or refresh) the repo's **`AGENTS.md`** — the team-owned steering doc every
other skill reads for "how this project builds, tests, and verifies." A good
AGENTS.md is a **thin pointer-doc**: it cites canonical sources (build manifests, CI
config, PR templates, enforced-rule definitions) rather than copying facts that rot.
The discipline is the product — a bloated AGENTS.md that duplicates the build config
is worse than none.

This skill **only** authors `AGENTS.md`. It never writes `docs/agents/` skill config
(that's `/setup-fleonardi-skills`) and never the sources AGENTS.md points at.

Built on two pieces — point at them, don't restate:

- **The [recon playbook](../improve/references/recon-playbook.md)** at `full`
  depth — maps the territory (stack, verification gates, conventions, layout,
  intent docs).
- **`/grilling`** — the relentless one-question-at-a-time interview for what recon
  can't infer.

## 1. Detect — create or refresh?

Read `AGENTS.md` (if present) and `CLAUDE.md` first.

- **No `AGENTS.md`** → the **create** branch (§4a).
- **`AGENTS.md` exists** → the **refresh** branch (§4b). The existing file is
  **authoritative** — you propose edits, never silently overwrite it.

## 2. Recon

Run [the recon playbook](../improve/references/recon-playbook.md) at `full` depth. The brief gives you the *facts* AGENTS.md should cite:
solution entrypoint, project/surface layout, verification gates (test suite?
build-to-verify policy?), commit/PR conventions, intent docs. **Cite these; do not
copy what already lives in a canonical source** — record the pointer instead (e.g.
"versions: trust the build manifest, never this file").

## 3. Grill the gaps

Recon finds *what is*; only the maintainer knows *what bites*. Run `/grilling` over
the gaps recon can't infer — one question at a time, each with your recommended
answer, exploring the codebase before asking:

- **Verification policy** — is there a test suite? Is building-to-verify allowed, or
  read-first? What must be true before finishing?
- **Architecture invariants** — the non-obvious rules that bite (enforced rules,
  implicit runtime / concurrency models, things that look wrong but are deliberate).
- **Contracts & boundaries** — what must never break (public contracts, schema
  portability), what's off-limits (vendored libs, legacy code).
- **The why** behind conventions a newcomer would "fix" wrongly.
- **Source-of-truth precedence** — which canonical files beat AGENTS.md on conflict.

Skip anything recon already answered — never re-ask a settled fact.

## 4. Synthesize

Fill [the template](template.md) in the house style — thin, cited, drift-resistant.
Every section either holds real content or is dropped; no fabricated "N/A" padding.

**Always include Local overrides (optional)** — the verbatim three-line read contract
for `docs/agents/local/preferences.md` from the template. It is not optional to
omit: agents discover personal prefs only through this pointer. The file itself may
be missing; "skip silently if missing" is the contract.

### 4a. Create

Start from [template.md](template.md). Draft each section from the recon facts (as
pointers) and the grilled invariants. Present the full draft for sign-off before
writing.

### 4b. Refresh (relentless)

The existing file is authoritative — but be **relentless** about its drift. Diff it
against reality (recon facts + grill answers) and surface every:

- **Stale fact** — a claim contradicted by the current build manifest / CI / source.
- **Bloat** — content copied from a canonical source that should be a pointer (the
  thing that rots).
- **Gap** — a missing section or invariant the codebase clearly needs (a new enforced
  rule with no entry, an undocumented concurrency boundary, or **Local overrides**
  when `docs/agents/` or fleonardi skills are present).
- **Ethos violation** — duplication where a citation belongs, no precedence rule, no
  "fix drift in the same PR" discipline.

Propose each as a concrete edit with its reason. Push hard — a comfortable "looks
fine" is a failure here. But **the user owns the commit**: present the edits, ask
before overwriting, and apply only what they approve.

## 5. Write

On sign-off, write `AGENTS.md`. If `CLAUDE.md` doesn't already point at it, offer to
make `CLAUDE.md` a thin `Read ./AGENTS.md` pointer.

**Completion criterion:** `AGENTS.md` reflects the current repo — every cited fact
traceable to a canonical source (not copied), every non-obvious invariant the grill
surfaced captured, every section either real or dropped, **Local overrides (optional)
present verbatim** — and it was written only after the user signed off on the draft
(create) or the specific edits (refresh).
