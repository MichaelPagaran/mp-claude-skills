# Installing plugins from mp-claude-skills

Each skill in this repo is packaged as its own Claude Code plugin, so you can install only the ones you want. These steps work for any plugin in this marketplace, now or in the future. Replace `<plugin-name>` with the plugin you want (see [Available plugins](#available-plugins)).

Every step shows two ways to run it:

- **In Claude Code:** type the `/plugin …` command in a Claude Code session.
- **In a terminal:** run the `claude plugin …` command in your shell.

## Prerequisites

- Claude Code is installed. Check with:
  ```bash
  claude --version
  ```
- You can access the GitHub repo `MichaelPagaran/mp-claude-skills`. For a private repo, Claude Code uses your existing `git` or `gh` login.

## Step 1: Add the marketplace (once per machine)

In Claude Code:
```
/plugin marketplace add MichaelPagaran/mp-claude-skills
```

In a terminal:
```bash
claude plugin marketplace add MichaelPagaran/mp-claude-skills
```

Check that it was added:
```bash
claude plugin marketplace list
```

## Step 2: See which plugins are available

In Claude Code, run `/plugin` and browse the plugins from `mp-claude-skills`:
```
/plugin
```

In a terminal:
```bash
claude plugin list --available --json
```

## Step 3: Install a plugin

In Claude Code:
```
/plugin install <plugin-name>@mp-claude-skills
```

In a terminal:
```bash
claude plugin install <plugin-name>@mp-claude-skills --scope user
```

Example:
```
/plugin install jira-ticket-writer@mp-claude-skills
```
```bash
claude plugin install jira-ticket-writer@mp-claude-skills --scope user
```

`--scope` controls where the plugin is available:

| Scope | Where it applies |
|---|---|
| `user` (default) | All your projects on this machine |
| `project` | Everyone working in the current repo (saved in `.claude/settings.json`, which you commit) |
| `local` | Only you, only in the current repo (not committed) |

## Step 4: Load the plugin

In Claude Code:
```
/reload-plugins
```

You can also restart Claude Code instead.

## Step 5: Check that it worked

In a terminal:
```bash
claude plugin list
claude plugin details <plugin-name>
```

Example:
```bash
claude plugin details jira-ticket-writer
```

In a Claude Code session, the plugin's skills appear as `<plugin-name>:<skill-name>`.

## Step 6: Use it

You can use a skill in two ways:

- **Just ask.** Skills trigger on their own when your request matches. For example: "make a Jira ticket for this bug: the export button does nothing on Safari."
- **Call it directly:**
  ```
  /<plugin-name>:<skill-name>
  ```
  For example:
  ```
  /jira-ticket-writer:jira-ticket-writer
  ```

## Configuring plugins

Some plugins have settings. Claude asks for them and saves them, so you never edit configuration files by hand. Settings are kept when the plugin updates, removed when you uninstall it, and take effect right away with no restart.

### jira-ticket-writer: ticket standard

By default, tickets follow a built-in standard. The first time you ask for a ticket, Claude asks once whether your team has a Confluence page with ticket policies. Paste the link, or say "skip" to keep the default.

To change it later:

```
/jira-ticket-writer:set-standard https://<your-site>.atlassian.net/wiki/spaces/<SPACE>/pages/<id>
```
```
/jira-ticket-writer:set-standard default
```
```
/jira-ticket-writer:set-standard show
```

- The first sets a new page. Claude checks that it can read the page before saving it.
- `default` goes back to the built-in standard.
- `show` shows which standard is in use.

You can also just ask, for example: "use this Confluence page as our ticket standard: …".

To use a different page for one ticket only, paste its link in your request. Your saved setting doesn't change.

## Updating plugins

In Claude Code:
```
/plugin marketplace update mp-claude-skills
/reload-plugins
```

In a terminal:
```bash
claude plugin marketplace update mp-claude-skills
claude plugin update <plugin-name>@mp-claude-skills
```

Auto-update is off by default for marketplaces outside Anthropic's. To turn it on, run `/plugin`, open the marketplaces view, select `mp-claude-skills`, and enable auto-update.

## Disabling or uninstalling

In Claude Code, run `/plugin`, open your installed plugins, select the plugin, and choose **Disable** or **Uninstall**.

In a terminal:
```bash
claude plugin disable <plugin-name>@mp-claude-skills
claude plugin uninstall <plugin-name>@mp-claude-skills
```

To remove the whole marketplace:
```bash
claude plugin marketplace remove mp-claude-skills
```

## Troubleshooting

| Problem | What to do |
|---|---|
| The skill doesn't show up | Run `/reload-plugins` or restart Claude Code, then check `claude plugin details <plugin-name>`. |
| The same skill shows up twice | You probably also have a copy or link of it in `~/.claude/skills/`. Remove that copy, or don't install the plugin on that machine. |
| The skill can't reach Jira, Confluence, etc. | The plugin needs a connector. Check the **Requires** column below and connect it in Claude settings. |
| You want to try a plugin without installing it | Clone the repo and run `claude --plugin-dir <path-to-repo>/plugins/<plugin-name>`. Some organizations turn this flag off in managed settings. |
| The marketplace can't be added | Make sure you can open the GitHub repo and that `git` or `gh` is logged in. If your organization restricts plugin sources, ask an admin to approve this marketplace. |

## Available plugins

| Plugin | What it does | Requires |
|---|---|---|
| `jira-ticket-writer` | Drafts one Jira ticket from rough notes using your org's standard, and creates it only after you confirm. `/jira-ticket-writer:set-standard` picks the Confluence page it follows. | Atlassian connector |
