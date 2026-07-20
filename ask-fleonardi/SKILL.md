---
name: ask-fleonardi
description: Ask which skill or flow fits your situation. Router over the engineering and advisor skills in this repo.
disable-model-invocation: true
---

# Ask fleonardi

You don't remember every skill, so ask.

Two families share this repo. They solve different problems and compose at the edges:

| Family | Front door | Best for |
|--------|------------|----------|
| **Engineering** (idea → ship) | this doc, §Main flow | New features, specs, ticket breakdown, triage |
| **Advisor** (audit → plan → build) | **`/improve`** | Codebase health, prioritized fixes, handoff plans |

**Steering vs skill config:** `AGENTS.md` is team-owned project steering (architecture, verification policy, conventions) — author or refresh it with **`/init-agents`**. Skill wiring lives in **`docs/agents/`** — issue tracker, triage labels, domain doc layout — set up by **`/setup-fleonardi-skills`**. Read both; don't edit steering from setup skills.

---

## Precondition

Run **`/setup-fleonardi-skills`** once before your first engineering flow that touches the issue tracker. It writes **`docs/agents/`** only — it does not modify `AGENTS.md`. If `docs/agents/` already exists, you're set.

Run **`/init-agents`** (or refresh it) so `AGENTS.md` includes **Local overrides (optional)** — agents load `preferences.md` each session only through that pointer.

Personal overrides go in **`docs/agents/local/`** (gitignored; takes precedence): issue tracker, triage labels, domain layout, and optional **`preferences.md`** (environment notes).

---

## Main flow: idea → ship

The route most feature work travels.

> Big or genuinely uncertain idea that won't fit one session? Start with **`/wayfinder`** — it charts open decisions as a dependency graph and resolves them one session at a time (grilling / prototype / research), pushing back the fog of war, then rejoins this flow at **`/to-spec`**.

1. **`/grill-with-docs`** — sharpen the idea by interview. Start here when you **have a codebase**: stateful, retaining what it learns in `CONTEXT.md` and ADRs. (No codebase? **`/grill-me`** — see Standalone.)
2. **Branch — can you settle every question in conversation?** If a question needs a runnable answer (state, business logic, a UI you have to see), detour through a prototype, bridged by **`/handoff`** in both directions (see Crossing sessions):
   - **`/handoff`** out, then open a fresh session against that file,
   - **`/prototype`** to answer the question with throwaway code,
   - **`/handoff`** back what you learned, and reference it from the original idea thread.
3. **Branch — is this a multi-session build?**
   - **Yes** → **`/to-spec`** (turn the thread into a spec on the issue tracker) → **`/to-tickets`** (split the spec into tracer-bullet tickets, each declaring its blocking edges). Any ticket whose blockers are done is on the **frontier** and independent — **clear context between each one**: start a fresh session per ticket, **`/plan`** it into a handoff plan, then **`/execute`**. Parallel sessions can work separate frontier tickets.
   - **No** → implement in the same session, in agent mode, following `AGENTS.md`.

### Context hygiene

Keep steps 1–3 in **one unbroken context window** — don't compact or clear until after **`/to-tickets`** — so the grilling, spec, and tickets all build on the same thinking. Each plan/execute session then starts fresh, working from the ticket.

