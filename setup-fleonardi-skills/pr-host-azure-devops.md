# Seed: PR host — Azure DevOps

Append to `issue-tracker.md`, replacing `<org>`, `<project>`, `<repo>`. Fill the description
conventions from the team's actual practice (ask; don't invent).

---

## PR host: Azure DevOps

Org `<org>`, project `<project>`, repo `<repo>`. Auth: `az login`, then
`az devops configure --defaults organization=https://dev.azure.com/<org> project=<project>`
(requires the `azure-devops` CLI extension). Consumed by `/execute`'s publish-on-approve tail
and `/review-pr`.

| Operation | Command |
|---|---|
| List active PRs | `az repos pr list --status active -o table` |
| PR metadata | `az repos pr show --id <id>` |
| Create draft PR | `az repos pr create --draft --source-branch <branch> --title "<title>" --description "<md>"` |
| List comment threads | `az devops invoke --area git --resource pullRequestThreads --route-parameters project=<project> repositoryId=<repo> pullRequestId=<id> --api-version 7.1` |
| Post comment thread | same resource with `--http-method POST --in-file <body.json>`; body `{"comments":[{"parentCommentId":0,"content":"..","commentType":1}],"status":"active"}`; file/line anchor via `threadContext` (`filePath`, `rightFileStart`/`rightFileEnd`) |
| Edit a comment | `--resource pullRequestThreadComments` + `--route-parameters ... threadId=<t> commentId=<c>` `--http-method PATCH --in-file <body.json>`; body `{"content":".."}` |
| Exact merge state | `git fetch origin refs/pull/<id>/merge` (absent when conflicted → fetch the source branch instead) |

**Text encoding (Windows `az`):** everything sent through `az` — inline `--description`,
`--in-file` comment bodies — silently drops *every non-ASCII character* server-side (em-dashes,
arrows, even accented letters), which can fuse adjacent words. Keep az-authored text ASCII-only
(`--`, `->`, "union of"). After a POST, read back the stored content to confirm it round-tripped;
PATCH-edit to repair (row above). Prefer redirecting `az` output to a file over piping it
(`az … -o json > out.json`, then parse the file) — pipes from `az` have been flaky under Git Bash.

Description conventions: ADO caps PR descriptions at **4000 chars** — record the team's overflow
convention here (e.g. description ends at a named section; trailing sections such as test cases
posted as the PR's first comment).

**Votes: never.** `az repos pr set-vote` exists but authenticates as the human user — an agent
vote would masquerade as their judgment and may satisfy branch-policy reviewer requirements.
Votes, draft→active flips, merges: human-only.
