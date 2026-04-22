---
description: Configure Azure DevOps credentials for the Apollo plugin
allowed-tools: Read, Edit, Bash(node:*)
---

Configure the Apollo plugin by setting your Azure DevOps credentials in `~/.claude/settings.json`.

## Steps

**Step 1 — Read current settings**

Read `~/.claude/settings.json`. If it doesn't exist, treat it as `{}`.

Check if these are already set under `env`:
- `AZURE_DEVOPS_ORG` (org slug, e.g. `apollo-ssc`)
- `AZURE_DEVOPS_ORG_URL` (full URL, e.g. `https://apollo-ssc.visualstudio.com/`)
- `ADO_MCP_AUTH_TOKEN` (Personal Access Token)

**Step 2 — Ask for missing values**

If `AZURE_DEVOPS_ORG` is not set, ask:
> What is your Azure DevOps organization slug?

If `AZURE_DEVOPS_ORG_URL` is not set
> What is your Azure DevOps organization URL?

If `ADO_MCP_AUTH_TOKEN` is not set, ask:
> What is your Azure DevOps Personal Access Token?
> (Create one at: <org>.visualstudio.com → User Settings → Personal Access Tokens)
> Minimum required scopes: Work Items (Read & Write), Code (Read)

If all values are already set, show current values (mask the token: last 4 chars only) and ask if the user wants to update them.

**Step 3 — Write to settings.json**

Merge the values into the `env` section of `~/.claude/settings.json`:

```json
{
  "env": {
    "AZURE_DEVOPS_ORG": "<org slug>",
    "AZURE_DEVOPS_ORG_URL": "https://<org>.visualstudio.com/",
    "ADO_MCP_AUTH_TOKEN": "<token>"
  }
}
```

Preserve all other existing fields.

**Step 4 — Confirm and instruct**

Show:
- ✓ `AZURE_DEVOPS_ORG` set to `<value>`
- ✓ `AZURE_DEVOPS_ORG_URL` set to `<value>`
- ✓ `ADO_MCP_AUTH_TOKEN` set (…last 4 chars)

Then tell the user: **Restart VS Code (or open a new Claude terminal session) for the changes to take effect.**
