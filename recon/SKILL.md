---
name: recon
description: Map the repo and produce a recon brief — stack, verification gates, conventions, intent-doc tradeoffs. User-invoked onboarding before audit or plan.
disable-model-invocation: true
---

# Recon

Map the territory. Recon **reads** the repo and outputs a brief — no findings, no plans, no edits.

**First, read [the advisor contract](../improve/references/advisor-contract.md).** Then run [the recon playbook](../improve/references/recon-playbook.md) at the depth below and present the **recon brief** only.

## Depth (argument)

| Argument | Branch | Use when |
|---|---|---|
| *(none)* | `full` | Default — full onboarding |
| `gates` | `gates` | Verification commands only |
| `light` | `light` | Direction / shape skim |
| `branch` | `branch` | Scope for branch-only audit |

**Completion criterion:** the recon brief is complete for the chosen branch and every verification gate is quoted from project steering (or marked "steering silent — asked user").
