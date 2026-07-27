---
name: judge
description: Judge two or more candidate artifacts produced for the same brief — select a dominant winner unchanged, or synthesize a merged version on a split decision. Use when asked to judge, compare, or choose between candidate versions of a plan/spec/report, or when another skill produces multiple candidates for one slot.
---

# Judge

Take two or more **candidates** — artifacts produced for the same brief (plans, specs, reports, any document a skill emits) — and render a verdict: one candidate wins unchanged, or a merged version beats them all. Read-only on source and on the candidates themselves; the only file you may write is the merged artifact.

**Altitude guard:** you judge relative merit against the shared bar, not each artifact in isolation. Full functional vetting of the winner is `vet`'s job; template polish is `/plan tighten`. And substance outranks style — length, polish, and confident prose are not evidence.

## Steps

1. **Pin the bar.** State what the candidates were supposed to deliver — the shared brief, requirement, or the purpose the artifacts themselves declare. If the candidates disagree about what they're even for, stop and ask; you can't judge two answers to different questions.

   **Completion criterion:** the bar is stated in a sentence or two that every candidate can be measured against.

2. **Read each candidate in full, alone.** One pass per candidate, noting strengths and weaknesses against the bar *before* any side-by-side comparison — first-read anchoring and "longer looked thorougher" are the biases to beat.

   **Completion criterion:** per-candidate notes exist for every candidate, written before any comparison.

3. **Set the dimensions.** Derive 3–6 judging dimensions from the bar (e.g. correctness of as-is claims, scope fit, approach quality, coverage). Dimensions come from what the brief needs, not from whatever the candidates happen to differ on.

   **Completion criterion:** 3–6 dimensions named, each traceable to the bar.

4. **Score per dimension.** For each dimension, name the winner with quoted evidence from every candidate. Where candidates make *contradictory factual claims* and the contradiction decides a dimension, check reality (codebase, corpus) — prose quality cannot settle a fact.

   **Completion criterion:** every dimension has a named winner (or tie) with quoted evidence from every candidate; any deciding factual contradiction was checked against reality.

5. **Verdict.** If one candidate **dominates** — wins or ties every dimension — select it unchanged; grafting from a strictly worse candidate is churn, and "select as-is" is a first-class outcome, not a cop-out. Only a **split decision** — each candidate winning dimensions the other loses — earns synthesis. If the caller asked select-only, stop here and report.

   **Completion criterion:** dominance or split decision declared, with the per-dimension tally behind it.

6. **Synthesize (split decision only).** Base = the overall winner; **graft** the losing candidates' winning parts into it — take sections whole, don't average prose. Then re-read the merged artifact end to end: no orphaned references to dropped parts, no contradiction between grafted and native sections. Write it to the path the caller named, or beside the candidates with a `-merged` suffix; never overwrite or delete a candidate.

   **Completion criterion:** merged artifact on disk and re-read whole — no orphaned references or contradictions — with every candidate untouched.

## Report

End with: the verdict, the per-dimension score with the deciding evidence, and — if synthesized — exactly what was grafted from where and the merged file's path. Route the winner onward as its type demands (plan → `vet` or `execute`; spec → `gap-hunt`).

**Completion criterion:** every dimension scored with evidence quoted from every candidate, contradictory facts settled against reality not rhetoric, and a verdict of select-unchanged or a coherent merged artifact on disk — a winner declared from a single read-through is premature.
