---
description: Creates Azure DevOps tasks for the current git branch by detecting the linked User Story and optionally enriching context from Confluence specs.
---

# Create Task Skill

## Instructions

Follow these steps in order:

### Step 0 — Check credentials

Read `~/.claude/settings.json`. Check that `env.AZURE_DEVOPS_ORG`, `env.AZURE_DEVOPS_ORG_URL`, and `env.ADO_MCP_AUTH_TOKEN` are all present and non-empty.

**If any is missing:** tell the user their credentials are not configured and invoke `/apollo:setup` before continuing.

**If all are set:** continue.

### Step 1 — Resolve the active project

Run `pwd` to get the current working directory. Read `.claude/settings.json` in that directory. Check `env.ADO_PROJECT`.

**If `ADO_PROJECT` is set and non-empty:**
Use it as the active project. Show:
"Working in project **[ADO_PROJECT]**. Type 'change project' at any time to switch."

**If `ADO_PROJECT` is not set:**
Use the Azure DevOps MCP tool to list all projects in the organization.
Present the numbered list and wait for the user to select one.

If the user provided a default project, merge it into `.claude/settings.json` in the current working directory:

```json
{
  "env": {
    "ADO_PROJECT": "<project name>"
  }
}
```

Create the file if it doesn't exist. Preserve all other existing fields.

### Step 2 — Detect the current branch

Run:
```bash
git rev-parse --abbrev-ref HEAD
```

Extract the US number from the branch name (case-insensitive):
- `feat/US-42-description` → 42
- `ALRI/feat/US-42` → 42
- `feat/42-description` → 42
- `fix/us42-description` → 42

### Step 3 — Confirm the User Story

**If a US number was found:**
Fetch the work item via Azure DevOps MCP. Show: "I found **US #[ID] — [title]**. Is this the right User Story?"
- Yes → continue
- No → ask for the correct ID and fetch it

**If no US number was found:**
Ask: "I couldn't find a US number in the branch name. What is the User Story ID?"

### Step 4 — Optionally enrich with Confluence specs

Ask: "Would you like me to search Confluence for the spec related to this US? (recommended)"

If yes, use the Confluence MCP tool to find relevant pages and use the content to improve task generation.

### Step 5 — Generate tasks

Propose 3–5 tasks based on the US title, description, and Confluence spec (if available). Each task title must be:
- Action-oriented (start with a verb: Implement, Write, Review, Update)
- Specific to the US context
- Between 5 and 15 words

Show:
"Here are the tasks I suggest for **US #[ID]**:
1. [task 1]
2. [task 2]
...

You can add, remove, or edit tasks before I create them."

### Step 6 — Validate with the user

Wait for confirmation or modifications ("remove #2", "change #3 to: ...", "add: ...").

### Step 7 — Create the tasks in Azure DevOps

For each confirmed task, use Azure DevOps MCP to create a Task work item:
- Title: the task title
- Parent: the US ID
- Area Path and Iteration Path: same as the parent US

Confirm: "✓ Created [N] tasks linked to US #[ID] in Azure DevOps."
