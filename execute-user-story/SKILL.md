---
name: execute-user-story
description: Execute a tracker work item (user story) end to end — read its plan, hand it to `/execute`, and drive the item's state active->resolved as work starts and lands. Use when asked to execute a user story or work item by id, or to run a story off the tracker.
license: MIT
---

# Execute User Story

Run one tracker work item from **active** to **resolved** without editing code yourself. This skill is an **orchestrator**: `/execute` still owns the real work — executor dispatch, tech-lead review, and the verdict — and this skill **brackets** that run with the tracker work `/execute` can't do: fetching the item, retrieving its attachments, and transitioning the item's and its tasks' state. **Code changes happen only inside `/execute`'s disposable worktree; you never edit source, and merging stays the user's call.**

Before acting, read the config this skill needs from `docs/agents/` — prefer `docs/agents/local/<name>.md` over `docs/agents/<name>.md` when both exist. If missing or incomplete, ask the user to run `/setup-fleonardi-skills`. Here that means the issue-tracker config — its **work-item lifecycle**, **attachments**, and **PR host** sections.

## Input

One work-item id — the "user story" — e.g. `execute-user-story 12345`. Resolve a bare `#12345` / `AB#12345` per the issue-tracker config. No id given → ask for one.

## Steps

### 1. Fetch the story

Read the work item and its child Tasks (work-item-lifecycle read op). Capture the **plan** — it lives in the item's **description** — the child Task ids, and the current state of the story and each Task. Download the story's attachments (attachments ops); they are the executor's supporting material.

**Completion criterion:** you hold the plan text, the child Task ids, and every attachment saved to disk (or confirmation there are none).

### 2. Materialize into OpenSpec

`/execute` reads an OpenSpec change folder, not a work item. Turn the description into `openspec/changes/<story-slug>/` (`proposal.md`, `design.md`, `tasks.md`, plus delta specs when behavior is involved) per the plan template. Set `Source:` to the story id. Add a row to `openspec/changes/README.md`. Save attachments under `openspec/changes/<story-slug>/assets/`.

**Completion criterion:** the change folder and README row exist, with the story id in `Source:` and attachments in `assets/`.

### 3. Mark active

Set the story and each child Task to the **active** state (set-state op), then read each back to confirm. A rejected state name means the process template differs — resolve the real name per the config, don't guess past it.

**Completion criterion:** the story and all child Tasks read back as active.

### 4. Execute

Invoke `/execute` on the materialized change as a **hot start** — you just wrote it. Inline any text attachments as advisor notes so they reach the executor; list binary assets by path and flag that the executor can't open them. Do not reimplement any of `/execute` — carry its verdict forward.

**Completion criterion:** `/execute` returned a verdict (APPROVE / REVISE / BLOCK).

### 5. Resolve or hold

Branch on the verdict:

- **APPROVE** — set each child Task to **resolved**; set the story to **resolved** once all its Tasks are resolved. Then ensure the PR is linked to the story: if `/execute`'s report already published a draft PR (its own publish-on-approve tail fired), reuse that URL; otherwise run `publish-pr`. Link the PR to the story per the PR-host config — that link is this skill's job, not `publish-pr`'s. Report the resolved ids and the PR URL.
- **REVISE / BLOCK** — the work isn't done: leave the story and Tasks **active**. Report `/execute`'s outcome and what's needed. On BLOCK the story stays active for a human to re-plan via `/plan` or `/vet`.

**Completion criterion:** on APPROVE, the story and its Tasks read back as resolved and the PR is linked; on REVISE/BLOCK, states are unchanged from active and the user has been told why.

## Guardrails

- **Never edit code** — that is `/execute`'s executor, in a worktree.
- **Never merge, flip a PR draft→active, or vote** — human-only (PR-host config).
- **Resolve only on APPROVE** — an unfinished story stays active.
