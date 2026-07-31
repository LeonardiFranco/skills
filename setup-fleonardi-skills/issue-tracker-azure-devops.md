# Issue tracker: Azure DevOps Boards

Issues for this repo are tracked as **work items on Azure DevOps Boards**, not GitHub/GitLab issues
and not local markdown. The engineering skills (`triage`, `publish-plan`, etc.) should
read and write work items through the `az boards` CLI.

- **Organization**: `https://dev.azure.com/<org>`
- **Project**: `<project>`
- **CLI**: the [Azure CLI](https://learn.microsoft.com/cli/azure/) with the `azure-devops` extension.

> Note: durable engineering plans live under `openspec/changes/`; `.scratch/` is disposable drafts only.
> Treat Azure DevOps Boards as the distribution/status surface; the OpenSpec change folder stays the
> engineering source of truth for decisions and done criteria.

## One-time setup

```bash
az extension add --name azure-devops
az devops configure --defaults organization=https://dev.azure.com/<org> project=<project>
az login   # or set AZURE_DEVOPS_EXT_PAT for non-interactive auth
```

## Work-item types

Use the type that matches the request:

- **Bug** — defect in shipped behavior
- **User Story** (or **Issue**, depending on the project's process template) — feature work / enhancement
- **Task** — a sub-unit of implementation work

If a command rejects the type, the project may use a different process template — list valid types with
`az boards work-item type list` (if available) or check an existing work item, and use the matching name.

## Pull requests as a triage surface

**PRs as a request surface: no.** _(Set to `yes` if this repo treats external PRs as feature requests; `/triage` reads this flag.)_

When set to `yes`, PRs run through the same states as work items, using `az repos pr` equivalents:

- **Read a PR**: `az repos pr show --id <id>`; **list**: `az repos pr list --status active`.
- **Triage state**: PRs don't carry `System.Tags` — record it on a linked work item or in a PR comment.

## When a skill says "publish to the issue tracker" / "create an issue"

```bash
az boards work-item create \
  --title "<concise title>" \
  --type "User Story" \
  --description "<HTML or plain-text body>" \
  --fields "System.Tags=needs-triage"
```

The command returns JSON including the new work-item `id`. Reference issues by that numeric id.

For a spec or a multi-ticket breakdown, create one parent work item for the spec/epic and child work items
for each slice, linking them:

```bash
az boards work-item relation add --id <childId> --relation-type parent --target-id <parentId>
```

## When a skill says "publish a plan to the issue tracker"

Mirror an OpenSpec change as a Boards work item — the change folder stays the source of truth; the work item is distribution.

1. Preflight: `az` authenticated with the `azure-devops` extension configured. If not, skip publish and say why.
2. **Sensitive plans**: before publishing changes describing vulnerabilities, credential locations, or other sensitive findings, warn and get explicit confirmation when the project is publicly visible.
3. Per change: create a work item with the proposal title and a body synthesized from `proposal.md` + `tasks.md` done criteria:

```bash
az boards work-item create \
  --title "<proposal title>" \
  --type "Task" \
  --description "<synthesized body>"
```

4. Tag with `plan` and the proposal Status `Category` value via `System.Tags` only if those tags already exist (read current tags first; skip rather than fail).
5. Record the returned work-item id in `proposal.md` Status and `openspec/changes/README.md`.

## When a skill says "fetch the relevant ticket"

```bash
az boards work-item show --id <id>
```

The user will normally pass the work-item id (or a Boards URL containing it) directly.

## When a skill needs to query the queue (e.g. triage)

Use WIQL:

```bash
az boards query --wiql "SELECT [System.Id], [System.Title], [System.Tags], [System.State] \
FROM workitems WHERE [System.TeamProject] = '<project>' AND [System.Tags] CONTAINS 'needs-triage'"
```

## When a skill changes triage state

Triage roles are represented as **work-item tags** (see `triage-labels.md`). Update them with:

```bash
az boards work-item update --id <id> --fields "System.Tags=ready-for-agent"
```

`System.Tags` is a semicolon-separated set — read the current tags first and rewrite the whole field so
you preserve unrelated tags and only swap the triage-role tag.

## Comments

Append conversation/history as work-item comments (discussion), not by mutating the description:

```bash
az boards work-item update --id <id> --discussion "<comment>"
```
