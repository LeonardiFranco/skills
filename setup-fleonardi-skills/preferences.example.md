# Seed: preferences.example.md

Copied to `docs/agents/preferences.example.md` (team template). Users copy it to
`docs/agents/local/preferences.md` (gitignored) for personal environment notes —
agents load it only via the `AGENTS.md` **Local overrides** pointer.

---

# Personal preferences

Environment notes agents should know in this repo. Keep it short; this file is
read every session.

## Environment

<!-- e.g. "Windows + PowerShell; prefer forward slashes in git paths" or
     "docker compose up db before running the suite" -->

## Publish on approve (opt-in)

<!-- Uncomment to let /execute invoke /publish-pr after an APPROVE verdict:
     commit in the worktree, push, write-pr description, draft PR per the
     PR host section of issue-tracker.md. Never on REVISE/BLOCK; flipping
     the draft to active stays human.

## Publish on approve

Publish approved plans as draft PRs.
-->
