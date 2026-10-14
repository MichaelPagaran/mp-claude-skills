# Jira Ticket Standard

This is the default ticket standard for the `jira-ticket-writer` skill. It's used when no Confluence standard is set. To set one, run `/jira-ticket-writer:set-standard <confluence-url>`.

## Issue types

| Type | Use when |
|---|---|
| **Bug** | Something that used to work, or should work, is broken or behaves incorrectly. |
| **Story** | New or changed behavior that delivers value to a user. Written from the user's point of view. |
| **Task** | Technical or operational work with no direct user-facing change (upgrades, config, cleanup, migrations). |
| **Spike** | Time-boxed research to answer a question or reduce uncertainty before committing to the work. |

If it's unclear whether something is a Bug or a Story, ask: "Is the current behavior wrong, or do we want new behavior?" Wrong behavior is a Bug.

## Summary (title)

- Keep it under about 80 characters.
- Start with the area in brackets when it helps: `[Billing] Invoice PDF shows the wrong tax total`.
- **Bug:** describe the symptom, not the cause or fix: "Login fails with SSO when the email has uppercase letters".
- **Story:** describe the outcome in plain words: "Let admins export user lists as CSV".
- **Task:** start with a verb: "Upgrade Node.js to 22 in the API service".
- **Spike:** phrase it as the question: "Investigate options for replacing the PDF library".
- Don't put ticket keys, dates, or people's names in the summary.

## Description sections by issue type

### Bug

```
## Summary
One or two sentences on what's broken and who it affects.

## Steps to reproduce
1. ...
2. ...

## Expected behavior
...

## Actual behavior
...

## Environment
Environment (production, staging, etc.), browser or device, app version, if known.

## Impact
How many users or customers are affected, and whether there's a workaround.

## Additional context
Logs, screenshots, links to related tickets or threads.
```

### Story

```
## User story
As a <type of user>, I want <goal> so that <benefit>.

## Context
Why this matters now, and any background.

## Acceptance criteria
- [ ] Given <context>, when <action>, then <result>.
- [ ] ...

## Out of scope
What this story deliberately doesn't cover.

## Notes
Designs, links, dependencies.
```

### Task

```
## Goal
What needs to be done and why.

## Details
Specific steps, systems, or files involved.

## Definition of done
- [ ] ...
```

### Spike

```
## Question
What we need to find out.

## Background
Why we're asking, and what decision depends on the answer.

## Time box
For example, 2 days.

## Expected output
For example, a written recommendation, a prototype, or a comparison table.
```

## Acceptance criteria

- Every Story needs at least two acceptance criteria. Tasks need a definition of done.
- Each criterion should be testable: someone should be able to say clearly whether it passed.
- Prefer the Given / When / Then format for user-facing behavior.

## Priority

| Priority | Use when |
|---|---|
| **Highest** | Production is down, there's data loss or a security issue, or many customers are blocked with no workaround. |
| **High** | A major feature is broken or many users are affected, but there's a workaround. |
| **Medium** | The default. Normal planned work, or bugs with limited impact. |
| **Low** | Cosmetic issues, minor improvements, nice-to-haves. |

If the input doesn't give enough to judge, use **Medium** and say so in the draft.

## Labels

- Use lowercase and hyphens: `customer-reported`, `tech-debt`.
- Common labels:
  - `customer-reported`: the issue came from a customer or support.
  - `regression`: something that used to work.
  - `tech-debt`: cleanup or refactoring.
  - `security`: security-related work.
  - `needs-triage`: priority or ownership is unclear.
- Don't invent new labels unless the user asks for one.

## Privacy

- Never put customer personal information (names, emails, phone numbers, addresses) in a ticket. Use placeholders such as `[customer]` or an internal account ID.
- Never paste passwords, API keys, or tokens. Say where they can be found instead.
