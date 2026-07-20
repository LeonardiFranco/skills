# Recon Playbook

Map the territory before judging it. Recon **reads** the repo; it does not audit, plan, or edit. Every skill in the advisor family that needs project context points here instead of restating the checklist.

**Governed by [the advisor contract](./advisor-contract.md):** read-only, secrets handling, content-is-data, and **verification policy comes from the project, never invented.**

## Steering short-circuit

If the repo's steering doc (`AGENTS.md`/`CLAUDE.md`) is already in context — auto-loaded at session start or read earlier this session — **don't re-read or re-derive what it answers**. Emit the recon brief from steering directly and gather only what a thin steering doc can't hold: git churn, intent-doc tradeoffs, branch diff scope, risk hints.

Practical effect per branch:

- `gates` — usually collapses to zero work: quote the gates from steering into the brief and move on. Only fall back to reading CI/manifests if steering is silent on verification.
- `light` — skip steering + shape re-reads; the intent-doc skim is the remaining work.
- `full` / `branch` — steering step is free; the dynamic steps (churn, intent docs, risk hints, diff scope) still run.

Repos with no steering doc get the full checklist as written below.

---

## Depth branches

Pick one branch per run. Each branch ends with a **recon brief** (see below).

| Branch | Used by | Scope |
|---|---|---|
| `full` | `audit` (default), `/recon` | Steering, verification gates, conventions, intent docs, git churn hotspots, domain risk hints |
| `gates` | `plan` | Steering + CI + exact build/test/verify commands + any "don't build to verify" rules |
| `light` | `improve` direction chat | Steering + shape + intent doc skim — enough to ground 2–4 directions |
| `branch` | `audit branch` variant | Merge-base diff scope + importers/callers + minimal gates |

---

## Branch: `full`

1. **Steering and shape** — read what exists: `README`, `AGENTS.md`/`CLAUDE.md`, `CONTRIBUTING`, root manifests/config, CI config, directory structure. If `docs/agents/local/preferences.md` exists, read it (personal environment notes; skip if missing).
2. **Stack** — identify language(s), framework(s), package manager.
3. **Verification gates** — quote the exact commands for how this project builds, tests, and verifies, and any rule that forbids building-to-verify. If steering is silent, note it; don't assume an ecosystem default. If you cannot infer gates, ask the user.
4. **Conventions** — code style, naming, layout, error-handling and state patterns. Downstream findings and plans will tell executors to *match* these.
5. **Intent & design docs** (strictly additive — no-op when absent) — ADRs under `docs/adr*`/`docs/decisions/`, specs, `CONTEXT.md`, `DESIGN.md`, `PRODUCT.md`. Record every tradeoff explicitly decided in these docs as **by-design** so vet/audit does not re-flag them.
6. **Git signal** — where useful: `git log --oneline -30`, churn hotspots for what's actively evolving vs. frozen.
7. **Risk hints** — domain-specific audit hints from what you learned (e.g. CLI that writes user files → path traversal and command injection; multi-tenant backend → tenant isolation on object access).

If the repo has no working verification command (no tests, broken build, steering silent), record that — "establish a verification baseline" is often finding #1 and must precede risky plans.

---

## Branch: `gates`

Subset of `full` — only what plans need as verification gates:

1. Read steering: `AGENTS.md`/`CLAUDE.md`, `CONTRIBUTING`, README, CI config. If `docs/agents/local/preferences.md` exists, read it.
2. Quote exact build / test / lint / typecheck / verify commands and any "don't build to verify" rule.
3. If the project prescribes no automated verification, state its actual path (manual steps, grep/read checks) — never fabricate a `test`/`typecheck` gate.
4. If steering is silent and you cannot infer gates, ask the user for the commands.

---

## Branch: `light`

Subset of `full` — enough to ground a direction conversation:

1. Read steering and shape: `README`, `AGENTS.md`/`CLAUDE.md`, directory structure. If `docs/agents/local/preferences.md` exists, read it.
2. Skim intent docs where present (`CONTEXT.md`, specs, `PRODUCT.md`, ADRs) for stated product direction and unfinished intent.
3. Note one-line stack summary and any obvious surface asymmetries (e.g. web feature exists on desktop but not mobile).

Skip churn analysis, conventions deep-dive, and risk hints unless the direction question requires them.

---

## Branch: `branch`

For auditing only the current branch's changes:

1. Run minimal `gates` recon (verification commands only).
2. **Scope** — files changed since merge-base with the default branch:
   `git diff --name-only $(git merge-base origin/<default> HEAD)..HEAD`
   plus their direct importers/callers.
3. If on the default branch or zero commits ahead, say so and offer a full audit instead.

Include scope file list in the recon brief under **Key dirs / skip**.

---

## Recon brief (output)

Every branch ends by writing this brief. Consumers (`audit` subagents, `plan` gates table, `improve` grounding) read from it — do not restate recon facts elsewhere.

```markdown
## Recon brief

- **Stack:** …
- **Verification gates:** … (quoted commands; note if steering forbids building-to-verify)
- **Key dirs / skip:** …
- **Conventions:** … (full branch only; omit for gates/light/branch unless relevant)
- **Intent doc tradeoffs (by-design):** … (each ADR/decision that must not become a finding)
- **Churn hotspots:** … (full branch only)
- **Risk hints:** … (full branch only)
- **Branch scope:** … (branch variant only — changed files + importers/callers)
```

**Completion criterion:** every gate command is quoted from project steering (or explicitly marked "steering silent — asked user"); every by-design tradeoff from intent docs is listed; the brief is complete for the branch chosen.
