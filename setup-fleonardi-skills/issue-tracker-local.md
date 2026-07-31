# Issue tracker: Local Markdown

When this repo has no external tracker, work items are OpenSpec changes. Disposable drafts stay under `.scratch/`.

## Layout

**Source of truth (committed):**

```
openspec/
├── specs/<domain>/spec.md
└── changes/
    ├── README.md
    └── <slug>/
        ├── proposal.md
        ├── design.md
        ├── tasks.md
        └── specs/<domain>/spec.md
```

**Disposable (gitignored):**

```
.scratch/
├── README.md
├── _write-pr/
└── _pr-review/
```

Legacy `.scratch/<requirement>/` or `_backlog/plans/` trees, if present, are stale — migrate via `/plan`.

## Pull requests as a triage surface

**PRs as a request surface: no.** Local markdown has no PR/MR surface — `/triage` is for external trackers.

## When a skill says "publish to the issue tracker"

Route by artifact:

- A **behavioral spec** → `openspec/specs/<domain>/spec.md` (or a change delta under `openspec/changes/<slug>/specs/`)
- A **raw report** for later human triage → optional note under `.scratch/` only if the user wants a scratch file; prefer creating an OpenSpec change via `/plan` when it's actionable work

## When a skill says "publish a plan to the issue tracker"

**Unsupported — skip with explanation.** Changes already live in `openspec/changes/<slug>/` as the handoff artifact. Do not duplicate them into `.scratch/`. Write the change folder normally and say that publish was skipped because the local tracker has no separate distribution surface.

## When a skill says "fetch the relevant ticket"

Read the OpenSpec change folder or spec path the user named.
