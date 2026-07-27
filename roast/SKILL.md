---
name: roast
description: Roast a codebase functionality-wise — a candid, evidence-backed audit of user-facing gaps, half-built features, workflow absurdities, and cross-surface inconsistencies, written to a markdown file. Use when asked to "roast" an app or give a brutally honest functional critique.
---

# Roast

Invoke the [`audit`](../audit/SKILL.md) skill with this focus argument (compose the user's `quick`/`deep` keywords and any extra scoping into it verbatim):

> Roast the application functionality-wise — a candid, critical survey of user-facing functionality gaps, inconsistencies, half-built features, and UX/workflow absurdities. Output as a markdown findings file.

Everything else — recon, parallel fan-out, the finding format, the vet-before-present pass — is audit's; do not reimplement any of it.

## Lens (what the fan-out hunts)

Shape the parallel subagents around the product's actual user surfaces discovered in recon (e.g. one per client surface, one half-built/no-op feature hunt, one cross-surface parity pass). The roast categories:

- **Workflow absurdities** — success states that look like failures, modal ceremonies, actions that silently fail or silently commit, forced restarts/reloads, state lost on navigation.
- **Half-built and decorative features** — flags and config knobs read by nothing, stubs a user can reach, placeholder data shown as real, "temporarily" hardcoded tenant workflows, zombie branches behind long-rolled-out flags.
- **Cross-surface asymmetries** — capabilities one client has that another lacks while the backend already supports both; export without import; CRUD-minus-one.
- **Inconsistencies users notice** — the destructive action with no confirm next to the trivial one with a modal, mixed languages, error messages that lie.

## Output

Tone: candid and punchy, but **no roast without receipts** — every jab carries `file:line` evidence, and the vet pass still applies before anything is published. Impact/effort/risk/confidence per finding as audit defines.

Write the report to `.scratch/_roast/ROAST.md` (or a path the user names). Suggested shape: a one-paragraph roast up top, a leverage-ordered "hall of shame" table, per-surface sections, a parity table when there are multiple clients, then — separated, per audit's rules — direction options, security side-notes, rejections, and an explicit not-audited list. Do not commit the file; it's a working artifact.
