---
name: wayfinder
description: Plan a huge chunk of work — more than one agent session can hold — as a shared map of investigation tickets under .scratch, resolved one at a time until the way to the destination is clear.
disable-model-invocation: true
---

A loose idea too big for one session, wrapped in **fog** — the way from here to the **destination** isn't visible yet. Wayfinding finds that way; it doesn't charge at the destination. **Chart** the map, then work the **frontier** one ticket at a time until the way is clear.

The destination varies per effort — a spec to hand off, a decision to lock before planning, a change made in place — and **naming it is the first act of charting**: it fixes the scope every ticket is measured against.

## Plan, don't do

Each ticket resolves a decision; the map is done when nothing is left to decide before someone goes and builds the thing. The pull to just do the work is usually the signal you've reached the edge of the map and it's time to hand off. An effort can override this in its Notes — absent that, produce decisions, not deliverables.

## Storage — `.scratch` only

**Never** create tracker work items, labels, or project docs for wayfinding. Maps and tickets live only under the gitignored scratch tree so they do not pollute the repo:

```
.scratch/_wayfinder/<effort-slug>/
├── decision-map.md          # index
└── tickets/
    ├── <ticket-slug>.md
    └── …
```

Create `.scratch/_wayfinder/` and the effort folder as needed. If `.scratch/` does not exist, create it (and a minimal `.scratch/README.md` pointing at OpenSpec for real plans if helpful).

No `docs/agents` issue-tracker config is required for this skill.

## Refer by name

In everything the human reads — narration, Decisions so far, the fog — use the ticket **title**, never a bare id, number, or slug. Paths may appear as links wrapped in the name.

## The Map

The map is the canonical **index** for one wayfinding effort — not a store. It gists decisions and links to the tickets that hold their detail; each decision lives in exactly one ticket.

Assets link from the ticket or index; never paste them in.

### Index body (`decision-map.md`)

Load once per session at low resolution. Open tickets are **not** listed on the index — discover them by reading `tickets/*.md`.

```markdown
# Wayfinder: <effort title>

## Destination

<what reaching the end of this map looks like — one or two lines>

## Notes

<domain; skills every session should consult; standing preferences for this effort>

## Decisions so far

<!-- one line per closed ticket: enough to judge relevance, then zoom the link for the detail -->

- [<closed ticket title>](tickets/<slug>.md) — <one-line gist>

## Not yet specified

<!-- fog: in-scope questions you can't ticket yet -->

## Out of scope

<!-- work ruled beyond the destination; closed, never graduates -->
```

### Tickets (`tickets/<slug>.md`)

One question per ticket, sized to one session:

```markdown
# <Ticket title>

- **Type**: research | prototype | grilling
- **Status**: open | claimed | resolved | out-of-scope
- **Claimed-by**: <empty | session label, e.g. user@host or "cursor-session">
- **Blocks**: <comma-separated ticket slugs, or none>
- **Blocked-by**: <comma-separated ticket slugs, or none>

## Question

<the decision or investigation this ticket resolves>

## Answer

<!-- filled on resolution -->
```

**Claiming:** set `Claimed-by` and `Status: claimed` **before any work**, then re-read the file to confirm the claim is still yours. Lost the race → pick another. Never take a ticket claimed by someone else, however stale — releasing a stale claim is the human's call.

A ticket is **unblocked** when every slug in `Blocked-by` has `Status: resolved` (or no blockers). The **frontier** is open (or claimed by you), unblocked tickets. Prefer wiring `Blocks` / `Blocked-by` after all ticket files exist (create-then-wire).

## Ticket Types

Every ticket is **HITL** — human in the loop — or **AFK**, driven by the agent alone. A HITL ticket only resolves through that live exchange; the agent never stands in for the human.

- **Research** (AFK) — read external docs, APIs, or knowledge bases; output a linked markdown summary under the effort folder or `.scratch/_wayfinder/<effort>/assets/`.
- **Prototype** (HITL) — cheap concrete artifact via `/prototype`; link the asset.
- **Grilling** (HITL) — `/grilling`, one question at a time. Default.

## Fog of war

The map is deliberately incomplete — don't chart what you can't yet see. **Not yet specified** holds in-scope fog. Graduating a patch into tickets clears it from the fog.

**Fog or ticket?** Can you state the question precisely now — _not_ answer it, _phrase_ it?

- **Ticket** when sharp — even if blocked.
- **Fog** when not.

## Out of scope

Work beyond the **destination** is not fog. Close such tickets as `out-of-scope` and add one line under **Out of scope** on the index.

## Invocation

Two branches. **Never resolve more than one ticket per session.**

### Chart the map

Loose idea in.

1. Pin the **destination** with the user — before any ticket exists.
2. Pick an `<effort-slug>` (kebab-case) and create `.scratch/_wayfinder/<effort-slug>/`.
3. `/grilling` to surface open decisions — one question at a time. **If no fog surfaces, stop**: the journey fits one session — no map needed; ask how they'd like to proceed (often `/plan`).
4. Write `decision-map.md`: Destination and Notes filled, Decisions so far empty, fog under Not yet specified.
5. Create specifiable tickets under `tickets/`, then wire `Blocks` / `Blocked-by` in a **second pass**; everything you can't yet specify stays in the fog; anything beyond the destination goes to Out of scope. Trivially-decidable tickets may resolve in this session and append to Decisions so far.
6. **Done when** the index and wired tickets exist — do not resolve other tickets in this session. Tell the user the effort path.

### Work through the map

Map path in (e.g. `.scratch/_wayfinder/<effort-slug>/`). Ticket slug optional; without one, pick from the frontier.

1. Load **`decision-map.md`** — not every ticket body — and orient to the Destination.
2. If the user named a ticket, use it; otherwise pick from the frontier — when several are open, choose the one this session is best positioned to resolve, or pick at random when indifferent. **Claim** it (edit the ticket file), then re-read to confirm; lost the race → pick another. If the frontier is empty, **stop and report**.
3. Resolve — zoom into the ticket and blockers on demand; invoke skills named in Notes; default to `/grilling`. Respect HITL.
4. Record resolution in the ticket (`## Answer`, `Status: resolved`, clear `Claimed-by`); append one gist line to Decisions so far on the index.
5. Create-then-wire new tickets; graduate fog; rule out-of-scope work; update or delete invalidated tickets.
6. **Done when** one ticket is resolved — or, if no open tickets remain, hand off: offer `/plan` with the map path and Decisions so far as the brief (writes an OpenSpec change under `openspec/changes/`), or implement directly if small.

Parallel frontier work is expected — one `/wayfinder` invocation per window.
