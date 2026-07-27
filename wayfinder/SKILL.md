---
name: wayfinder
description: Plan a huge chunk of work — more than one agent session can hold — as a shared map of investigation tickets, resolved one at a time until the way to the destination is clear.
disable-model-invocation: true
---

A loose idea too big for one session, wrapped in **fog** — the way from here to the **destination** isn't visible yet. Wayfinding finds that way; it doesn't charge at the destination. **Chart** the map, then work the **frontier** one ticket at a time until the way is clear.

The destination varies per effort — a spec to hand off, a decision to lock before planning, a change made in place — and **naming it is the first act of charting**: it fixes the scope every ticket is measured against.

## Plan, don't do

Each ticket resolves a decision; the map is done when nothing is left to decide before someone goes and builds the thing. The pull to just do the work is usually the signal you've reached the edge of the map and it's time to hand off. An effort can override this in its Notes — absent that, produce decisions, not deliverables.

## Agent skills config

Before acting, read the config this skill needs from `docs/agents/` — prefer `docs/agents/local/<name>.md` over `docs/agents/<name>.md` when both exist. If missing or incomplete, ask the user to run `/setup-fleonardi-skills`. Here that means the issue tracker config; consult its **Wayfinding operations** section for maps, tickets, blocking, claiming, and frontier queries.

## Refer by name

In everything the human reads — narration, Decisions so far, the fog — use the ticket **title**, never a bare id, number, or slug. The id or URL doesn't vanish — the name wraps its link — but rides inside the name, never stands in for it.

## The Map

The map is the canonical **index** for one wayfinding effort — not a store. It gists decisions and links to the tickets that hold their detail; each decision lives in exactly one ticket. Physical layout is tracker-specific (Wayfinding operations).

Assets link from the ticket or index; never paste them in.

### Index body

Load once per session at low resolution. Open tickets are **not** on the index — find them via the frontier query in issue-tracker config.

```markdown
## Destination

<what reaching the end of this map looks like — the spec, decision, or change this effort is finding its way to. One or two lines; every session orients to it before choosing a ticket.>

## Notes

<domain; skills every session should consult; standing preferences for this effort>

## Decisions so far

<!-- one line per closed ticket: enough to judge relevance, then zoom the link for the detail -->

- [<closed ticket title>](link) — <one-line gist>

## Not yet specified

<!-- see Fog of war: in-scope fog you can't ticket yet; graduates as the frontier advances -->

## Out of scope

<!-- see Out of scope: work ruled beyond the destination; closed, never graduates -->
```

### Tickets

One question per ticket, sized to one 100K token session:

```markdown
## Question

<the decision or investigation this ticket resolves>
```

Each ticket has a **type** ([Ticket Types](#ticket-types)) and **claim** state. A session claims a ticket **first**, before any work, so concurrent sessions skip it; where the tracker has assignees, the assignee *is* the claim — an open, unassigned ticket is unclaimed. After claiming, **re-read the ticket to confirm the recorded claim is yours** — if another session's claim is there, you lost the race: back off and pick a different ticket. **A claim is absolute**: never work a ticket claimed by another session, even one that looks stale or abandoned — releasing a stale claim is the human's decision, not yours (an agent overriding a claim has broken HITL). Prefer the tracker's **native** blocking relationship — it renders the frontier visually in the tracker's own UI, so the human sees what's takeable without opening the map. A ticket is **unblocked** when every blocker is resolved; the **frontier** is open, unblocked, unclaimed tickets. The answer isn't part of the index — it's recorded on resolution. Mechanics follow issue-tracker config.

## Ticket Types

Every ticket is **HITL** — human in the loop, worked *with* a human who speaks for themselves — or **AFK**, driven by the agent alone. A HITL ticket only resolves through that live exchange; the agent never stands in for the human's side of it (a grilling agent that answers its own questions has broken HITL).

- **Research** (AFK) — read external docs, APIs, or knowledge bases; output a linked markdown summary.
- **Prototype** (HITL) — cheap concrete artifact via `/prototype`; offer the copy-paste command when the session ends; link the asset.
- **Grilling** (HITL) — `/grilling` + `/domain-modeling`, one question at a time. Default.

## Fog of war

The map is deliberately incomplete — don't chart what you can't yet see. Beyond the tickets lies **fog**: decisions you sense are coming but can't pin down while blockers remain open. Resolving a ticket clears fog ahead of it, graduating specifiable patches into fresh tickets until no tickets remain. Graduating a patch clears it from the fog — a question never lingers in both places.

The index **Not yet specified** section holds that dim view — suspected questions, areas to revisit, deferred risks. Everything in it is in scope, just not sharp enough to ticket.

**Fog or ticket?** Can you state the question precisely now — _not_ answer it, _phrase_ it?

- **Ticket** when sharp — even if blocked.
- **Fog** when not. Don't pre-slice: one fog patch may graduate into several tickets, or none.

Fog excludes Decisions so far and existing tickets.

## Out of scope

Fog is gated by *knowledge*; out of scope is gated by *scope*. Work beyond the **destination** is not fog — it never graduates, no matter how far the frontier advances. When charting or working the map turns up a beyond-destination ticket, rule it out: close it, one line in **Out of scope**. It returns only as a fresh effort if the destination is redrawn.

## Invocation

Two branches. **Never resolve more than one ticket per session.**

### Chart the map

Loose idea in.

1. Read issue-tracker config (Wayfinding operations).
2. Pin the **destination** with the user — before any ticket exists.
3. `/grilling` + `/domain-modeling` to surface open decisions — one question at a time. **If no fog surfaces, stop**: the journey fits one session — no map needed; ask the user how they'd like to proceed.
4. Create the index: Destination and Notes filled, Decisions so far empty, fog sketched under Not yet specified.
5. Create specifiable tickets, then wire blocking in a **second pass** (identities must exist first); everything you can't yet specify stays in the fog; anything beyond the destination goes to Out of scope. Trivially-decidable tickets may resolve in this session and append to Decisions so far.
6. **Done when** the index and wired tickets exist — do not resolve other tickets in this session.

### Work through the map

Map reference in (URL, number, or path — per issue-tracker config). Ticket identifier optional; without one, pick the first frontier ticket.

1. Load the **index** — not every ticket body — and orient to the Destination.
2. If the user named a ticket, use it; otherwise pick from the frontier — when several tickets are open, don't default to the first: choose the one this session is best positioned to resolve, or pick at random when indifferent, so parallel plain invocations spread out instead of colliding. **Claim** it per issue-tracker config, then re-read to confirm the claim is yours; lost the race → pick another. If the frontier is empty (everything claimed or blocked), **stop and report** — never take claimed work.
3. Resolve — **zoom** into the ticket and any blockers or related closed tickets on demand; invoke skills named in Notes; default to `/grilling` + `/domain-modeling`. Respect HITL: the human's side of the exchange is theirs.
4. Record resolution per issue-tracker config: answer in the ticket, mark resolved, append one gist line to Decisions so far.
5. Create-then-wire new tickets; graduate specifiable fog (clearing it from Not yet specified); rule beyond-destination work out of scope; update or delete invalidated tickets.
6. **Done when** one ticket is resolved — or, if no open tickets remain, hand off: `Invoke /to-spec with the map at <reference>.` (then `/to-tickets`) for a multi-session build, or implement directly if small.

Parallel frontier work is expected — one `/wayfinder` invocation per window. Naming the ticket is optional: plain invocations stay safe through the de-correlated pick plus claim-then-verify.
