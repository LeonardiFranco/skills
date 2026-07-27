---
name: conformance
description: Review the changes since a fixed point along two axes — repo standards and originating spec — reported side by side.
disable-model-invocation: true
---

# Conformance

Two-axis review of the diff between `HEAD` and a fixed point the user supplies:

- **Standards** — does the code conform to this repo's documented conventions?
- **Spec** — does the code faithfully implement the originating issue / spec?

Both axes run as **parallel sub-agents** so they don't pollute each other's context, then this skill aggregates their findings.

Sibling: `audit branch` reviews the same range for *health* (bugs, security, debt — a findings table tagged introduced/pre-existing); this skill reviews *compliance*. Pick by question: "what's wrong with these changes?" → `audit branch`; "do these changes follow the rules and the spec?" → here.

Before acting, read the config this skill needs from `docs/agents/` — prefer `docs/agents/local/<name>.md` over `docs/agents/<name>.md` when both exist. If missing or incomplete, ask the user to run `/setup-fleonardi-skills`. Here that means the issue tracker config, used to fetch the originating spec.

## Process

### 1. Pin the fixed point

Whatever the user said is the fixed point — a commit SHA, branch name, tag, `main`/`dev`, `HEAD~5`, etc. If they didn't specify one, ask for it.

Capture the diff command once: `git diff <fixed-point>...HEAD` (three-dot, so the comparison is against the merge-base). Also note the list of commits via `git log <fixed-point>..HEAD --oneline`.

Before going further, confirm the fixed point resolves (`git rev-parse <fixed-point>`) and the diff is non-empty. A bad ref or empty diff should fail here — not inside two parallel sub-agents.

### 2. Identify the spec source

Look for the originating spec, in this order:

1. Issue references in the commit messages (`#123`, `Closes #45`, Azure DevOps `AB#67`, etc.) — fetch via the workflow in the issue-tracker config (see the preamble above).
2. A path the user passed as an argument.
3. The canonical local spec — `.scratch/<requirement-slug>/SPEC.md` (legacy `PRD.md`) — matching the branch name or feature; or a legacy `SPEC-*.md` / `PRD-*.md` under `docs/`, `specs/`, or the repo root.
4. If nothing is found, ask the user where the spec is. If they say there isn't one, the **Spec** sub-agent will skip and report "no spec available".

### 3. Identify the standards sources

Anything that documents how code should be written here: `AGENTS.md` / `CLAUDE.md` and the conventions it points at, `docs/agents/`, `CONTRIBUTING.md` or `CODING_STANDARDS.md` if present, and any enforced-rule definitions (linters / analyzers) the steering names as authoritative.

On top of whatever the repo documents, the Standards axis always carries the **smell baseline** — a fixed set of Fowler code smells (_Refactoring_, ch.3) that applies even when a repo documents nothing, kept in [smells.md](smells.md). Two rules bind it:

- **The repo overrides.** A documented repo standard always wins; where it endorses something the baseline would flag, suppress the smell.
- **Always a judgement call.** Each smell is a labelled heuristic ("possible Feature Envy"), never a hard violation — and, like any standard here, skip anything tooling already enforces.

### 4. Spawn both sub-agents in parallel

Send a single message with two `Agent` tool calls. Use the `general-purpose` subagent for both. Both prompts also carry rules 5 and 6 of [the advisor contract](../improve/references/advisor-contract.md) **pasted verbatim from that file** — never paraphrase them; sub-agents don't inherit them.

**Standards sub-agent prompt** — include:

- The full diff command and commit list.
- The list of standards-source files you found in step 3, **plus the full contents of [smells.md](smells.md) pasted in** — the sub-agent has no other access to it.
- The brief: "Report — per file/hunk where relevant — (a) every place the diff violates a documented standard: cite the standard (file + the rule); and (b) any baseline smell you spot: name it and quote the hunk. Distinguish hard violations from judgement calls — documented-standard breaches can be hard, but baseline smells are always judgement calls, and a documented repo standard overrides the baseline. Skip anything tooling enforces. Under 400 words."

**Spec sub-agent prompt** — include:

- The diff command and commit list.
- The path or fetched contents of the spec.
- The brief: "Report: (a) requirements the spec asked for that are missing or partial; (b) behaviour in the diff that wasn't asked for (scope creep); (c) requirements that look implemented but where the implementation looks wrong. Quote the spec line for each finding. Under 400 words."

If the spec is missing, skip the Spec sub-agent and note this in the final report.

### 5. Aggregate

Present the two reports under `## Standards` and `## Spec` headings, verbatim or lightly cleaned. Do **not** merge or rerank findings — the two axes are deliberately separate (see _Why two axes_).

End with a one-line summary: total findings per axis, and the worst issue _within each axis_ (if any). Don't pick a single winner across axes — that's the reranking the separation exists to prevent.

## Why two axes

A change can pass one axis and fail the other:

- Code that follows every standard but implements the wrong thing → **Standards pass, Spec fail.**
- Code that does exactly what the issue asked but breaks the project's conventions → **Spec pass, Standards fail.**

Reporting them separately stops one axis from masking the other.
