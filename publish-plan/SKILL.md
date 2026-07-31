---
name: publish-plan
description: Publish one or more OpenSpec changes from openspec/changes/ to the project issue tracker as work items — the change folder stays the source of truth, the tracker item is distribution. Use when asked to publish a plan or change to the tracker.
license: MIT
---

# Publish Plan

Mirror an OpenSpec change to the project issue tracker as a work item. The **change folder under `openspec/changes/<slug>/` stays the source of truth**; the tracker item is distribution, so people and AFK agents can find and act on it. Publishing copies the change's decisions out — it never rewrites them.

Before acting, read the config this skill needs from `docs/agents/` — prefer `docs/agents/local/<name>.md` over `docs/agents/<name>.md` when both exist. If missing or incomplete, ask the user to run `/setup-fleonardi-skills`. Here that means the issue-tracker config — its "publish a plan to the issue tracker" workflow and the work-item-type mapping.

## Input

One or more change folder paths or slugs under `openspec/changes/` — e.g. `publish-plan openspec/changes/make-settings-role-switch-reliable`, or the changes just written by `/plan`. No path given → ask which change(s).

## Steps

### 1. Preflight

Read the issue-tracker config's "publish a plan" workflow. If publishing is **unsupported** for the configured tracker (e.g. local markdown, which already holds the change as its artifact), stop and say publish was skipped and why. Otherwise confirm the tracker CLI/auth is reachable; if not, skip and say so.

**Completion criterion:** the tracker supports plan publish and is reachable, or you have reported the skip and stopped.

### 2. Sensitive-content gate

Before publishing a change that describes vulnerabilities, credential locations, or other sensitive findings, warn the user and get explicit confirmation when the tracker is publicly visible (per the issue-tracker config).

**Completion criterion:** no sensitive change reaches a public tracker without explicit confirmation.

### 3. Publish each change

Per change, synthesize a work-item body from `proposal.md` (why/what/out of scope) plus the done criteria / manual verify from `tasks.md`. Follow the config's "publish a plan" steps: create the work item of the configured type with the proposal title and that body, link it under its parent when there is one, and apply tags only where they already exist.

**Completion criterion:** each change has a tracker work item, with its returned id/URL in hand.

### 4. Record the link

Write each returned work-item id/URL into the change's `proposal.md` Status block (`Source:` or a `Tracker:` line) and its row in `openspec/changes/README.md`. The change folder stays the source of truth; the id is the pointer between the two.

**Completion criterion:** every published change's proposal Status and README row carry the tracker id/URL.
