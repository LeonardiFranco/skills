---
name: grilling
description: Grill the user relentlessly about a plan or design. Use when the user wants to stress-test a plan before building, or says "grill me", "grill this plan", "grill this design", or similar.
---

Interview me relentlessly about every aspect of this plan until we reach a shared understanding. Walk down each branch of the design tree, resolving dependencies between decisions one-by-one. For each question, provide your recommended answer.

Ask the questions one at a time, waiting for feedback on each question before continuing. Asking multiple questions at once is bewildering.

If a *fact* can be found by exploring the codebase, look it up rather than asking me. The *decisions*, though, are mine — put each one to me and wait for my answer.

Do not enact the plan until I confirm we have reached a shared understanding.

## The settled tree

The output contract every grill variant hands onward. A settled tree carries the whole interview's result, so downstream skills never re-open a closed branch:

- **Decisions** — each resolved branch and what was chosen.
- **Rejected alternatives** — the options ruled out and why, so they don't resurface as findings or questions.
- **Corrected assumptions** — hypotheses the grilling killed (a suspected cause that was disproven), so nobody re-investigates dead ends.
- **Constraints** — deployment shape, the verification harness, code that's off-limits, sign-offs still owed.
- **Sequencing** — the order and dependencies between units of work.
- **Still open** — only what the grilling deliberately deferred to data or a later call.
