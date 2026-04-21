---
description: Creates Azure DevOps tasks for the current git branch by detecting the linked User Story and optionally enriching context from Confluence specs.
---

# Create Task Skill

## Instructions

Follow these steps in order:

### Step 0 — Resolve the active project

Check the value of `${user_config.ado_project}`:

**If `ado_project` is set and non-empty:**
Use it as the active project. At the end of Step 0, show:
"Working in project **${user_config.ado_project}**. Type 'change project' at any time to switch."

**If `ado_project` is empty or not configured:**
Use the Azure DevOps MCP tool to list all projects in the `${user_config.ado_org}` organization.
Present the list to the user:
"Which project do you want to work in?
[numbered list of projects]"
Wait for the user to select one by number or name.
Use the selected project for all subsequent steps.
After selection, tell the user:
"To set this project as default and skip this question next time, update `ado_project` in your devtools plugin config to: **[selected project name]**"

### Step 1 — Detect the current branch

Run this bash command:
```bash
git rev-parse --abbrev-ref HEAD
```

Extract the US number from the branch name using these patterns (case-insensitive):
- `feat/US-42-description` → 42
- `ALRI/feat/US-42` → 42
- `feat/42-description` → 42
- `fix/us42-description` → 42

### Step 2 — Confirm the User Story

**If a US number was found:**
Use the Azure DevOps MCP tool to fetch the work item by ID.
Show the user: "I found **US #[ID] — [title]**. Is this the right User Story?"
- If yes → continue to Step 3
- If no → ask the user for the correct US number, then fetch it

**If no US number was found:**
Ask: "I couldn't find a US number in the branch name. What is the User Story ID?"
Fetch it with the Azure DevOps MCP tool, then continue.

### Step 3 — Optionally enrich with Confluence specs

Ask: "Would you like me to search Confluence for the spec related to this US? (recommended)"

If yes:
- Use the Confluence MCP tool to search for pages related to the US title
- Use the most relevant page content to improve task generation

### Step 4 — Generate tasks

Based on the US title, description, and Confluence spec (if available), propose a list of 3–5 tasks. Each task title should be:
- Action-oriented (start with a verb: Implement, Write, Review, Update)
- Specific to the US context (not generic like "Testing")
- Between 5 and 15 words

Show the list to the user:
"Here are the tasks I suggest for **US #[ID]**:
1. [task 1]
2. [task 2]
...

You can add, remove, or edit tasks before I create them."

### Step 5 — Validate with the user

Wait for the user to confirm or modify the list.
Accept free-text modifications ("remove #2", "change #3 to: ...", "add: ...").

### Step 6 — Create the tasks in Azure DevOps

For each confirmed task:
1. Use the Azure DevOps MCP tool to create a Task work item with:
   - Title: the task title
   - Parent: the US ID (to link it as a child)
   - Area Path and Iteration Path: same as the parent US (read from the US work item)

After all tasks are created, confirm: "✓ Created [N] tasks linked to US #[ID] in Azure DevOps."
