---
name: vaiz-tasks
description: Find, read, create, and update Vaiz tasks and boards — filter task lists, inspect a task with its comments and history, create tasks with Markdown descriptions, edit fields, move between columns, and manage blocker relationships.
---

# Vaiz tasks and boards

Tasks live on boards; boards belong to projects. A task is identified either by its 24-character database id or by its human-readable id (HRID) such as `PRJ-123`. Every task tool accepts both.

## Prerequisite

The Vaiz MCP server (`https://api.vaiz.com/mcp`) must be available through the installed Vaiz plugin and authorized with OAuth. If tools are missing or return an authorization error, open the Vaiz connection and complete OAuth sign-in.

Vaiz tools may be loaded lazily: the `mcp__Vaiz` namespace can be deferred, with only some of its tools loaded at the start of a turn. A Vaiz tool that is not in the current tool list is not missing — load it by name through tool search (for example `Vaiz get_tasks`) before concluding it is unavailable. Work with Vaiz only through these tools: do not open app.vaiz.com in a browser or drive the Vaiz desktop app with Computer Use, even if the user's `@Vaiz` mention also resolves to that app.

## Finding tasks

- `get_tasks` — filtered list with pagination. Filters combine with AND. Useful patterns:
  - my open tasks: `assignees: [myMemberId], completed: false`
  - a board column by name across all boards: `groupLabel: "In Progress"`
  - epics in a project: `projectId, typeLabel: "Epic"`
  - unassigned: `assignees: "empty"`; no deadline: `dueDate: "empty"`; no milestone: `milestones: "empty"`
  - overdue: `dueBefore: <now ISO>, completed: false`
  - include archived: `includeArchived: true`
  Prefer `groupLabel` and `typeLabel` when you know names but not ids; use `groupId` / `types` when you already have ids from `get_board`.
- `search_space` with `entityType: "task"` — keyword search over names and content when you do not know the HRID.
- `get_task_context` — compact decisions, problems, risks, recent Devlogs, and GitHub links for a task (see the `vaiz-memories` skill).

Find your own member id with `get_current_user`; find other members with `list_members`.

## Reading a task

- `get_task` — full task by id or HRID. Custom field values are omitted unless `includeCustomFields: true`.
- `get_task_comments` — comment thread.
- `get_task_history` — change log.
- `get_board` — board structure: columns (`groups`), task types (`typesList`), custom fields. Read it before creating tasks on an unfamiliar board.
- `get_automations` — workflows configured on a board, useful to understand why a task moved or changed automatically.

## Creating a task

`create_task` takes a `data` object. Required: `name` and `boardId`. Optional: `groupId` (defaults to the first column), `description` in Markdown, `priority` (`"0"` Low, `"1"` General, `"2"` Medium, `"3"` High, `"4"` Urgent — names are accepted too), `assignees`, `dueStart`, `dueEnd` (ISO 8601), `parentTask` for a subtask, `milestones` (not allowed on subtasks), `types` from the board's `typesList`, and `customFields`.

Mentions in Markdown use `@[label](kind:id)` where kind is `user`, `task`, `document`, `milestone`, `project`, or `board`.

Before creating: search for an existing task on the same topic with `get_tasks` or `search_space`, and read `get_board` to pick a valid column and type.

## Editing a task

`edit_task` takes a `data` object with `taskId` plus only the fields to change: `name`, `assignees` (replacement list; `[]` unassigns everyone), `completed` (`true` to close, `false` to reopen), `dueStart` / `dueEnd` (`null` clears), `priority`, `types`, `milestones` (replacement lists), `group` to move to another column, `parentTask` (id to nest, `null` to detach), `customFields`.

To change the description, use `edit_document_content` with the task's document id (from `get_task`); see the `vaiz-documents` skill. To discuss rather than change, add a comment with `create_comment` (`entityType: "task"`).

## Blockers

`set_task_blocker` toggles a blocker link between two tasks. `direction` is relative to `taskId`: `"blockers"` means `blockerTaskId` blocks `taskId`; `"blocking"` means `taskId` blocks `blockerTaskId`. Calling it again with the same arguments removes the link.

## Response protocol for lists

`get_tasks` (and other list tools) tell you in the first text block how the result was delivered:

- "rendered for the user in an interactive widget" — the user already sees the rows. Do not repeat them as a table; reply with one short sentence or answer the specific question.
- "rows follow as JSON in the next content block" — present the rows yourself as a concise table.

If neither phrase is present: `structuredContent` without a `data` array means a widget was rendered; with `data` means you present it.

## Good habits

- Confirm the space with `get_space_info` when the user has several spaces; switch with `select_space` if needed.
- Do not create tasks for things that belong in Space Memory (decisions, conventions) — use the `vaiz-memories` skill instead.
- Link code to the task with `link_github_entity` when a branch or PR exists (see `vaiz-github-links`).

Further reading: https://vaiz.com/help/tutorials/how-to-connect-vaiz-to-mcp · https://vaiz.com
