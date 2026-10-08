---
name: vaiz-files
description: Upload files to Vaiz tasks, milestones, and documents from local disk or a public URL, insert them into document bodies or comments, and read attached files back including inline images and text.
---

# Vaiz files

Files in Vaiz are always attached to an entity: a task, a milestone, or a document. Uploading is a two-step process (get an upload URL, then send bytes); attaching is a separate call.

## Prerequisite

The Vaiz MCP server (`https://api.vaiz.com/mcp`) must be available through the installed Vaiz plugin and authorized with OAuth (open the Vaiz connection and complete OAuth sign-in). Local uploads also require the ability to run an HTTP request (for example `curl` in the terminal).

Vaiz tools can appear under different names depending on how Vaiz is connected: `mcp__Vaiz__get_tasks` for a directly configured MCP server, or `mcp__codex_apps__vaiz_get_tasks` (sometimes with a longer app prefix such as `vaiz_testing_`) when Vaiz is installed as an app. Match tool names case-insensitively and by the tool suffix (`get_tasks`, `query_tasks`, …), not by an exact `Vaiz` prefix. Tools may also be loaded lazily: a Vaiz tool that is not in the current tool list is not missing — load it through tool search (for example `Vaiz get_tasks`) before concluding it is unavailable. Work with Vaiz only through these tools: do not open app.vaiz.com in a browser or drive the Vaiz desktop app with Computer Use, even if the user's `@Vaiz` mention also resolves to that app.

## Uploading a local file

1. `prepare_file_upload` with `entityType` (`"task"`, `"milestone"`, `"document"`), `entityId` (task HRID or id; 24-char id for others), `fileName` with extension, and optionally `size` in bytes so oversized files are rejected early. The response contains a short-lived upload URL (15 minutes) and a `curlExample`.
2. Send the bytes to that URL — `PUT` with the raw body, or `POST multipart/form-data`. The HTTP response contains the new `fileId`.
3. Attach the `fileId` (next section).

## Importing from a URL

`upload_file_from_url` with `entityType`, `entityId`, and a public `https://` `url`; optional `fileName` overrides the stored name. Private networks and localhost are rejected. Returns a `fileId`. Use this for CI artifacts, screenshots already hosted online, or when you cannot run HTTP uploads yourself.

## Attaching

- `attach_files_to_document` — append up to 20 `fileIds` to the body of the same entity they were uploaded for. Images become Image blocks, videos Video blocks, everything else a Files block. Order is preserved.
- `create_comment` with `fileIds` — attach to a new comment on that entity instead; `content` may be omitted when files are provided.

Files must be attached to the entity they were uploaded for; you cannot upload to one task and attach to another.

## Reading a file

`read_file` with `file` set to the 24-char file id or the file URL seen in `documentContent` or comments. Returns metadata and a short-lived `downloadUrl` (15 minutes, plain GET, no auth headers). With `includeContent: true`:

- images are returned inline, downscaled to 1600px
- text-like files (txt, md, csv, json, code) are returned as UTF-8 text, first 100k characters
- PDFs and other binaries: use `downloadUrl`
- videos are not served

This is a heavy operation. Call it only when the user asks about a specific file, and set `includeContent: false` when metadata is enough.

## Good habits

- Name files descriptively (`login-flow-diagram.png`, not `image1.png`).
- Prefer `upload_file_from_url` when the file already has a public URL.
- Reuse the same `fileId` for both the body and a comment only if it really belongs in both places.

Further reading: https://vaiz.com/help/tutorials/how-to-connect-vaiz-to-mcp · https://vaiz.com
