---
name: vaiz-workspace
description: Manage the Vaiz session and people context — check connection and current user, list and switch spaces, look up members and their activity, review notifications, and query completed time-tracking entries.
---

# Vaiz workspace, members, and activity

Use this skill to establish who you are, which space you are working in, who else is on the team, and what has been happening.

## Prerequisite

The Vaiz MCP server (`https://api.vaiz.com/mcp`) must be available through the installed Vaiz plugin and authorized with OAuth. If tools are missing or return an authorization error, open the Vaiz connection and complete OAuth sign-in. This skill does not replace that step.

## Connection and identity

- `ping_server` — health check; use it first when something looks broken.
- `get_current_user` — the authenticated user and the current space context. Returns your member id, which `get_tasks(assignees: [...])` and other filters need.
- `get_space_info` — details of the selected space and your permissions in it.

## Spaces

A user may belong to several spaces (workspaces). The MCP session has one selected space at a time.

- `list_spaces` — all spaces available to you, with ids.
- `select_space(spaceId)` — switch the session to another space.

Most tools also accept an optional `spaceId` argument that overrides the session space for that single call. Pass it when the user is clearly working across spaces or when you cannot rely on session state.

## Members

- `list_members` — everyone in the current space with ids and roles. Follows the list response protocol: if the first text block says the rows were rendered in a widget, reply with a short sentence instead of repeating the table.
- `get_member(memberId)` — one member in detail.
- `search_space(entityType: "user", query)` — find a member by name.
- `get_user_history(memberId)` — that member's activity across the workspace. Narrow with `dateRangeStart` / `dateRangeEnd`, `projectIds`, `boardIds`, `taskIds`, `entityTypes`, or event `keys`; paginate with `limit` and `page.nextCursor`.

Member ids are what `assignees`, `createdBy`, and mention syntax (`@[Name](user:id)`) expect.

## Notifications

`get_notifications` returns your notifications with `readStatus` (`"All"`, `"Read"`, `"Unread"`), `groups`, `pinned`, and `limit`. Paginate with `lastLoadedDate` (Unix ms of the newest loaded notification). Use it to answer "what needs my attention" questions.

## Time tracking

`get_time_entries` returns completed time-tracking entries with task, member, and project data plus exact totals. Filters: `dateFrom` / `dateTo`, `projectIds`, `taskIds` (ids or HRIDs), `memberIds`, `billable` (`"all"`, `"billable"`, `"non_billable"`), `isManual`, `statuses`. Sort with `sortBy` (`"startTime"`, `"duration"`) and `sortOrder`; aggregate with `groupBy` (`"member"`, `"task"`, `"project"`). Available to space owners and managers only — if the call is denied, say so rather than retrying.

## Good habits

- Resolve the space and your member id once at the start of a session.
- Do not paste other members' personal data into Space Memory or documents beyond what the task needs.
- Use `get_user_history` and `get_project_history` (see `vaiz-projects-milestones`) for summaries instead of scanning tasks one by one.

Further reading: https://vaiz.com/help/tutorials/how-to-connect-vaiz-to-mcp · https://vaiz.com
