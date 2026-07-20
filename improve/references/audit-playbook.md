# Audit Playbook

What to look for, per category, and the shape every finding comes back in. Language-agnostic on purpose: the *concepts* below recur in every ecosystem — translate each into the idioms of the stack you're auditing (learned during recon), don't expect a particular language's tells. Adapt depth to repo size: a 2K-line CLI gets a lighter pass than a 500K-line monorepo.

A finding is only a finding **with evidence**. "Probably has N+1 queries somewhere" is not a finding; "`orders/api:142` issues one query per item inside the loop" is.

---

## 1. Correctness / Bugs

The highest-trust category — real bugs found by reading, not speculation.

- **Error handling**: swallowed errors, empty catch/rescue blocks, errors logged-and-continued on critical paths, missing error states in UI.
- **Concurrency & async hazards**: unsynchronized access to shared state, check-then-act races, fire-and-forget async work whose failure is lost, missing cancellation/cleanup (listeners, timers, subscriptions never torn down), missing transactions around multi-write operations, non-idempotent handlers for retried operations (webhooks, queues).
- **Nullability flows**: values dereferenced where they can be absent, suppression of "may be null" warnings on values that genuinely can be, unchecked indexing past collection bounds.
- **Boundary conditions**: off-by-one, empty-collection handling, timezone/locale assumptions, numeric overflow in counters/IDs.
- **State machines**: impossible states made representable, status enumerations with unhandled branches (a default/else that silently no-ops).
- **Type-system escape hatches**: clusters where the type checker was overruled — unchecked casts, untyped escape values, suppression directives. Each is a place a guarantee was waived.
- **Resource leaks**: handles, connections, file descriptors, subscriptions opened without a guaranteed release path.

## 2. Security

Review only what code evidence directly supports. Frame everything as defensive maintenance: name the pattern, explain the production impact, describe the remediation at the level of code/config/test changes. **Never include runnable exploit strings or step-by-step misuse.**

- **Credential hygiene**: hardcoded keys/tokens/passwords, secrets in committed config or `.env`, credentials written to logs or persisted in history/event stores. Name the credential *type* and `file:line` only; the fix always includes rotation, not just removal.
- **Untrusted data crossing into an interpreter or privileged API**: queries or shell commands assembled from request data (injection), markup sinks fed user-controlled content (XSS), dynamic-execution APIs fed runtime input, filesystem paths derived from request data (traversal). Describe the safe API or validation boundary.
- **Access control**: routes/actions lacking a server-side identity check, authorization enforced only client-side, object access by ID without an ownership/tenant check, missing request-authenticity checks on state-changing routes.
- **Input contracts**: boundaries that trust request bodies without schema validation, uploads without type/size/storage limits, bulk assignment from request data straight into persistence models.
- **Dependency posture**: if the ecosystem has an advisory/audit tool, run it read-only and report only critical/high advisories reaching runtime or build/distribution paths. Skip low-signal noise.
- **Production configuration**: over-broad CORS where credentials are allowed, missing response-hardening headers on sensitive browser surfaces, cookies missing appropriate flags, debug/verbose modes enabled in production.
- **Data minimization**: PII or sensitive operational data in logs, internal errors/stack traces returned to clients.

**By-design is not a finding.** Standard platform conventions are intentional (honoring proxy env vars, reading well-known config files, a local dev tool shelling out to configured tooling). A tradeoff explicitly recorded in an ADR or decision doc found during recon is settled, not a finding. Flag these only when the *implementation* adds risk beyond the convention or the documented decision.

## 3. Performance

Algorithmic and architectural wins, not micro-optimizations.