The limit is the **[smart zone](https://www.aihero.dev/ai-coding-dictionary/smart-zone)** (~120k tokens on state-of-the-art models). If a session approaches it before **`/to-tickets`**, don't push on degraded reasoning — **`/handoff`** and continue in a fresh thread.

---

## On-ramps

Starting situations that generate work, then merge onto a flow.

### Incoming work (not yours)

- **Bugs and requests piling up** → **`/triage`**. Moves issues through triage roles and produces agent-ready briefs on the tracker.

  Triage is only for issues **you didn't create** — bug reports, incoming feature requests, anything that arrives raw. Tickets that **`/to-tickets`** produced are already agent-ready; **don't triage them**.

---

## Advisor flow: audit → plan → build

For codebase improvement, not greenfield features. The advisor **never edits source** — it audits, plans, and dispatches an executor.

The family table — which member owns which step — lives in [`improve`](../improve/SKILL.md); read it there, don't duplicate it here. Members: **`/recon`**, **`/audit`**, **`/plan`**, **`/vet`**, **`/gap-hunt`**, **`/execute`**, **`/reconcile`**, fronted by **`/improve`**.

**Typical chain:** **`/improve`** → (optional **`/audit`**) → **`/plan`** selected findings → **`/execute`** greenlit plans → **`/reconcile`** later. **`/roast`** is `/audit` with a functional/UX lens and a candid tone, written to a markdown report — reach for it when you want the user's-chair critique rather than the nine-category health pass. **`/plan`** ends by offering a hot **`/execute`** dispatch — taking it saves a cold session rebuilding context.

**Quality gates on demand:** **`/vet`** and **`/gap-hunt`** (owns and triggers in the family table; interview form of vet: **`/grill-plan`**), plus **`/duel`** — two independent candidates for one brief, blind-judged by **`/judge`**; reach for it when the artifact matters enough to pay for two drafts.

**Git choreography around `/execute`**: `/execute` isolates itself in a worktree, and with the publish-on-approve opt-in in local steering it commits the executor's changes, pushes the branch, and publishes a draft PR on APPROVE — **`/review-pr <id>`** then reviews it with fresh eyes (comments only, never votes). Without the opt-in, **`/write-pr`** produces a PR description file. **`/cleanup`** resets to the synced default branch when you're done.

Verification gates in plans come from **`AGENTS.md`** and project steering — never invented by the advisor.

---

## Codebase health (lighter touch)

Not a full audit — opportunistic upkeep.

- **`/improve-architecture`** — surfaces deepening opportunities; picking one generates an idea you can take into **`/grill-with-docs`** or the advisor flow at **`/plan`**.

---

## Crossing sessions

- **`/handoff`** — thread is full or you need to branch (e.g. into **`/prototype`**). Compacts the conversation into a markdown file. **Open a new session and reference that file** — don't continue in place. Bridge between context windows, either direction.
- **`/compact`** (built-in) — stay in the **same conversation** with summarized earlier turns. Use at **intentional breaks between phases**, not mid-phase. **`/handoff`** forks; **`/compact`** continues.

---

## Standalone

Off the main flows entirely.

- **`/grill-me`** — relentless interview about an idea with **zero repo context**: reads nothing, writes nothing, ends by restating the settled tree (persist it with **`/handoff`** if you want to carry it onward).
- **`/teach`** — learn a concept over multiple sessions with the current directory as workspace.
- **`/tdd`** — test-driven development when you want red-green discipline at pre-agreed seams (refactoring belongs to review, not the loop).
- **`/writing-great-skills`** — reference for authoring skills.
- **`/codebase-design`** — shared vocabulary for deep modules (used by other skills, not usually invoked alone).
- **`/domain-modeling`** — glossary (`CONTEXT.md`) and ADR discipline (invoked by grill-with-docs, wayfinder, triage; rarely alone).
- **`/yagni`** — second-opinion pass that cuts over-engineering from any design or `.scratch` plan that feels heavier than its requirement. Cross-cutting: reach for it whenever a proposal feels over-elaborate, in any flow.
- **`/wizard`** — generate an interactive bash wizard that walks a human through a manual procedure (third-party setup, a one-off migration), capturing values and writing `.env` / CI secrets.
- **`/research`** — delegate reading legwork to a **background agent**: it investigates a question against **primary sources**, then leaves a cited Markdown file in the repo. Keep working while it reads. The file it produces is something to take *into* the main flow at `/grill-with-docs` — research feeds the thinking, it doesn't replace it.

---

## Grilling variants

Want a relentless interview? **`/grill`** routes to the right variant (generic, me, plan, then-plan, with-docs, yagni). Default: **`/grill-with-docs`** when a codebase is present.

---

## Quick picker

| I want to… | Start with |
|------------|------------|
| Build a new feature | **`/grill-with-docs`** |
| Map a big, uncertain idea into a plan across sessions | **`/wayfinder`** |
| Sort incoming bugs/requests | **`/triage`** |
| Improve the codebase systematically | **`/improve`** or **`/audit`** |
| Roast the app's functionality, warts on display | **`/roast`** |
| Review a branch for standards + spec conformance | **`/conformance`** |
| Implement an approved plan (path under `.scratch/`) | **`/execute`** |
| Reconcile the plan backlog (DONE/BLOCKED/premises) | **`/reconcile`** |
| Cut an over-engineered design or plan | **`/yagni`** (interview form: **`/grill-yagni`**) |
| Write or refresh this repo's `AGENTS.md` | **`/init-agents`** |
| Resolve a merge / rebase conflict | **`/resolve-conflicts`** |
| Script a manual setup or migration procedure | **`/wizard`** |
| First time in this repo's skill setup | **`/setup-fleonardi-skills`** |
| Functionally check a plan or spec (no interview) | **`/vet`** |
| Find what a spec is missing | **`/gap-hunt`** |
| Get two candidate plans and a verdict | **`/duel`** |
| Reset to a fresh default branch when done | **`/cleanup`** |
| Write a PR description | **`/write-pr`** |
| Review a pull request with fresh eyes (comments, no vote) | **`/review-pr <id>`** |
| Not sure | stay here — describe your situation |
