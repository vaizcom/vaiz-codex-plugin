---
name: vaiz-github-links
description: Connect code to work in Vaiz — discover connected GitHub repositories, search branches, pull requests, and commits, and link or unlink them on tasks and Space Memory entries so PR state syncs into Vaiz.
---

# Vaiz GitHub links

Vaiz can attach GitHub branches, pull requests, and commits to a task or to a Space Memory entry. Linked pull requests show up in the Vaiz UI and their state is synced automatically. Linking changes nothing on GitHub; it only records the relationship inside Vaiz.

## Prerequisite

The Vaiz MCP server (`https://api.vaiz.com/mcp`) must be available through the installed Vaiz plugin and authorized with OAuth (open the Vaiz connection and complete OAuth sign-in), and the space must have the GitHub integration configured by a space admin. If `get_github_repositories` returns nothing, the integration is not set up — tell the user instead of retrying.

Vaiz tools can appear under different names depending on how Vaiz is connected: `mcp__Vaiz__get_tasks` for a directly configured MCP server, or `mcp__codex_apps__vaiz_get_tasks` (sometimes with a longer app prefix such as `vaiz_testing_`) when Vaiz is installed as an app. Match tool names case-insensitively and by the tool suffix (`get_tasks`, `query_tasks`, …), not by an exact `Vaiz` prefix. Tools may also be loaded lazily: a Vaiz tool that is not in the current tool list is not missing — load it through tool search (for example `Vaiz get_tasks`) before concluding it is unavailable. Work with Vaiz only through these tools: do not open app.vaiz.com in a browser or drive the Vaiz desktop app with Computer Use, even if the user's `@Vaiz` mention also resolves to that app.

## Flow

1. `get_github_repositories` — repositories connected to the current space. Use the returned full names (`owner/repo`) in the next steps.
2. `search_github` — search a connected repository for branches, pull requests, or commits by name, number, title, or sha. Returns identifying data (PR number, branch name, commit sha).
3. `link_github_entity` — attach the entity to a task (id or HRID) or to a memory entry. Details are verified against GitHub before linking.
4. `get_github_links` — list existing links on a task or memory entry; each has a link `_id`.
5. `unlink_github_entity` — remove a link by its `_id`. Only the Vaiz-side link is removed.

## When to link

- A branch or pull request is opened for a task — link it to the task as soon as the ref exists.
- A commit fixes a recorded Problem or implements a Decision — link it to that memory entry, or pass it in `relatedGitHubEntities` of `create_devlog` (see `vaiz-memories`).
- A pull request is merged — check `get_github_links` on the task so the state is reflected; nothing else is needed.

## When not to link

- Do not link every commit of a long branch; link the branch or the pull request instead.
- Do not link refs from repositories that are not connected to the space; `link_github_entity` will reject them.
- Leave `linkGitHubEntitiesToRelatedTasks` at `false` in `create_devlog` unless the same code link genuinely belongs on every related task.

## Working with the current repository

When you are inside a git checkout, read the current branch and remote (`git rev-parse --abbrev-ref HEAD`, `git remote get-url origin`) to find the `owner/repo` and branch name, then confirm the repository appears in `get_github_repositories` before linking. Never guess a PR number; find it with `search_github`.

Further reading: https://vaiz.com/help/tutorials/how-to-connect-vaiz-to-mcp · https://vaiz.com
