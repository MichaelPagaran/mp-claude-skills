---
name: jira-ticket-writer
description: Turns rough notes, bug reports, Slack messages, or feature ideas into a single well-structured Jira ticket that follows the organization's ticket standard, shows it as a draft for review, and creates it in Jira only after the user explicitly confirms. Use this skill whenever the user wants to write, draft, create, file, log, or raise a Jira ticket, issue, story, bug, task, or spike, or says things like "make a ticket for this", "turn this into a story", "log this bug", or "can you put this in Jira", even if they don't say "Jira" outright but are clearly describing work that should be tracked as a ticket.
---

# Jira Ticket Writer

Turn whatever the user gives you (rough notes, a pasted bug report, a feature idea, a chat thread) into one Jira ticket that follows the organization's standard. Always show a draft first. Only create the ticket in Jira after the user clearly approves it.

Version 1 handles **one ticket per request**. If the input clearly describes several separate pieces of work, say so, suggest how you'd split them, and ask which one to draft first.

## Step 1: Load the ticket standard

The standard defines the issue types, required sections, summary style, priority rules, and labels. Read it before drafting, since that's what makes the ticket look like the team's other tickets rather than a generic one.

The standard comes from one of three places. Use the first one that applies:

1. **A Confluence link in the current request.** It applies to this ticket only. Don't save it.
2. **The saved Confluence page**, if the user has set one (see below).
3. **The bundled default**, `references/standards.md`.

Always read `references/standards.md`. When a Confluence page is used, fetch it with the Atlassian `getConfluenceContent` tool (`detail: "full"`, `content_format: "markdown"`). Where the page and the local file disagree, the page wins, because it's the version the team maintains. If the fetch fails, carry on with the local file, say so in one line, and suggest `/jira-ticket-writer:set-standard` to fix the link. Don't block the draft on it.

### The saved setting

The user's choice is kept in a small JSON file, so they never have to edit configuration by hand:

- **Path:** `${CLAUDE_PLUGIN_DATA}/config.json`. If the path still shows the literal text `${CLAUDE_PLUGIN_DATA}` (because the skill isn't running as an installed plugin), use `~/.claude/jira-ticket-writer/config.json` instead.
- **Format:** `{ "standards_url": "<confluence url>" or null, "updated": "<YYYY-MM-DD>" }`. A `null` URL means the user chose the built-in default.

**If the file doesn't exist yet**, this is the first run on this machine. Before drafting, ask once:

> Quick one-time setup: does your team have a Confluence page with ticket policies? Paste the link, or say "skip" to use the built-in default. You can change this anytime with `/jira-ticket-writer:set-standard`.

- **They give a link:** check that you can read it with `getConfluenceContent` (`detail: "outline"`). If you can, save it and mention the page title. If you can't, tell them and ask whether to save it anyway or skip.
- **They say skip, or ignore the question:** save `null`.

Then carry on with the ticket in the same turn, so setup never costs the user an extra round. Create the folder if needed, and write the file with the Write tool.

**If the file exists**, don't ask again. Use the saved URL, or the default if it's `null`.

## Step 2: Understand the input

Work out from the input:

- **Issue type**: Bug, Story, Task, or Spike, using the rules in the standard.
- **Project key**: Use one the user named or one that's obvious from context (for example, a ticket key like `EDGE-123` in the input suggests `EDGE`). Otherwise you'll need to ask. Don't guess a project, because a ticket in the wrong project is easy to miss.
- **The facts**: what's happening, who is affected, what should happen, and any steps, environments, or links.

Don't invent facts. If the input doesn't say how to reproduce a bug or what the expected behavior is, don't make it up. Put it under **Open questions** in the draft so the gap is visible. A ticket that looks complete but contains made-up details is worse than one that's honestly missing something.

Only ask a question before drafting when you truly can't produce a useful draft without the answer. Usually it's better to draft with clear placeholders and let the user fill the gaps in the review step, all in one round.

### Customer data

Bug reports often include customer names, emails, phone numbers, or account details. Jira tickets are widely visible, so replace personal information with neutral placeholders such as `[customer]`, `[customer email]`, or `[account ID]`, and tell the user you did it. If the reproduction really depends on a specific account, suggest they reference it by an internal ID instead.

## Step 3: Show the draft

Present the draft in chat in this format, so the user can review exactly what will be sent to Jira:

```
**Draft Jira ticket**

| Field | Value |
|---|---|
| Project | EDGE (or "not set, please confirm") |
| Issue type | Bug |
| Summary | <summary following the standard's style> |
| Priority | Medium |
| Labels | label-one, label-two |
| Parent / Epic | <key, if given> |

**Description**

<description body using the sections the standard requires for this issue type>

**Open questions** (only if there are gaps)
- <what's missing and why it matters>

Reply **"publish"** to create it in Jira, or tell me what to change.
```

Keep the description in Markdown. The Jira tool converts it to Jira's format automatically.

## Step 4: Revise until approved

If the user asks for changes, update the draft and show the full revised version again, so what they approve is exactly what gets created.

## Step 5: Publish only on explicit confirmation

Creating a Jira ticket notifies people and is visible to the team, so wait for a clear go-ahead such as "publish", "create it", "yes, go ahead", or "looks good, ship it". If the reply is ambiguous (for example, "nice" or "hmm, ok"), ask whether they want it created. Never publish in the same turn you first show the draft.

Before publishing, make sure the project key is confirmed. Then:

1. Get the `cloudId` with `getAccessibleAtlassianResources`, once per session.
2. Call `createJiraIssue` with `projectKey`, `issueType`, `summary`, `description`, `priority`, and `labels`, plus `parent` if there is one. Put any other fields the standard requires in `additional_fields`.
3. If Jira rejects the request, read the `repairHint` and `allowedValues` in the error. If the fix is mechanical (for example, the priority is called "P2" instead of "Medium" in this project), retry once with the corrected value and tell the user what you changed. If the fix changes the meaning of the ticket, ask the user first.
4. If the response includes an issue key, the ticket exists. Don't call `createJiraIssue` again for the same draft, even if part of the response (like sprint assignment) failed, because that would create a duplicate.

Then reply with the new ticket key and link, and note anything that didn't apply cleanly.
