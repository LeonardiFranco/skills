# Seed: PR host — GitHub

Append to `issue-tracker.md`, replacing `<owner>/<repo>`. Commands are the standard `gh`
surface but unverified by this setup — verify each on first use and correct in place.

---

## PR host: GitHub

Repo `<owner>/<repo>`. Auth: `gh auth login`. Consumed by `/execute`'s publish-on-approve tail
and `/review-pr`.

| Operation | Command |
|---|---|
| List open PRs | `gh pr list --state open` (drafts included) |
| PR metadata | `gh pr view <id> --json title,body,isDraft,baseRefName,headRefName,author` |
| Create draft PR | `gh pr create --draft --title "<title>" --body-file <file>` |
| List comments | `gh pr view <id> --comments`; review threads via `gh api repos/<owner>/<repo>/pulls/<id>/comments` |
| Post summary comment | `gh pr comment <id> --body-file <file>` |
| Post file/line-anchored comment | `gh api repos/<owner>/<repo>/pulls/<id>/comments -f body=.. -f path=.. -F line=.. -f side=RIGHT -f commit_id=..` |
| Exact merge state | `git fetch origin refs/pull/<id>/merge` (absent when conflicted → fetch the head branch instead) |

Description conventions: none by default — record any team practice here (length limits,
sections posted as comments).

**Approvals: never.** `gh pr review --approve` authenticates as the human user — an agent
approval would masquerade as their judgment and may satisfy branch-protection requirements.
Approvals, ready-for-review flips, merges: human-only.
