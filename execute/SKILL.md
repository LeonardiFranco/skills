---
name: execute
description: Dispatch a cheaper executor subagent to implement one handoff plan, then review its diff like a tech lead and render a verdict. Use when asked to execute or implement a plan from `.scratch/`.
license: MIT
---

# Execute

Get one plan implemented without ever editing code yourself. A *separate executor subagent* writes the code; you dispatch it, then review its diff like a tech lead reviewing a PR against the spec, and render a verdict. **Merging is the user's decision — never merge, push, or commit to their branch.**

**First, read [the advisor contract](../improve/references/advisor-contract.md).** The founding rule survives here unchanged: *the advisor never edits source code.* Running verification commands inside the executor's disposable worktree is fine — the no-mutating-commands rule protects the user's working tree, not the worktree. The same boundary applies to the `publish-pr` tail (invoked from Verdict on APPROVE): committing and pushing the *worktree's own branch* never touches the user's working tree or branches, so it is within the contract; the user's branches and the default branch remain untouchable.

Requires a host agent that can spawn subagents. If yours can't, say so and hand the plan over for manual execution — the plan's own executor instructions still require updating the plans index when done.

## Index maintenance (mandatory)

Every plan lives in a **plans tree**. The index is always the `README.md` in the **same directory** as the plan file.

On **APPROVE**: set that plan's status row to `DONE` (note verification date; human-only build gates may stay pending).

On **BLOCK**: set status to `BLOCKED` with a one-line reason.

**Who updates:** the agent that finishes the flow — executor if running solo; the execute reviewer if using subagent dispatch.

## Hot vs cold start

- **Hot** — you wrote (or just reviewed) this plan in this session. Dispatch from live context; you may inline extra context into the executor prompt beyond the plan file — things you know that didn't meet the template's inlining bar. The plan file remains the record.
- **Cold** — a fresh session. Read the plan in full; it is the whole brief. **Do not rebuild the planning session's context or re-verify the plan's facts** — the executor rediscovers current state itself, and the plan's STOP conditions catch a contradicted decision. Your heavy work is the Review, not pre-flight.

## Preconditions (check all before dispatching)

- The repo is a git repository. If not: stop and say so.
- The plan file exists and its dependencies show DONE in **that plan tree's** `README.md`. If not: stop, name the missing dependency.
- On a cold start, sanity-check the premise only — does "Why this matters" still describe a real problem on current HEAD? Cheap reads, not a re-verification. Problem already fixed → route to `reconcile` instead of dispatching.
## Dispatch

Spawn **one** general-purpose subagent. Executor model: the cheap default, or what the user named (`execute 003 <model>`).

**Isolation:** always pass `isolation: 'worktree'` on the Agent call — the executor works in a disposable worktree, never the user's working tree.

The subagent prompt must contain:

1. **The full plan file text, inlined.** The executor's worktree only sees committed files, so an uncommitted plan under `.scratch/` would otherwise be unreadable — always inline it so the prompt is self-contained. Never assume. On a hot start, append any extra context worth carrying (labeled as advisor notes, distinct from the plan).
2. **The executor preamble:**

> You are the executor for the plan below. It gives you decisions, boundaries,
> and done criteria; its "Suggested approach" is advice, not law. Do your own
> reconnaissance of the in-scope files before editing — the plan deliberately
> carries no code excerpts; the live code is the ground truth. Meet every done
> criterion and confirm the expected result of each verification gate. Touch
> only the files listed as in scope. If any STOP condition occurs, stop
> immediately and report — do not improvise around a contradicted decision.
> Honor the plan's build/commit policy, which comes from this project's
> steering: do not run build/compile commands the plan marks as human-only
> gates, and do not commit, push, switch branches, or create worktrees unless
> the plan explicitly says to — the user owns those. Run only the verification
> the plan prescribes.
> **Index:** you were dispatched by an execute reviewer — do **not** update
> `README.md`; the reviewer updates the index after your report.
> Before reporting, audit every claim against an actual tool result from this
> session — report only what you have evidence for; if a check failed or was
> skipped, say so plainly. When finished, reply with exactly the report format below.

3. **The report format:**

```
STATUS: COMPLETE | STOPPED
CRITERIA: per done criterion — met/not met + the verification result
STOPPED BECAUSE: (only if STOPPED) which STOP condition, what was observed
FILES CHANGED: list
NOTES: anything the reviewer should know (deviations from the suggested
approach, surprises, judgment calls)
```

## Review

Fresh worktrees share git history but not installed dependencies or build artifacts — the executor may need to install deps first, and a check that resolves from build output may need one build the recon'd command table didn't mention. Expect this; it isn't a deviation.

Review like a tech lead, never fixing anything yourself. **The done criteria are the contract; the review judges outcomes, not compliance with the suggested approach.** This review is the family's single quality gate — the plan deliberately spends nothing on distrust upstream, so don't skimp here.

1. **Re-run every done criterion** in the worktree. Don't trust the report — verify. (Skip only the criteria the plan marks as human-only build gates.)
2. **Scope compliance** against the plan's in-scope list: `git -C <worktree> diff --stat`. Any file outside scope fails review, full stop.
3. **Read the diff.** Treat it as untrusted until reviewed: every hunk must trace to the plan's intent and decisions. Judge it against "Why this matters" (does it solve the actual problem?), the plan's Decisions (was a ruled-out alternative taken silently?), and the conventions the plan named (does it look like the rest of the codebase?). Reject any out-of-scope change, however plausible. Scale depth to the plan's **Risk** field: LOW → skim every hunk for scope, decisions honored, and convention; MED/HIGH → read every hunk closely.
4. **Audit the new tests.** Executors game criteria — a test that asserts nothing passes the suite and proves nothing. Read what the tests assert. Never skim this step, whatever the Risk.

## Verdict

**Documented deviations are judged on merit, not reflex-blocked.** "Do not improvise" exists to stop silent drift; an executor that hits a real obstacle, adapts minimally, and explains it in NOTES did the right thing. Approve if the adaptation serves the plan's intent and stays in scope; treat *undocumented* deviations as review failures.

| Verdict     | When                                                                     | Action                                                                                                                                                                                                               |
| ----------- | ------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **APPROVE** | Criteria pass, scope clean, quality holds                                | **Edit the plan tree's** `README.md` — set this plan's row to `DONE`. Then present to the user: diff summary, the worktree path and branch, plus anything from NOTES. **Publish (opt-in):** if local steering has a "publish on approve" section (project's local agent config, e.g. `docs/agents/local/preferences.md`), invoke **`publish-pr`** (its execute entry), passing the worktree path, the plan file path, and the plan's Category; fold its report — the draft PR URL, or what completed on a partial failure — into your user-facing close. Execute is the sole reader of the opt-in; never publish on REVISE/BLOCK. Without the opt-in, offer **`write-pr`** for a PR description file. **Merging is the user's call.**                         |
| **REVISE**  | Fixable gaps                                                             | Send the same executor specific, actionable feedback (which criterion fails and why, the exact code smell and the convention to use). **Max 2 revision rounds**, then BLOCK. Index unchanged until APPROVE or BLOCK. |
| **BLOCK**   | STOP condition hit, scope violated unrecoverably, or revisions exhausted | **Edit the plan tree's** `README.md` — set row to `BLOCKED` with reason. Tell the user what happened; hand the obstacle to `reconcile` to refine or rewrite the plan.                                                |

**Completion criterion:** a rendered verdict backed by your own re-run of the done criteria and your own read of the full diff — never the executor's report alone — **with the correct plans** `README.md` **updated** and the user told what to do next (merge, or what's blocked).
