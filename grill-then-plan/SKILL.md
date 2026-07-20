---
name: grill-then-plan
description: Run a grilling session, then turn the settled decisions into self-contained implementation plans via /plan.
disable-model-invocation: true
---

Run a `/grilling` session. If this conversation already holds a settled grilling, skip straight to the handoff.

Once grilling reaches shared understanding, hand the settled **design tree** to `/plan` as a description brief — the grilling *is* the audit, so `/plan` skips its own audit and plans straight from your decisions.

The brief is the **settled tree** the [`grilling`](../grilling/SKILL.md) skill defines — carry it whole, so the plans come out self-contained and `/plan` never re-opens a closed branch.

When the plan matters enough to pay for two drafts, hand the same brief to `/duel` instead — two independent candidates, blind-judged.

Then invoke `/plan` with the brief. `/plan` already skips the audit on a description brief and does its own light recon for the project's verification gates; instruct it to: write one self-contained plan per independently-executable unit in `.scratch/_backlog/plans/`, honour the sequencing, and **ask only what the brief leaves open — never re-litigate a settled branch.**
