# Scratch workspace

Personal drafting area (gitignored). Consumer skills read this file first as the master index for *disposable* drafts only.

**Plans and specs live in OpenSpec**, not here:

- Active work → `openspec/changes/<slug>/`
- Archived truth → `openspec/specs/<domain>/spec.md`
- Status board → `openspec/changes/README.md`

## Layout

```
.scratch/
├── README.md                 ← you are here (disposable index)
├── _wayfinder/<effort>/      ← wayfinder maps + tickets (never committed)
├── _write-pr/                 ← PR description drafts (write-pr skill)
└── _pr-review/                 ← PR review findings (review-pr skill)
```

- **Wayfinder** → `.scratch/_wayfinder/<effort-slug>/decision-map.md` + `tickets/`
- **PR description** → `.scratch/_write-pr/<branch-slug>-PR.md`
- **PR review findings** → `.scratch/_pr-review/<id>.md`

Legacy `.scratch/_backlog/plans/` or requirement folders, if present, are stale — migrate to OpenSpec via `/plan` rather than extending them.
