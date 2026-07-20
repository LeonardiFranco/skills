---
name: cleanup
description: Reset to a fresh local dev after finishing plan execution.
disable-model-invocation: true
---

# Cleanup

Return the working tree to the default branch synced with its origin. Run this after **`/execute`** (merge or abandon) when you're done for now.

## Preconditions

- The repo has a remote **`origin`** with a resolvable default branch. If not, stop and say what you found.

## Steps — sync the default branch

### Resolve `<default>`

The branch project steering (`AGENTS.md`/`CLAUDE.md`) names as the working default; if steering is silent, `git symbolic-ref --short refs/remotes/origin/HEAD` (strip the `origin/` prefix), falling back to the "HEAD branch" line of `git remote show origin`.

### Sync

Do the sync yourself, don't bounce the user. First, guard against stranding work: `git log --oneline origin/<default>..<default>` — local commits sitting on `<default>` would be silently rewound by the `checkout -B` below. Any found → stop and report them; rescuing is the user's call. Then:

```bash
git fetch origin
git checkout -B <default> origin/<default>
```

(`git pull` would fail on a branch with no upstream — e.g. the one `/execute` leaves you on — and `git branch -f <default>` refuses when `<default>` is already checked out; fetch + `checkout -B` covers both states.)

### If the checkout refuses

Standing local-only edits (e.g. dev-environment config overrides) are expected and not a blocker — the usual refusal cause is a modified file whose destination version differs from HEAD's. Whatever the cause, stop and report exactly what git named. Do not force-reset, stash, or discard on the user's behalf — tell them to resolve those specific files first.

## Completion criterion

All of the following hold:

- Current branch is **`<default>`** (`git branch --show-current` matches it).
- Local **`<default>`** matches **`origin/<default>`** (`git rev-parse` on both agree).
- **`git fetch origin`** completed without error.

Report the short SHA you landed on.
