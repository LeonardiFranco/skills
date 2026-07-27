---
name: publish-pr
description: Publish work as a draft pull request — commit, push, write the description via write-pr, open the PR. Use when the user says "publish the PR" (or asks to open a PR from the current branch), or when the execute skill's publish-on-approve opt-in fires after an APPROVE verdict.
---

# Publish PR

Turn finished work into a **draft** pull request: commit, push, invoke `write-pr` for the description, publish. Two entry paths feed one shared tail. Host commands and description conventions come from the project's local agent config (PR host section, see `/setup-fleonardi-skills`) and steering — never from this skill. Missing PR-host config → stop and report, offering `/setup-fleonardi-skills`.

## Entry paths

### After execute APPROVE (publish-on-approve opt-in)

The execute reviewer invokes this path after an APPROVE verdict when local steering opts in. Execute reads the opt-in and gates the call — this skill assumes it was intentional. Never enter from REVISE or BLOCK.

The caller supplies the **worktree path**, the **plan file path (or slug)**, and the plan's **Category**.

1. **Derive the branch name**: take the plan filename's slug (the part after the leading `NNN-`/`NN-`, without `.md`), prefix it by Category — `bug`/`security` → `fixes`, `direction` → `features`, `tech-debt` → `refactors`, else `chores` — as `{prefix}/{slug}`; on collision with an existing branch append `-2`, `-3`, …

   **Done when:** the branch name is stated and collides with no existing local or remote branch.

### Standalone ("publish the PR")

Publish the **current checked-out feature branch as-is** — never derive plan/category branch names on this path. Preconditions, all required:

- an unambiguous user request to publish;
- a git repository;
- a non-detached, non-default branch;
- PR-host config.

Any missing → stop and report which one. **Dirty-tree rule:** a clean tree publishes the committed tip with no commit step; dirty files are committed only when the publish request and the file set unambiguously belong together — otherwise stop and ask.

**Done when:** every precondition is verified and the publish scope — committed tip, or tip plus named dirty files — is stated.

## Shared tail

Execute path works in the worktree on the derived branch; standalone works on the current branch.

2. **Commit** what the entry path scoped, with a conventional `<type>: <subject>` message following the project's commit conventions. Skip when there is nothing to commit.

   **Done when:** the working tree is clean.

3. **Push** the branch to origin.

   **Done when:** the remote branch exists at the local tip.

4. **Invoke `write-pr`** against the pushed branch. On the execute path, pass the plan context so it takes its plan-driven entry; standalone uses the branch diff and commit log.

   **Done when:** the description file exists under `.scratch/_write-pr/`.

5. **Publish a draft PR** with the written description, targeting the default branch, via the PR-host config's commands — honoring any team description conventions (length caps, sections posted as comments).

   **Done when:** the draft PR exists and its URL is reported.

## Rules

- **Draft-only.** Never flip a PR draft→active, vote, approve, or merge — human acts.
- **Partial failure:** stop and report exactly what completed (e.g. "branch pushed, PR not created — run: `az …`"); the tail is retryable.
- **Idempotent retry:** on re-entry, check what already exists — branch pushed, description written, draft PR open — and skip completed steps. Never open a second draft PR for the same head without an explicit user request.
