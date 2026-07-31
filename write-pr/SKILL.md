---
name: write-pr
description: >-
  Write a pull request description. Use when the user asks for a PR description;
  when the current branch is ready to merge; or when the publish-pr skill needs
  a description for a branch it is publishing.
---

# Write PR

Produce a **filled PR description** — not a summary in chat — by reading project steering, the PR template, and the diff. Step 5 defines where it's written.

Two entry paths; same output:

| Path              | Scope source                                                             |
| ----------------- | ------------------------------------------------------------------------ |
| **Plan-driven** (via `publish-pr`'s execute entry) | OpenSpec change folder (`tasks.md` done criteria / manual verify, `proposal.md` risk & why, design in-scope) + branch diff |
| **Standalone**    | Branch diff + commit log on the current branch                           |

Steering discovery and fallback template: [references/steering-discovery.md](references/steering-discovery.md). Default skeleton when no repo template exists: [references/default-pr-template.md](references/default-pr-template.md).

## Step 1 — Read steering

1. Read `AGENTS.md`, then `CLAUDE.md` and `CONTRIBUTING.md` if present.
2. From steering, resolve:
  - **PR template path** — explicit path in steering wins; else discover per steering-discovery reference.
  - **Default base branch** — explicit name in steering, else user-named, else infer from remote default.
  - **Verification policy** — what counts as proof (manual steps, test commands, CI limits). Never claim checks the project does not run.

**Completion criterion:** you can name the template source (repo file path, or "default fallback") and the base branch used for the diff.

## Step 2 — Resolve the diff

Run read-only:

```bash
git branch --show-current
git log <base>..HEAD --oneline
git diff <base>..HEAD --stat
```

**Plan-driven:** also read the OpenSpec change from context (`proposal.md`, `tasks.md`, `design.md` — done criteria, manual verify, risk, why).

**Completion criterion:** you can state the commit range, major areas touched (from paths, not a raw file dump), and whether the diff exceeds plan scope (note in follow-up section if so).

## Step 3 — Read the template

1. If a repo template was found, read that file — it is the **section skeleton**. Strip HTML/XML comments from the output unless steering says to keep them.
2. If no repo template exists, use [references/default-pr-template.md](references/default-pr-template.md) as the skeleton.

**Completion criterion:** every template section is accounted for (filled, marked N/A, or omitted with reason per steering).

## Step 4 — Draft the description

1. **Opening:** why the change matters and what it does — concrete, not a file list.
2. **Follow the template sections** in order. Check only checklist items that apply; leave author confirmations unchecked.
3. **Test / verification section:** numbered steps a stranger can follow. Source from the plan's manual test plan when present; otherwise derive from the diff and steering's verification policy. No vague "verify it works" steps; no false claims about automated tests the repo lacks.
4. **Risk / impact:** match template boxes or prose; align with plan risk when a plan drove the work.
5. **Follow-up / notes:** deferred work, known limits, out-of-scope diff.
6. **Prose pass:** apply [`ste-writing`](../ste-writing/SKILL.md) to filled prose only — **STE-flavored** for summary, risk, and notes; **strict** for test/verification steps. Leave checklist labels, template headings, identifiers, and command syntax alone.

Do not reproduce secrets. Reference credential *types* and paths only.

**Completion criterion:** a reviewer could validate from the test section alone; filled prose passes the ste-writing self-lint for its mode.

## Step 5 — Persist

Write to:

```
.scratch/_write-pr/<branch-slug>-PR.md
```

`<branch-slug>` = current branch with `/` → `-`. Create `.scratch/_write-pr/` if needed. If `.scratch/` does not exist, ask the user for a path.

Do not commit — and never push — unless asked.

**Completion criterion:** file written; user given the path and one line on risk level plus what they should confirm before opening the PR.