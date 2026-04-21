# Apollo Plugin for Claude Code

Azure DevOps + Confluence integration for Claude Code.

## Skills

- `/apollo:create-task` — Detect the US from the current branch and create linked tasks in Azure DevOps

## Installation

### Local (development)

```bash
claude plugin install atlassian@claude-plugins-official
claude --plugin-dir ./devtools-plugin
```

### Team (via GitHub marketplace)

Add to `~/.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "apollossc": {
      "source": "github",
      "repo": "apollossc/devtools-plugin"
    }
  }
}
```

Then:

```bash
claude plugin install apollo@apollossc
```

At activation you will be prompted for:
- `ado_org` — Azure DevOps organization name (e.g. `apollo-ssc`)
- `ado_project` — Azure DevOps project name (optional — leave empty to pick from a list at runtime)

Confluence credentials are managed by the official Atlassian plugin.

## Authentication

**Azure DevOps**: On first tool use, a browser will open for Azure AD login. Credentials are cached automatically.

**Confluence**: Configured via the `atlassian@claude-plugins-official` plugin (API token from [id.atlassian.com](https://id.atlassian.com/manage-profile/security/api-tokens)).
