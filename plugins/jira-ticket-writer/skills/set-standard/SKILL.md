---
name: set-standard
description: Sets, shows, or resets the Confluence page that jira-ticket-writer uses as the team's Jira ticket standard. Use this when the user runs /jira-ticket-writer:set-standard, or asks to change, use, point to, clear, or check the Confluence page or ticket policy that Jira tickets should follow (for example "use this Confluence page as our ticket standard" or "which ticket standard are we using?").
argument-hint: "[confluence-url | default | show]"
---

# Set the Jira ticket standard

This manages which ticket standard `jira-ticket-writer` follows. The user never edits the setting by hand, so you read and write it for them.

## Where the setting lives

- **Path:** `${CLAUDE_PLUGIN_DATA}/config.json`. If the path still shows the literal text `${CLAUDE_PLUGIN_DATA}` (because the skill isn't running as an installed plugin), use `~/.claude/jira-ticket-writer/config.json` instead. This is the same rule `jira-ticket-writer` uses, so both always point at the same file.
- **Format:** `{ "standards_url": "<confluence url>" or null, "updated": "<YYYY-MM-DD>" }`. A `null` URL means the built-in default standard.

Create the folder if needed, and write the file with the Write tool.

## What to do

The argument is: `$ARGUMENTS`

| Argument | Action |
|---|---|
| A URL | Set it as the standard (see below). |
| `default`, `reset`, `none`, or `clear` | Save `"standards_url": null`. Confirm that tickets will now follow the built-in default standard. |
| `show`, empty, or anything else | Report the current setting (see below). |

The user may also ask in plain words instead of using the command. Work out which of the three they mean.

### Setting a URL

1. Check that it looks like a Confluence page link (an `atlassian.net/wiki/...` address, or a Confluence short link). If it doesn't, say so and don't save it.
2. Read it with the Atlassian `getConfluenceContent` tool (`content_url`, `detail: "outline"`). You'll need a `cloudId` from `getAccessibleAtlassianResources`. Reading it first means a typo or a page the user can't access is caught now, not halfway through drafting a ticket later.
3. **If the page can be read**, save the URL with today's date. Confirm with the page title, for example: "Saved. Tickets will now follow *Ticket Writing Guidelines*."
4. **If the page can't be read**, explain why if you can tell (for example, no access, page not found, or the Atlassian connector isn't connected). Ask whether to save it anyway. Don't save it unless they say yes.

### Showing the current setting

- **No file:** the user hasn't been asked yet. Say the built-in default is in use, and that `jira-ticket-writer` will offer to set a page on its next run.
- **A URL is saved:** show it and when it was set. If you can, also show the page title by reading it with `detail: "summary"`.
- **`null`:** say the built-in default standard is in use.

Always end with the commands they can use:

- `/jira-ticket-writer:set-standard <url>`
- `/jira-ticket-writer:set-standard default`

The setting takes effect on the next ticket. No restart is needed.
