# mp-claude-skills
This is a repository for my personal claude skills that I can use across multiple projects.

Each skill is packaged as its own Claude Code plugin, so you can install only the ones you need.

## Install

```
/plugin marketplace add MichaelPagaran/mp-claude-skills
/plugin install <plugin-name>@mp-claude-skills
```

For example, `/plugin install jira-ticket-writer@mp-claude-skills`. See [INSTALL.md](INSTALL.md) for the full step-by-step guide, updating, troubleshooting, and the list of available plugins.

## Maintainer: adding a new skill

1. Create the skill at `plugins/<name>/skills/<name>/SKILL.md`.
2. Add `plugins/<name>/.claude-plugin/plugin.json` with at least `name` and `description`.
3. Add an entry to the `plugins` list in `.claude-plugin/marketplace.json`:
   ```json
   { "name": "<name>", "source": "./plugins/<name>", "description": "..." }
   ```
4. Add a row to the **Available plugins** table in `INSTALL.md`.
5. Check it with `claude plugin validate ./` before pushing.
