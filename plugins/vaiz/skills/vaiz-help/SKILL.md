---
name: vaiz-help
description: Answer "how does Vaiz work" questions from the built-in knowledge base — list MCP resources, read vaiz-help articles, dictionaries, and space overview data, and tell system documentation apart from user documents.
---

# Vaiz help and MCP resources

The Vaiz MCP server exposes read-only resources alongside its tools: system help articles, dictionaries (enumerations, field definitions), and space overview data. Use them to answer questions about Vaiz itself without guessing.

## Prerequisite

The Vaiz MCP server (`https://api.vaiz.com/mcp`) must be available through the installed Vaiz plugin and authorized with OAuth (open the Vaiz connection and complete OAuth sign-in).

## Two sources of truth

- **System documentation** — Vaiz's own help and reference data. Read with `list_resources` and `read_resource`.
- **User content** — documents your team wrote. Read with `get_document`, `search_space`, `get_task`. `search_space` does not search system documentation.

If the user asks "how do I do X in Vaiz", use resources. If they ask "what did we write about X", use user documents.

## Reading resources

1. `list_resources` — returns every available resource: dictionaries, space data, and the help index. Call it once per session and reuse the names.
2. `read_resource(resourceName)` — reads one resource by name, for example `vaiz-space-overview`. Knowledge base articles use the `vaiz-help-` prefix followed by the article name from the index.

Quote or summarize the article for the user and mention that it comes from the Vaiz help center; do not paraphrase from memory when a resource is available.

## Agent skills as resources

The server also publishes agent skills under `skill://` URIs. `skill://index.json` lists them; each entry points to a `SKILL.md` (for example `skill://vaiz-memories/SKILL.md`). Where a skill exists both in this plugin and on the server, the server copy is the more current one — prefer it if the two differ.

## Good habits

- When a tool call fails with a validation error about an enum or field, check the relevant dictionary resource before retrying.
- For anything not covered by resources, point the user to the public help center and support.

Further reading:

- Help center: https://vaiz.com/help
- Connecting Vaiz to MCP: https://vaiz.com/help/tutorials/how-to-connect-vaiz-to-mcp
- Support: https://vaiz.com/support
- https://vaiz.com
