---
name: to-spec
description: Turn the current conversation into a spec and publish it to the project issue tracker — no interview, just synthesis of what you've already discussed.
disable-model-invocation: true
---

This skill takes the current conversation context and codebase understanding and produces a spec. Do NOT interview the user — just synthesize what you already know.

Before acting, read the config this skill needs from `docs/agents/` — prefer `docs/agents/local/<name>.md` over `docs/agents/<name>.md` when both exist. If missing or incomplete, ask the user to run `/setup-fleonardi-skills`. Here that means the issue tracker and triage label configs.

## Process

1. Explore the repo to understand the current state of the codebase, if you haven't already. Use the project's domain glossary vocabulary throughout the spec, and respect any ADRs in the area you're touching.

2. Sketch out the seams at which you're going to test the feature. Existing seams should be preferred to new ones. Use the highest seam possible. If new seams are needed, propose them at the highest point you can. The fewer seams across the codebase, the better - the ideal number is one.

Check with the user that these seams match their expectations.

3. Write the spec using the template below.

4. Before publishing, run the `gap-hunt` skill on the draft. Present its findings table for selection; fold accepted gaps into the draft (as specced stories or explicit Out of Scope entries per the chosen disposition). Skip only if the user says to publish as-is.

5. Publish it to the project issue tracker (or to `.scratch/<requirement-slug>/SPEC.md` when using local markdown). Apply the `ready-for-agent` triage label - no need for additional triage.

<spec-template>

## Problem Statement

The problem that the user is facing, from the user's perspective.

## Solution

The solution to the problem, from the user's perspective.

## User Stories

An exhaustive, numbered list of user stories — every aspect of the feature covered by at least one story. Each user story should be in the format of:

1. As an <actor>, I want a <feature>, so that <benefit>

<user-story-example>
1. As a mobile bank customer, I want to see balance on my accounts, so that I can make better informed decisions about my spending
</user-story-example>

## Implementation Decisions

A list of implementation decisions that were made — one decision per bullet, each with its one-line why, so it isn't relitigated downstream. Name modules, interfaces, and seams: names outlive locations. This can include:

- The modules that will be built/modified
- The interfaces of those modules that will be modified
- Technical clarifications from the developer
- Architectural decisions
- Schema changes
- API contracts
- Specific interactions

Do NOT include specific file paths or code snippets. They may end up being outdated very quickly.

Exception for prototype-derived snippets — the rule lives in [the prototype skill's "When done"](../prototype/SKILL.md).

## Testing Decisions

A list of testing decisions that were made. Verification comes from the project's own steering, never assumed: if the project has an automated suite, include —

- A description of what makes a good test (only test external behavior, not implementation details)
- Which modules will be tested
- Prior art for the tests (i.e. similar types of tests in the codebase)

If it doesn't, name the project's actual verification path instead — the surfaces to exercise manually and the checks its steering prescribes.

## Out of Scope

A description of the things that are out of scope for this spec.

## Further Notes

Any further notes about the feature.

</spec-template>
