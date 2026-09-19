---
name: vaiz-projects-milestones
description: Navigate the Vaiz workspace structure — list projects and boards, inspect project activity history, and list, read, or create milestones with due dates and rich descriptions.
---

# Vaiz projects and milestones

A Vaiz space contains projects; each project contains boards; boards hold tasks and milestones. Use this skill to orient yourself in a space and to plan work at the milestone level.

## Prerequisite

The Vaiz MCP server (`https://api.vaiz.com/mcp`) must be available through the installed Vaiz plugin and authorized with OAuth (open the Vaiz connection and complete OAuth sign-in).

## Orientation

Start here when you do not yet know the ids you need:

1. `get_space_info` — current space, your permissions.
2. `list_projects` — all projects in the space with their ids.
3. `list_boards` — all accessible boards, or `list_boards(projectId)` for one project. Board ids are required by `create_task` and `create_milestone`.
4. `get_project` — details of one project.
5. `get_board` — columns, task types, custom fields of one board.
6. `search_space` with `entityType: "project"` or `"board"` — when you only know a name.

## Project history

`get_project_history(projectId)` returns activity across the project: task changes, comments, document edits, and more. Narrow it with `dateRangeStart` / `dateRangeEnd` (ISO dates), `entityTypes`, `taskIds`, `boardIds`, `createdBy`, or event `keys` / `excludeKeys`. Paginate with `limit` and the `page.nextCursor` value from the previous response.

Use it for "what happened in this project this week" style questions instead of reading every task.

## Milestones

- `list_milestones` — all milestones in the space, optionally filtered by `boardId` or `projectId`. Follows the list response protocol: if the first text block says the rows were rendered in a widget, do not repeat them as a table.
- `get_milestone(milestoneId)` — one milestone with its document and linked tasks.
- `create_milestone` — requires `name` and `boardId`. Optional `description` is a **short plain-text summary** (not Markdown). `dueStart` / `dueEnd` are ISO 8601 dates.

To give a milestone rich content, create it first, then call `edit_document_content` with the milestone's document id (see the `vaiz-documents` skill). To attach tasks to a milestone, pass the milestone id in `milestones` when calling `create_task` or `edit_task` (see `vaiz-tasks`). Subtasks cannot carry milestones.

Comments on a milestone go through `create_comment` with `entityType: "milestone"`.

## Good habits

- Resolve names to ids once, then reuse the ids for the rest of the session.
- Check `list_milestones` before creating one to avoid duplicates.
- Record planning decisions that outlive the milestone in Space Memory (see `vaiz-memories`).

Further reading: https://vaiz.com/help/tutorials/how-to-connect-vaiz-to-mcp · https://vaiz.com
