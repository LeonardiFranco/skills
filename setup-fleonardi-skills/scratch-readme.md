# Scratch workspace

Personal drafting area (gitignored). Consumer skills read this file first as the master index.

## Layout

```
.scratch/
├── README.md                 ← you are here (requirements index; not the plan backlog)
├── <requirement-slug>/
│   ├── SPEC.md
│   ├── tickets.md
│   ├── issues/
│   └── plans/
│       ├── README.md
│       └── <NN>-<slug>.md
├── _backlog/
│   └── plans/
│       ├── README.md         ← cross-cutting advisor plans
│       └── <NNN>-<slug>.md
├── _write-pr/                 ← PR description drafts (write-pr skill)
├── _handoff/                  ← session handoff docs (handoff skill)
├── _improve-architecture/      ← HTML architecture reviews
└── _duel/                      ← duel candidates + verdicts, one slug folder per duel
```

- **Spec** → `.scratch/<requirement-slug>/SPEC.md` (legacy: `PRD.md`)
- **Tickets** → `.scratch/<requirement-slug>/tickets.md`
- **Issue** → `.scratch/<requirement-slug>/issues/<NN>-<slug>.md`
- **Requirement plan** → `.scratch/<requirement-slug>/plans/<NN>-<slug>.md`
- **Backlog plan** → `.scratch/_backlog/plans/<NNN>-<slug>.md`
- **PR description** → `.scratch/_write-pr/<branch-slug>-PR.md`
- **Handoff doc** → `.scratch/_handoff/<timestamp>-handoff.md`
- **Architecture review** → `.scratch/_improve-architecture/architecture-review-<timestamp>.html`
- **Duel** → `.scratch/_duel/<slug>/` (candidate-a, candidate-b, merged; user cleans up)

## Requirements

| Slug | Spec | Plans | Notes |
|------|-----|-------|-------|
| *(none yet)* | | | |

Add a row when you create a requirement folder under `.scratch/<requirement-slug>/`.

## Plan backlogs

| Tree | Index |
|------|-------|
| Cross-cutting / audit-driven | [`.scratch/_backlog/plans/README.md`](_backlog/plans/README.md) |
| Per-requirement | `.scratch/<requirement-slug>/plans/README.md` when that requirement exists |

Do not duplicate full backlog tables here — each plans README owns execution order and status.
