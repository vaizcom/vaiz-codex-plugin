---
name: vaiz-memories
description: Persist and retrieve team knowledge in Vaiz Space Memory — assemble task context, write Devlogs and durable records, search with lifecycle-aware ranking, and link entries to tasks and GitHub.
---

# Vaiz Space Memory

Space Memory is the shared, long-lived knowledge base of a Vaiz space. This skill explains how to read from it before starting work, how to write back what the team should remember, and how to keep records accurate over time.

## Prerequisite

The Vaiz MCP server (`https://api.vaiz.com/mcp`) must be available through the installed Vaiz plugin and authorized with OAuth. This skill does not replace authentication: if the Vaiz tools are missing or return an authorization error, open the Vaiz connection and complete OAuth sign-in first.

## When to use

- **Starting a task** — pull the task's context and the memories that govern it before writing code.
- **Ending a session** — record what was done, decided, and discovered so the next person (or agent) does not start from zero.
- **Recording decisions or risks** — as soon as a choice is made or a hazard is identified, even mid-session.
- **Searching team knowledge** — when you need to know how something is done in this space, why it is done that way, or whether a problem has been seen before.

## Kinds

Every memory has exactly one kind. Use these names and no others:

| Kind | Meaning |
| --- | --- |
| `Decision` | A choice that was made and the reasoning behind it |
| `Context` | Background a reader needs to understand the work |
| `Problem` | Something that is broken, blocked, or wrong |
| `Risk` | Something that may go wrong and what to watch for |
| `Convention` | A rule or pattern the team follows |
| `SessionLog` | Narrative of a single work session |

Do not invent kinds such as `Devlog`, `Fact`, or `Note`. A **Devlog** is not a kind: it is created with `create_devlog` and stored as one `SessionLog` plus the durable `Decision`, `Problem`, and `Risk` records you pass alongside it.

## Lifecycle

Each memory has a status: `Active`, `Resolved`, `Superseded`, or `Obsolete`.

- `Resolved` applies **only** to `Problem` and `Risk`. It means the problem was fixed or the risk no longer exists.
- `Decision`, `Context`, and `Convention` stay `Active` for as long as they still govern the work. They are never "resolved".
- `Superseded` is set **only** through `supersede_memory`, passing the newer memory id first and the older id second. The older record is marked superseded and points to its replacement.
- `set_memory_status` accepts only `Active`, `Resolved`, or `Obsolete`. Use `Obsolete` when a record is simply no longer true and nothing replaces it.
- `set_memory_archived` hides a duplicate or test record from normal listings. Archiving is **not** the same as `Obsolete`; it does not make a statement about whether the record was ever correct.

Search ranks `Active` records above `Resolved`, `Superseded`, and `Obsolete` ones, so keeping statuses accurate directly improves what future readers see first.

## Hygiene

If the current session fixes a recorded `Problem` or eliminates a recorded `Risk`, call `set_memory_status` with `Resolved` on that record **in the same session** — usually right after `create_devlog`. Only do this when you have confirmed the fix; do not guess. If you are not sure whether the problem is gone, leave it `Active` and say so in the SessionLog.

## Reading

- `get_task_context` — pass a task database id or its HRID (for example `PRJ-123`). Returns the task together with the memories linked to it and to its project. Use it first when starting on a task.
- `search_memory` — natural-language query across the space. Results are ranked with lifecycle awareness, so active records surface first. Use it for "how do we…", "why did we…", and "has anyone seen…" questions.
- `list_memories` — filter by kind, status, project, task, tag, or author when you want a structured view rather than a semantic search.

Always search before creating. If an equivalent record already exists, update its status, supersede it, or link it to the task instead of creating a duplicate.

## Writing

### End of session: `create_devlog`

Prefer `create_devlog` when closing a session. Provide:

- a `SessionLog` narrative: what was attempted, what changed, what remains;
- arrays of `Decision`, `Problem`, and `Risk` entries discovered during the session (each becomes its own durable record);
- `relatedTasks` — the task ids or HRIDs this work belongs to;
- `projectId` — the project the work lives in;
- `relatedGitHubEntities` — pull requests, issues, commits, or branches touched;
- a stable `idempotencyKey` — reuse the exact same key when retrying after a failure so the Devlog is not written twice.

Set `clientName` to `Codex`. Leave `linkGitHubEntitiesToRelatedTasks` at `false` unless the same code link genuinely belongs on every related task.

### Single record: `create_memory`

Use `create_memory` for one durable record outside a Devlog — for example a `Convention` agreed in a review, or a `Decision` made before any code is written. Give it a kind, a clear title, the body, and any related tasks, project, tags, and GitHub entities. Set `clientName` to `Codex`.

### What not to store

- Transient local state: `git status` output, whether tests are currently running, which files are open in the editor, or anything that is only true on your machine right now.
- Several topics in one entry. Keep one topic per record so it can be found, superseded, or resolved independently.

### Tags

Tags are cross-cutting themes such as `oauth`, `billing`, or `migrations`. Do not use tags for task ids, dates, or GitHub references — those belong in `relatedTasks`, timestamps, and `relatedGitHubEntities`.

## GitHub links

To connect memories and tasks with code:

1. `get_github_repositories` — list repositories connected to the space.
2. `search_github` — find the pull request, issue, commit, or branch to link.
3. `link_github_entity` — attach it to a task or memory.
4. `get_github_links` — review what is already linked.
5. `unlink_github_entity` — remove a link that no longer applies.

## Further reading

- How to connect Vaiz to MCP: https://vaiz.com/help/tutorials/how-to-connect-vaiz-to-mcp
- Vaiz: https://vaiz.com
