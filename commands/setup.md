---
description: Configure Azure DevOps credentials for the Apollo plugin
allowed-tools: Read, Edit, Bash(node:*)
---

Configure the Apollo plugin by setting your Azure DevOps credentials in `~/.claude/settings.json`.

## Steps

**Step 1 — Read current settings**

Read the file at `C:\Users\$USERNAME\.claude\settings.json` (or `~/.claude/settings.json`). If it doesn't exist, treat it as `{}`.

Check if `env.AZURE_DEVOPS_ORG_URL` and `env.AZURE_DEVOPS_TOKEN` are already set.

**Step 2 — Ask for missing values**

If `AZURE_DEVOPS_ORG_URL` is not set, ask the user:
> What is your Azure DevOps organization URL? (e.g. `https://your-org.visualstudio.com/` or `https://dev.azure.com/your-org/`)

If `AZURE_DEVOPS_TOKEN` is not set, ask the user:
> What is your Azure DevOps Personal Access Token?
> (Create one at: your-org.visualstudio.com → User Settings → Personal Access Tokens)
> Minimum required scopes: Work Items (Read & Write), Code (Read)

If both are already set, show their current values (mask the token: show only last 4 chars) and ask if the user wants to update them.

**Step 3 — Write to settings.json**

Merge the values into the `env` section of `~/.claude/settings.json`:

```json
{
  "env": {
    "AZURE_DEVOPS_ORG_URL": "<value provided>",
    "AZURE_DEVOPS_TOKEN": "<value provided>"
  }
}
```

Preserve all other existing fields in the file.

**Step 4 — Confirm and instruct**

Show a success message with:
- ✓ `AZURE_DEVOPS_ORG_URL` set
- ✓ `AZURE_DEVOPS_TOKEN` set (masked)

Then tell the user: **Restart VS Code (or open a new Claude terminal session) for the changes to take effect.**
