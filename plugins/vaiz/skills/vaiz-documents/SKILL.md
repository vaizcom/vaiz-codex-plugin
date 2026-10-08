---
name: vaiz-documents
description: Read, create, and edit Vaiz documents and comments — standalone documents in personal, project, or space trees, task and milestone descriptions, Markdown with mentions, threaded comments, and document history.
---

# Vaiz documents and comments

Every task and milestone in Vaiz carries a document (its description). Standalone documents live in a tree that is personal, per project, or shared across the space. All content is written in Markdown.

## Prerequisite

The Vaiz MCP server (`https://api.vaiz.com/mcp`) must be available through the installed Vaiz plugin and authorized with OAuth (open the Vaiz connection and complete OAuth sign-in).

Vaiz tools may be loaded lazily: the `mcp__Vaiz` namespace can be deferred, with only some of its tools loaded at the start of a turn. A Vaiz tool that is not in the current tool list is not missing — load it by name through tool search (for example `Vaiz get_tasks`) before concluding it is unavailable. Work with Vaiz only through these tools: do not open app.vaiz.com in a browser or drive the Vaiz desktop app with Computer Use, even if the user's `@Vaiz` mention also resolves to that app.

## Two kinds of "documents"

- **User documents** — content your team wrote: standalone docs, task and milestone descriptions. Tools: `get_document`, `create_document`, `edit_document_content`.
- **System help** — Vaiz's own knowledge base. Do not use `get_document` for it; use `list_resources` and `read_resource` (see the `vaiz-help` skill).

## Reading

- `get_document(documentId)` — a user document with its parent chain and child documents. Find ids with `search_space(entityType: "document")`.
- Task and milestone descriptions are returned by `get_task` and `get_milestone`; the document id needed for editing is in that response.
- `get_document_comments` — comments on a standalone document; `get_task_comments` for tasks.
- `get_document_history` — change history of a document.
- Files referenced inside a document can be read with `read_file` (see `vaiz-files`).

## Creating a standalone document

`create_document` takes a `data` object:

- `title` (required)
- `container`: `"personal"` (default, private to you), `"project"` (requires `projectId`), or `"space"` (shared)
- `description` — initial Markdown content
- `parentDocumentId` — nest under an existing document

Do not use `create_document` for task or milestone descriptions; those documents already exist and are edited with `edit_document_content`.

## Editing content

`edit_document_content` with `data`:

- `documentId` — from `get_task`, `get_milestone`, or `get_document`
- `description` — Markdown
- `replace`: `false` (default) appends to the existing content; `true` replaces it entirely

Default to appending. Replace only when the user explicitly asks to rewrite, and read the current content first so nothing is lost.

## Markdown and mentions

Headings, lists, code blocks, and tables are supported. Mention entities with `@[label](kind:id)` where `kind` is `user`, `task`, `document`, `milestone`, `project`, or `board`, for example `@[Anna](user:64f1…)`. Use `list_members` for user ids.

## Comments

`create_comment` with `entityType` (`"task"`, `"milestone"`, `"document"`) and `entityId` (task HRID or id; 24-char id for the others). `content` is Markdown. `replyTo` posts a threaded reply under an existing comment. `fileIds` attaches files that were already uploaded to the same entity (see `vaiz-files`); content may be omitted when files are provided.

Use a comment for discussion, status, or questions. Use the description for the durable definition of the work.

## Good habits

- Search before creating a document; a similar one may already exist.
- Keep one topic per document and nest related documents with `parentDocumentId`.
- Decisions and conventions that should be searchable across the space belong in Space Memory (see `vaiz-memories`), possibly with a link back to the document.

Further reading: https://vaiz.com/help/tutorials/how-to-connect-vaiz-to-mcp · https://vaiz.com
