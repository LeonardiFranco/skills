---
name: to-tickets
description: Break a spec, plan, or the current conversation into a set of tracer-bullet tickets, each declaring its blocking edges — written to .scratch as a tickets file, or as native blocking links on a real tracker.
disable-model-invocation: true
---

# To Tickets

Break a spec, plan, or conversation into a set of **tickets** — tracer-bullet vertical slices, each declaring the tickets that **block** it.

Before acting, read the config this skill needs from `docs/agents/` — prefer `docs/agents/local/<name>.md` over `docs/agents/<name>.md` when both exist. If missing or incomplete, ask the user to run `/setup-fleonardi-skills`. Here that means the issue tracker and triage label configs.

## Process

### 1. Gather context

Work from whatever is already in the conversation context. If the user passes a reference (a spec path, an issue number or URL) as an argument, fetch it and read its full body and comments.

### 2. Explore the codebase (if not already done)

If you have not already explored the codebase, do so to understand the current state of the code. Ticket titles and descriptions should use the project's domain glossary vocabulary, and respect ADRs in the area you're touching.

Look for opportunities to prefactor the code to make the implementation easier. "Make the change easy, then make the easy change."

### 3. Draft the tickets

Break the work into **tracer bullet** tickets, following the **Vertical slice rules**. A **wide refactor** is the exception to that rule — slice it by **expand–contract** instead (see **Wide refactors**).

Give each ticket its **blocking edges** — the other tickets that must complete before it can start. A ticket with no blockers can start immediately.

### 4. Quiz the user

Present the proposed breakdown as a numbered list. For each ticket, show:

- **Title**: short descriptive name
- **Blocked by**: which other tickets (if any) must complete first
- **What it delivers**: the end-to-end behaviour this ticket makes work
- **Stories covered**: which numbered spec stories this ticket addresses (when the source has them)

Ask one question: **"Approve this breakdown?"** Dig into granularity, blocking edges, merges/splits, or story coverage only when the user pushes back on the proposal. Iterate until the user approves the breakdown.

### 5. Publish the tickets to the configured tracker

Publish the approved tickets. **How** depends on the configured tracker — the tickets are the same either way, only the shape of the blocking edges changes:

- **Local markdown** → write `.scratch/<requirement-slug>/tickets.md`, all tickets in dependency order (blockers first), each with its "Blocked by" listing the titles it depends on. Use [tickets-file-template.md](tickets-file-template.md).
- **A real issue tracker** → publish one issue per ticket in dependency order (blockers first) so each ticket's blocking edges can reference real identifiers. Use the platform's native blocking / sub-issue relationship where it has one (mechanics in the issue-tracker doc); otherwise set each ticket's "Blocked by" to the blocking issues. Use [issue-body-template.md](issue-body-template.md). Apply the `ready-for-agent` triage label unless instructed otherwise — the tickets are agent-grabbable by construction.

Do NOT close or modify any parent issue.

Work the **frontier** — any ticket whose blockers are all done — one ticket at a time: `/plan` the ticket into a handoff plan, then `/execute` it, clearing context between tickets. Tickets on the frontier are independent, so several plan→execute lanes can run in parallel. When a ticket is planned, name it in the plan's `Source:` line and carry its blocking edges into the plan's `Depends on` row (as sibling plan numbers once those plans exist).

## Reference

### Vertical slice rules

Each ticket is a thin vertical slice that cuts through ALL integration layers end-to-end, NOT a horizontal slice of one layer.

- Each slice delivers a narrow but COMPLETE path through every layer (schema, API, UI, tests)
- A completed slice is demoable or verifiable on its own
- Each slice is sized to fit in a single fresh context window
- Any prefactoring should be done first

### Wide refactors

A **wide refactor** is one mechanical change — rename a column, retype a shared symbol — whose **blast radius** fans across the whole codebase, so a single edit breaks thousands of call sites at once and no vertical slice can land green. Don't force it into a tracer bullet; sequence it as **expand–contract**. First expand: add the new form beside the old so nothing breaks. Then migrate the call sites over in batches sized by blast radius (per package, per directory), each batch its own ticket blocked by the expand, keeping the build green batch to batch because the old form still exists. Finally contract: delete the old form once no caller remains, in a ticket blocked by every migrate batch. When even the batches can't stay green alone, keep the sequence but let them share an integration branch that all block a final integrate-and-verify ticket — green is promised only there.

### Templates

Both publish forms have a sibling template — [tickets-file-template.md](tickets-file-template.md) for local markdown, [issue-body-template.md](issue-body-template.md) for a real tracker.

In either form, avoid specific file paths or code snippets — they go stale fast. Exception for prototype-derived snippets — the rule lives in [the prototype skill's "When done"](../prototype/SKILL.md).
