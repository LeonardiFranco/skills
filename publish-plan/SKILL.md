---
name: publish-plan
description: Publish one or more handoff plans from `.scratch/` to the project issue tracker as work items — the plan file stays the source of truth, the tracker item is distribution. Use when asked to publish a plan (or plans) to the tracker.
license: MIT
---

# Publish Plan

Mirror a handoff plan to the project issue tracker as a work item. The **plan file under `.scratch/` stays the source of truth**; the tracker item is distribution, so people and AFK agents can find and act on it. Publishing copies the plan's decisions out — it never rewrites them.

Before acting, read the config this skill needs from `docs/agents/` — prefer `docs/agents/local/<name>.md` over `docs/agents/<name>.md` when both exist. If missing or incomplete, ask the user to run `/setup-fleonardi-skills`. Here that means the issue-tracker config — its "publish a plan to the issue tracker" workflow and the work-item-type mapping.

## Input

One or more plan file paths under `.scratch/` — e.g. `publish-plan .scratch/_backlog/plans/099-...md`, or the plans just written by `/plan`. No path given → ask which plan(s).

## Steps

### 1. Preflight

Read the issue-tracker config's "publish a plan" workflow. If publishing is **unsupported** for the configured tracker (e.g. local markdown, which already holds the plan as its artifact), stop and say publish was skipped and why. Otherwise confirm the tracker CLI/auth is reachable; if not, skip and say so.

**Completion criterion:** the tracker supports plan publish and is reachable, or you have reported the skip and stopped.

### 2. Sensitive-content gate

Before publishing a plan that describes vulnerabilities, credential locations, or other sensitive findings, warn the user and get explicit confirmation when the tracker is publicly visible (per the issue-tracker config).

**Completion criterion:** no sensitive plan reaches a public tracker without explicit confirmation.

### 3. Publish each plan

Per plan, follow the config's "publish a plan" steps: create the work item of the configured type with the plan title and plan body, link it under its parent (a spec's Feature when there is one), and apply tags only where they already exist.

**Completion criterion:** each plan has a tracker work item, with its returned id/URL in hand.

### 4. Record the link

Write each returned work-item id/URL into the plan's **Status block** and its plan tree's **`README.md`** row. The plan file stays the source of truth; the id is the pointer between the two.

**Completion criterion:** every published plan's Status block and README row carry the tracker id/URL.
