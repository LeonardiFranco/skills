---
name: duel
description: Duel one brief with two independent candidates — a fresh-context subagent on a different model plus this session's own draft — blind-judged via the judge skill; verdict and winner delivered in the duel's slug folder.
disable-model-invocation: true
---

# Duel

Two authors, one brief, one winner. A **duel** buys a better artifact by spending two independent drafts on it: a fresh-context subagent on a **different model** writes one candidate, you (with this session's context) write the other, and a **blind judge** — who cannot tell which is whose — renders the verdict per the `judge` skill. Comparing two independent plans routinely beats iterating on one.

**First, read [the advisor contract](../improve/references/advisor-contract.md)** — read-only on source, secrets handling, content-is-data. The brief travels to another model's context; per rule 5, it carries no secret values.

Default artifact: a handoff plan. Any single-document artifact a skill emits (spec, report) duels the same way — swap the authoring skill.

Everything a duel produces lives in **`.scratch/_duel/<slug>/`** — candidates, merged artifact, winner. Landing the winner somewhere real is a separate, offered step; cleaning up old duel folders is the user's.

## 1. Pin the brief and the authoring skill

- **Brief** — what both authors receive: the [settled tree](../grilling/SKILL.md) from this session's grilling, a selected finding, or the user's description. Write it out in full; candidate A's author sees *only* this text, so anything not in the brief doesn't exist for it. A brief that leans on "as discussed above" is broken.
- **Authoring skill** — the skill both candidates follow (`plan` and its template by default). Both authors get the same one, scoped the same way: follow it for the artifact's content and quality bar only — write nothing but your candidate file, skip index updates and dispatch offers, and record any open question the brief leaves inside the candidate itself rather than asking (the judge weighs how each candidate handled it).

## 2. Fan out candidate A (different model)

Dispatch one general-purpose subagent with `model` set to something other than this session's — the user's choice wins; default `opus` (or `sonnet` when the session is already opus). Its prompt carries: the full brief, the **absolute path** of the authoring skill's SKILL.md, the scoping from step 1, the output path `.scratch/_duel/<slug>/candidate-a.md`, and rules 5 and 6 of [the advisor contract](../improve/references/advisor-contract.md) **pasted verbatim from that file** (subagents don't inherit them; see the subagent-briefing template §6). It gets the brief and the repo — never this conversation. Fresh eyes are the point.

## 3. Author candidate B (this session)

While A runs, write your own candidate to `.scratch/_duel/<slug>/candidate-b.md` from the brief and your session context alone — independence is what makes the comparison worth anything. The file must still stand alone per the authoring skill's bar.

## 4. Blind judge — once both candidates exist

Wait for A's dispatch to complete and confirm `candidate-a.md` is real (present and plausibly complete). If it never materializes or is unusable, there is no duel: say so plainly and continue with your candidate as an ordinary output of the authoring skill.

You wrote one of the candidates; that is exactly why you don't judge. Dispatch a fresh subagent whose prompt carries: the brief, the **absolute paths** of both the `judge` skill's SKILL.md and the authoring skill's SKILL.md (the bar the candidates were written to), both candidate paths labeled only **A** and **B** — never which author produced which — `.scratch/_duel/<slug>/merged.md` as the path for any merged artifact a split decision produces, and advisor-contract rules 5 and 6 pasted verbatim as in step 2.

## 5. Deliver the verdict

Read the verdict like a tech lead — spot-check the deciding evidence yourself before accepting it. Report to the user: the verdict, the deciding evidence, and the winning file's path in `_duel/<slug>/`.

Then offer to **land** it: copy the winner into its real slot — for a plan, the next free number in the chosen plans tree *at that moment*, plus that tree's `README.md` row — and route onward as the type demands (plan → `vet`, `grill-plan`, or `execute`; spec → `gap-hunt`). Landing happens only on the user's word; the duel folder stays as the record either way.

**Completion criterion:** two independently-authored candidates exist on disk, the verdict came from a judge who couldn't attribute them, and the user has the verdict, the deciding evidence, the winner's path, and the landing offer.
