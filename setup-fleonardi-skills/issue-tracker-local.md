# Issue tracker: Local Markdown

Issues and specs for this repo live as markdown files under `.scratch/`. Master index: `.scratch/README.md`.

## Layout

```
.scratch/
├── README.md
├── <requirement-slug>/
│   ├── SPEC.md
│   ├── tickets.md
│   ├── issues/
│   │   └── <NN>-<slug>.md
│   └── plans/
│       ├── README.md
│       └── <NN>-<slug>.md
└── _backlog/
    └── plans/
        ├── README.md
        └── <NNN>-<slug>.md      ← cross-cutting advisor plans
```

- **Spec**: `.scratch/<requirement-slug>/SPEC.md` (legacy requirements may have `PRD.md` — treat it as the spec)
- **Tickets**: `.scratch/<requirement-slug>/tickets.md` — tracer-bullet tickets with blocking edges, from `/to-tickets`
- **Issue**: `.scratch/<requirement-slug>/issues/<NN>-<slug>.md` — raw incoming reports (bugs, requests) awaiting `/triage`; not produced by the spec→tickets flow
- **Requirement plan**: `.scratch/<requirement-slug>/plans/<NN>-<slug>.md`
- **Backlog plan**: `.scratch/_backlog/plans/<NNN>-<slug>.md`
- Triage state is a `Status:` line near the top of each issue file (see `triage-labels.md`)
- Comments append under a `## Comments` heading at the bottom of the issue file

## Pull requests as a triage surface

**PRs as a request surface: no.** Local markdown has no PR/MR surface — `/triage` covers issue files only.

## When a skill says "publish to the issue tracker"

Route by artifact:

- A **spec** → `.scratch/<requirement-slug>/SPEC.md`
- **Tickets** (`/to-tickets`) → `.scratch/<requirement-slug>/tickets.md`
- A **raw report** — a bug or request being filed for later `/triage` — → a new file under `.scratch/<requirement-slug>/issues/` (creating directories as needed). This is the only thing that belongs in `issues/`; planned tickets never go there.

## When a skill says "publish a plan to the issue tracker"

**Unsupported — skip with explanation.** Plans already live in `.scratch/.../plans/` as the local handoff artifact. Do not duplicate them into `issues/` or `tickets.md`. Write the plan files normally and say that publish was skipped because the local tracker has no separate distribution surface for plans.

## When a skill says "fetch the relevant ticket"

Read the file at the referenced path. The user will normally pass the path or issue number directly.

## Wayfinding operations (`/wayfinder`)

Wayfinder maps live under `.scratch/<requirement-slug>/`:

```
.scratch/<requirement-slug>/
  decision-map.md          # index: Destination, Notes, Decisions so far, Not yet specified, Out of scope
  tickets/
    relational-db.md       # one file per ticket
  assets/                  # research summaries, prototypes (linked, not pasted)
```

- **Map (index)**: `.scratch/<requirement-slug>/decision-map.md`
- **Ticket**: `.scratch/<requirement-slug>/tickets/<slug>.md` — slug is a short dash-case id (e.g. `relational-db`); title in the `#` header
- **Asset**: `.scratch/<requirement-slug>/assets/`

Each ticket file:

```markdown
# Relational Or Non-Relational Database?

Blocked by: <slug>, <slug>
Status: open | in-progress | resolved
Claimed: <who> <YYYY-MM-DD>   ← only while in-progress
Type: Research | Prototype | Grilling

## Question

<the decision or investigation this ticket resolves>

## Answer

<filled on resolution>
```

- **Claiming**: set `Status: in-progress` plus a `Claimed: <who> <YYYY-MM-DD>` line and save **before any work** — then re-read the file to confirm the recorded claim is yours; if another session's claim is there, back off and pick a different ticket. Never clear or override someone else's claim, however stale it looks — the human releases claims.
- **Blocking**: `Blocked by:` slugs; unblocked when all blockers are `resolved`
- **Frontier query**: scan `tickets/*.md` for `Status: open` with satisfied `Blocked by`
- **Resolution**: write `## Answer`, set `Status: resolved`, append one gist line to the index Decisions so far
- **Create-then-wire**: create all ticket files first, then add `Blocked by` in a second pass
