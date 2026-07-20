---
name: gap-hunt
description: Hunt one spec for omissions — user features and behaviors its own scope implies but never specs — and report grounded gaps for the user to select. Use when asked to gap-hunt a spec, find what a spec is missing, or check spec coverage; also reached by vet after a clean evaluative pass and by to-spec before publishing. Report-only — writes nothing.
license: MIT
---

# Gap Hunt

Hunt one spec for **errors of omission**: the user features and behaviors its own scope implies but never specs. The divergent counterpart to `vet` — evaluation judges what's written; the hunt covers what isn't. You report; the user decides. **This skill writes nothing.**

**First, read [the advisor contract](../improve/references/advisor-contract.md).**

## The grounding rule — the YAGNI firewall

A gap survives only if it cites a **hook**: a specific place in (i) the spec's own declarations — actors, scope, lifecycle it names — or (ii) the corpus — a sibling spec, the domain model, the roadmap's stated direction — that *implies* the missing piece. Domain priors (what products in this category generally have) may be used as a lens to *notice* a candidate, never as its grounding: a candidate with no hook is not a finding. It either dies or, if genuinely worth a glance, lands on the **Horizon** shelf (below). Inventing features is this skill's failure mode; the hook requirement is the defence, by construction.

## Resolve

The argument is one spec: a path, or the draft in conversation when `to-spec` routes here pre-publication. Read that spec in full — and only that spec. Family and sibling documents are read by the parity lens subagent in its own window, never pulled into yours.

From the spec alone, enumerate the coverage frame: every **actor** its scope touches and every **lifecycle stage** of the thing it governs. This frame is what "exhaustive" means below.

## Hunt — divergent fan-out

Dispatch cheap read-only subagents, one per lens (briefed per [the subagent briefing template](../improve/references/subagent-briefing.md), return contract: candidate gaps with hooks, no fixes):

- **Actor lens** — walk each actor in the frame through the spec: what does this person see, do, and get told at each point? Silent stretches are candidates.
- **Lifecycle lens** — walk the governed thing through its stages, including the unhappy ones (failure, cancellation, reversal, concurrency): what does the spec say happens? Unspecced transitions are candidates.
- **Parity lens** — reads the spec's family (master/slices/variants, resolved by naming convention) and cross-referenced siblings in its own context: capabilities a sibling specs that this spec's analogous scope leaves silent, and gaps that fall *between* family documents. Each candidate names which document should absorb it.
- **Priors lens** — domain priors only. Its candidates are Horizon material unless the judge finds them a hook.

## Judge — your verdict

Judge every candidate against the grounding rule yourself:

- Hook cited and it holds on your own read → **finding**.
- Actually covered, or explicitly out-of-scope in the spec → dead. An "Out of Scope" entry is a decision, not a gap.
- No hook, but genuinely worth a glance → **Horizon**, capped at 3; the rest die.

## Report

A findings table in conversation, `audit`-style — no file. Per gap: the omission in one sentence, the hook (document + quote), the absorbing document, and a recommended disposition — **spec it** / **explicitly out-scope it** / **reject**. Order by leverage.

Then, beneath the table, **Horizon**: at most 3 one-liners, plainly marked as ungrounded domain priors, offered without advocacy. Unpromoted Horizon items vanish with the conversation — they are never findings and never carry forward.

## Select & route

The user selects; nothing proceeds unselected. Accepted gaps route onward — a `to-spec`-style edit of the spec, or entries in its open-questions section — done by whoever the user sends them to, not by you.

**Completion criterion:** every actor and lifecycle stage in the coverage frame is accounted for — covered, explicitly out-of-scope, or a finding; every reported gap carries a hook you verified yourself; Horizon holds at most 3 clearly-marked items; and the user has a table to select from.
