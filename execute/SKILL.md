---
name: execute
description: Dispatch a cheaper executor subagent to implement one OpenSpec change, then review its diff like a tech lead and render a verdict. Use when asked to execute or implement a change from openspec/changes/. Prefer this over stock /opsx-apply.
license: MIT
---

# Execute

Get one OpenSpec change implemented without ever editing code yourself. A *separate executor subagent* writes the code; you dispatch it, then review its diff like a tech lead reviewing a PR against the change folder, and render a verdict. **Merging is the user's decision — never merge, push, or commit to their branch.**

**First, read [the advisor contract](../improve/references/advisor-contract.md).** The founding rule survives here unchanged: *the advisor never edits source code.* Running verification commands inside the executor's disposable worktree is fine — the no-mutating-commands rule protects the user's working tree, not the worktree. The same boundary applies to the `publish-pr` tail (invoked from Verdict on APPROVE): committing and pushing the *worktree's own branch* never touches the user's working tree or branches, so it is within the contract; the user's branches and the default branch remain untouchable.

**AIMS / IIS note:** if project steering forbids git worktrees, dispatch in the current working tree instead (still never edit code yourself — the executor subagent does). Honor steering over this skill's default isolation.

Requires a host agent that can spawn subagents. If yours can't, say so and hand the change over for manual execution — the change's own executor instructions still require updating `openspec/changes/README.md` when done.

## Index maintenance (mandatory)

Every change lives under `openspec/changes/<slug>/`. The index is always `openspec/changes/README.md`.

On **APPROVE**: set that change's status row to `DONE` (note verification date; human-only build gates may stay pending). Offer archive: merge delta specs into `openspec/specs/` and move the folder to `openspec/changes/archive/` (CLI `openspec archive` when available).

On **BLOCK**: set status to `BLOCKED` with a one-line reason.

**Who updates:** the agent that finishes the flow — executor if running solo; the execute reviewer if using subagent dispatch.

## Hot vs cold start

- **Hot** — you wrote (or just reviewed) this change in this session. Dispatch from live context; you may inline extra context into the executor prompt beyond the change folder. The folder remains the record.
- **Cold** — a fresh session. Read the full change folder (`proposal.md`, `design.md`, `tasks.md`, delta specs); it is the whole brief. **Do not rebuild the planning session's context or re-verify the change's facts** — the executor rediscovers current state itself, and STOP conditions catch a contradicted decision. Your heavy work is the Review, not pre-flight.

## Preconditions (check all before dispatching)

- The repo is a git repository. If not: stop and say so.
- The change folder exists under `openspec/changes/<slug>/` with `proposal.md`, `design.md`, and `tasks.md`. Dependencies show DONE in `openspec/changes/README.md`. If not: stop, name the missing dependency.
- On a cold start, sanity-check the premise only — does proposal "Why" still describe a real problem on current HEAD? Cheap reads, not a re-verification. Problem already fixed → mark REJECTED in the index instead of dispatching.

## Dispatch

Spawn **one** general-purpose subagent. Executor model: the cheap default, or what the user named (`execute <slug> <model>`).

**Isolation:** pass `isolation: 'worktree'` on the Agent call when project steering allows it — otherwise run in the current working tree per steering.

The subagent prompt must contain:

1. **The full change folder text, inlined** (`proposal.md`, `design.md`, `tasks.md`, and any delta specs). The executor's worktree only sees committed files, so an uncommitted change would otherwise be unreadable — always inline it. On a hot start, append any extra context worth carrying (labeled as advisor notes, distinct from the change).
2. **The executor preamble:**

> You are the executor for the OpenSpec change below. It gives you decisions,
> boundaries, and done criteria; its "Suggested approach" is advice, not law.
> Do your own reconnaissance of the in-scope files before editing — the change
> deliberately carries no code excerpts; the live code is the ground truth.
> Meet every done criterion in tasks.md and confirm the expected result of each
> verification gate in design.md. Touch only the files listed as in scope. If
> any STOP condition occurs, stop immediately and report — do not improvise
> around a contradicted decision. Honor the project's build/commit policy from
> design.md: do not run build/compile commands marked as human-only gates, and
> do not commit, push, switch branches, or create worktrees unless the change
> or operator explicitly says to. Run only the verification the change
> prescribes. Check off tasks.md items as you complete them.
> **Index:** you were dispatched by an execute reviewer — do **not** update
> `openspec/changes/README.md`; the reviewer updates the index after your report.
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

Review like a tech lead, never fixing anything yourself. **The done criteria are the contract; the review judges outcomes, not compliance with the suggested approach.**

1. **Re-run every done criterion** (skip only human-only build gates). Don't trust the report — verify.
2. **Scope compliance** against the change's in-scope list: `git -C <worktree> diff --stat` (or `git diff --stat` if no worktree). Any file outside scope fails review, full stop.
3. **Read the diff.** Every hunk must trace to the change's intent and decisions. Judge against proposal "Why", design Decisions, and named conventions. Reject out-of-scope changes. Scale depth to **Risk**: LOW → skim; MED/HIGH → read every hunk closely.
4. **Audit new tests** when the project has a suite — read what they assert. Never skim this step.

## Verdict

**Documented deviations are judged on merit, not reflex-blocked.** Approve if the adaptation serves the change's intent and stays in scope; treat *undocumented* deviations as review failures.

| Verdict     | When                                                                     | Action                                                                                                                                                                                                               |
| ----------- | ------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **APPROVE** | Criteria pass, scope clean, quality holds                                | **Edit** `openspec/changes/README.md` — set this change's row to `DONE`. Present diff summary, worktree path/branch if any, NOTES. **Publish (opt-in):** if local steering has "publish on approve", invoke **`publish-pr`**. Without the opt-in, offer **`write-pr`**. Offer **archive** of the change into `openspec/specs/`. **Merging is the user's call.** |
| **REVISE**  | Fixable gaps                                                             | Send the same executor specific, actionable feedback. **Max 2 revision rounds**, then BLOCK. Index unchanged until APPROVE or BLOCK. |
| **BLOCK**   | STOP condition hit, scope violated unrecoverably, or revisions exhausted | **Edit** `openspec/changes/README.md` — set row to `BLOCKED` with reason. Tell the user what happened; refine or rewrite the change via `plan` / `vet`. |

**Completion criterion:** a rendered verdict backed by your own re-run of the done criteria and your own read of the full diff — never the executor's report alone — **with** `openspec/changes/README.md` **updated** and the user told what to do next (merge, archive, or what's blocked).
