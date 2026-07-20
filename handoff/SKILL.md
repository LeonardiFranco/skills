---
name: handoff
description: Compact the current conversation into a handoff document for another agent to pick up.
argument-hint: "What will the next session be used for?"
disable-model-invocation: true
---

Write a handoff document summarising the current conversation so a fresh agent can continue the work. Save to:

```
.scratch/_handoff/<timestamp>-handoff.md
```

Use an ISO-8601 timestamp compact form (e.g. `20260629T143022`). Create `.scratch/_handoff/` if needed. If `.scratch/` does not exist, ask the user for a path.

Include a "suggested skills" section: each suggestion names a skill and the remaining step it serves — no untethered suggestions.

Do not duplicate content already captured in other artifacts (specs, plans, ADRs, issues, commits, diffs). Reference them by path or URL instead.

Redact any sensitive information, such as API keys, passwords, or personally identifiable information.

If the user passed arguments, treat them as a description of what the next session will focus on and tailor the doc accordingly.

**Completion criterion:** a fresh agent given only the document — and the artifacts it references — could continue without re-asking: the goal, what's done vs what remains, each decision with its why, and every suggested skill tied to a remaining step.