- **N+1 patterns**: a query/fetch per item inside a loop or per rendered row; missing batching.
- **Wrong complexity**: nested scans over the same collection, repeated linear searches inside hot loops where a keyed lookup belongs.
- **Caching gaps**: identical expensive computations/fetches repeated per request or render; no caching on stable data.
- **Payload size**: over-fetching (whole objects where IDs suffice), missing pagination on unbounded lists, oversized responses shipped to clients.
- **Backend**: synchronous work that belongs on a queue, indexes implied by query patterns but absent (flag for verification — don't claim without schema evidence), per-request connection setup where pooling exists.
- **Client / UI**: heavyweight dependencies for trivial use, missing lazy-loading on rarely-hit paths, unoptimized assets, render waterfalls. For UI frameworks, defer to the repo's own conventions and any installed best-practices guidance.
- **Build / CI**: slow pipelines from missing caching, redundant steps, suites that could parallelize.

## 4. Test Coverage

The goal is not a percentage — it's *which untested code is dangerous*.

- Map the critical paths (money, auth, data mutation, the feature the repo exists for) and check which have zero or trivial coverage.
- High-churn modules (from git log) with no tests are the top refactor risk — flag as "characterization tests first" candidates.
- Existing test quality: tests that assert nothing meaningful, mocks so heavy they test the mocks, snapshots nobody reads, flaky patterns (real clocks, real network, order dependence).
- Missing layers: unit-only suites with no coverage at integration boundaries, or slow end-to-end tests doing a unit test's job.
- **Verification baseline**: is there a known way to tell the codebase works? Per the advisor contract, that way is whatever the *project* prescribes — don't equate "no test suite" with "untestable." If the project prescribes nothing and nothing exists, "establish a verification baseline (with the maintainer)" is often finding #1 and a prerequisite for any risky change.

## 5. Tech Debt & Architecture

- **Duplication**: the same logic re-implemented in 3+ places, or divergent copies that have drifted.
- **Layering violations**: higher layers reaching into lower-layer internals, circular dependencies, a "utils" module that became a high-fan-in junk drawer.
- **Dead code**: unreferenced modules, fully rolled-out flags still branching, commented-out blocks with no explanation, manifest dependencies no longer used.
- **God objects/modules**: files an order of magnitude larger than the repo median that everything depends on; functions with deep nesting or unwieldy parameter lists.
- **Inconsistent patterns**: three ways of doing data access / error handling / styling in one repo — name the winner (the one the team converged on most recently) and plan the consolidation.
- **Abstraction mismatches**: premature abstractions with a single implementation, or a missing abstraction where one change always forces edits across N files in lockstep.

## 6. Dependencies & Migrations

- Major-version lag on the core framework/runtime where staying behind has real cost (EOL, security-fix cutoff, ecosystem incompatibility) — not every minor bump.
- Deprecated APIs in use with announced removal timelines.
- Abandoned dependencies (no release in years, archived) on critical paths.
- Duplicate dependencies solving the same problem.
- Lockfile/manifest drift, inconsistent pinning across a monorepo.
- For each migration candidate, estimate blast radius (files touched) — that drives effort and whether to recommend it at all.

## 7. DX & Tooling

- Missing or broken developer feedback: typecheck, lint, formatter, pre-commit hooks, editor config — *as the project defines them*.
- Slow feedback loops: multi-minute dev-server or test startup, no watch mode, CI without caching.
- Onboarding friction: setup steps that are wrong/incomplete, undocumented required env vars, no example env file.
- Missing agent steering (`AGENTS.md`/`CLAUDE.md`): for repos where agents will execute the plans, this is high-leverage — recommend one and outline it as a plan.
- Observability: unstructured logs on services, missing request/correlation IDs, debugging that requires code changes.

## 8. Docs

Lowest default priority — flag only where absence has a concrete cost:

- Published/public API surface without reference docs.
- Decisions nobody can reconstruct (why X over Y) in actively-contested areas.
- Stale docs that are actively *wrong* (worse than missing) — setup steps or examples that no longer work.

## 9. Direction — features & where to take this next

Forward-looking: not what's broken, but what this codebase wants to become. **Grounding rule:** every suggestion cites evidence from the repo itself. A suggestion that could apply to any project in the category ("add dark mode", "add AI") is noise. Sources of grounded signal:

- **Unfinished intent**: TODO/FIXME clusters around one theme, flags never rolled out, stubbed or half-built modules, abandoned mid-feature work in git history.
- **Stated-but-undelivered**: README/roadmap promises with no code, config options that are no-ops. A spec or product brief that names users or a direction the code hasn't caught up to is the strongest signal there is — prefer it over inferred intent, and never propose something a decision doc already rejected (note the contradiction instead).
- **Surface asymmetries**: one-directional pairs (export without import, create without bulk-create), entities with CRUD-minus-one, an internal API hand-rolled around because the real one was missing.
- **The adjacent possible**: capabilities the existing architecture makes disproportionately cheap — a plugin seam one interface away, an integration the data model already supports.
- **Friction worth productizing**: things users evidently do by hand around the project (visible in docs, examples, issues) that it could absorb.

Direction findings use the standard format with two adaptations: **Impact** is product/user value (who wants this and why now), and **Confidence** reflects how *grounded* the evidence is — not certainty it's the right call. Strategy belongs to the maintainer; your job is grounded options with honest trade-offs. Effort here is coarser — say so. Plans for selected direction findings are usually a *design/spike plan* (investigate, prototype, define the interface, list open questions), not build-everything.

---

## Finding format

Every finding, from every category and every subagent, comes back in this shape:

```markdown
### [CATEGORY-NN] Short imperative title

- **Evidence**: `path/file:123` — one sentence on what's there. (Repeat per location; 2–5 strongest, note "and ~N similar sites" if widespread.)
- **Impact**: What goes wrong / what's being paid. Concrete: "every list render issues 1+N queries", not "suboptimal".
- **Effort**: S (hours) / M (a day-ish) / L (multi-day) — for the *fix*, including its verification.
- **Risk**: What the fix could break; LOW/MED/HIGH plus one line why.
- **Confidence**: HIGH (read the code, certain) / MED (strong signal, needs verification) / LOW (smell, needs investigation). LOW-confidence findings get an "investigate" plan, not a "fix" plan.
- **Fix sketch**: 1–3 sentences. Not the plan — just enough to judge effort honestly.
```

## Prioritization rubric

Order by **leverage = impact ÷ effort, discounted by confidence and fix-risk**. Tiebreakers:

1. Anything that unblocks other findings (verification baseline, characterization tests) floats up.
2. HIGH-confidence security findings float above equivalent-leverage non-security findings.
3. Prefer findings with a clean verification story — executors succeed at those.
4. "Not worth doing" is a valid verdict; record it with one line of reasoning so it isn't re-audited next run.
