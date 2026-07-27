---
name: review-pr
description: >-
  Review a pull request with fresh eyes and post the findings as PR comments —
  typically a draft PR published by /publish-pr, before the human spends manual
  testing time. Never votes; never edits code.
argument-hint: "<pr-id> [<effort: low|high>]"
disable-model-invocation: true
---

# Review PR

Review a published pull request like a second tech lead who did **not** see the plan or the
executor session — fresh eyes on exactly the artifact a human reviewer sees — then post findings
as labeled PR comments. This complements `/execute`'s pre-publish review (which has full plan
context); the value here is the absence of that context: no anchoring on the plan session's
assumptions.

**Never vote or approve on the host**, even where a command for it exists: the CLI authenticates
as the human user, so an agent vote would masquerade as their judgment and may satisfy
branch-policy reviewer requirements. Votes, draft→active flips, and merges are human-only.
**Never edit code** — findings are comments, not fixes.

## Preconditions

- The project's local agent config (discovered via steering, e.g. a `PR host` section alongside
  the issue-tracker config) names this repo's PR host and the commands for: PR metadata, listing
  comment threads, posting a comment thread, and fetching the PR's merge or source ref. Missing →
  stop and say what's needed (offer `/setup-fleonardi-skills` to record it).
- A PR id (or URL to extract it from). Without one: list active PRs (including drafts) per the
  host config and ask which to review.

## Step 1 — Fetch the PR state

Using the host config's commands:

1. PR metadata — title, description, source/target branches, draft flag, author.
2. Existing comment threads — conventions may put part of the contract there (e.g. test cases
   posted as the first comment).
3. The PR's **merge ref** if the host exposes one, fetched into a **detached scratch worktree**;
   if unavailable (e.g. conflicted PR), fall back to fetching the source branch and diffing
   three-dot against the target — and say so in the summary.

The description plus any convention-mandated comments are the stated intent — the contract to
review against. **Never check the PR branch out in the user's working tree** — always a detached
worktree; remove it when done (`git worktree remove`).

**Completion criterion:** metadata and existing threads read, and the diff base established in a
detached worktree — merge ref, or the three-dot fallback noted for the summary.

## Step 2 — Review

Diff = merge state vs target, computed inside the worktree with plain git. Read project steering
(`AGENTS.md` etc.) first; it defines the conventions axis.

Judge every hunk on:

1. **Intent** — does the change do what the PR description claims? Anything the description
   promises but the diff lacks, or the diff does but the description hides?
2. **Conventions** — steering rules, analyzer constraints, patterns of the surrounding code.
3. **Correctness** — bugs, edge cases, contract breaks — whatever steering marks load-bearing.
4. **Test cases** — do the stated test cases actually exercise the changed behavior? Would a
   stranger following them touch the risky paths?

Effort scales like a tech-lead review: default skim-every-hunk; `high` → read every hunk closely
and chase call sites in the worktree.

**Completion criterion:** steering read first, and every hunk judged on all four axes at the
requested effort.

## Step 3 — Report and post

1. Write the findings file first: `.scratch/_pr-review/<id>.md` — verdict line
   (LOOKS GOOD / FINDINGS / BLOCKING), then findings ordered by severity, each with file:line,
   what, why it matters, and a suggestion. This file is the durable record.
2. Post to the PR per the host config's comment command, every comment prefixed
   **`[agent review]`** so nothing masquerades as the human: one **summary thread** (verdict +
   finding count + pointer lines), plus optionally one thread per significant finding,
   file/line-anchored if the host supports it.
3. Leave threads unresolved/active — resolving them is the human reviewer's act.

**Completion criterion:** findings file on disk, every posted comment carries the
`[agent review]` prefix, and all threads left active.

## Completion criterion

Findings file written, summary thread posted (URL reported), worktree removed, and the user told
the verdict plus what to do next (test and flip the draft, or send findings back through
`/execute` REVISE / `reconcile`). No votes cast, no code edited, user's working tree untouched.
