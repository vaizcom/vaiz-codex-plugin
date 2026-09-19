# Vaiz plugin for ChatGPT and Codex

Official Vaiz plugin for ChatGPT and Codex. It connects the assistant to your Vaiz workspace — tasks, boards, documents, milestones, Space Memory, files, workspace activity, and GitHub links — through the hosted Vaiz MCP server with OAuth.

There is no server code in this repository. The MCP server is hosted at `https://api.vaiz.com/mcp`. No API token belongs in this repository, and none is required to use the plugin.

Looking for another client? See the [Cursor plugin](https://github.com/vaizcom/vaiz-cursor-plugin) or [Claude Code plugin](https://github.com/vaizcom/vaiz-claude-plugin).

## Installation

### ChatGPT desktop app

1. Add this repository as a local marketplace while developing, or install Vaiz from the Plugins Directory after publication.
2. Open the Plugins Directory, select the **Vaiz** source, and install **Vaiz**.
3. Complete the OAuth sign-in when prompted.
4. Start a new chat so the bundled skills and MCP tools are loaded.

For a local checkout, add the repository marketplace with:

```bash
codex plugin marketplace add /absolute/path/to/vaiz-codex-plugin
```

Restart the ChatGPT desktop app after adding the marketplace.

### Codex CLI

Add the marketplace and install the plugin:

```bash
codex plugin marketplace add vaizcom/vaiz-codex-plugin
codex plugin add vaiz@vaiz
```

Then start a new Codex session and complete OAuth authentication when prompted.

## What is included

| Path | Purpose |
| --- | --- |
| `plugins/vaiz/plugin.json` | Portable Agent Plugins manifest for ChatGPT and Codex |
| `plugins/vaiz/mcp.json` | Portable remote MCP definition for `https://api.vaiz.com/mcp` |
| `plugins/vaiz/.codex-plugin/plugin.json` | Codex compatibility manifest and install-surface metadata |
| `plugins/vaiz/.mcp.json` | Compatibility MCP definition |
| `plugins/vaiz/skills/vaiz-memories` | Space Memory, task context, Devlogs, lifecycle, and search |
| `plugins/vaiz/skills/vaiz-tasks` | Tasks and boards |
| `plugins/vaiz/skills/vaiz-projects-milestones` | Projects, boards, history, and milestones |
| `plugins/vaiz/skills/vaiz-documents` | Documents and comments |
| `plugins/vaiz/skills/vaiz-files` | File upload, attachment, and reading |
| `plugins/vaiz/skills/vaiz-workspace` | Session, spaces, members, activity, notifications, and time tracking |
| `plugins/vaiz/skills/vaiz-github-links` | GitHub branches, pull requests, and commits linked to Vaiz work |
| `plugins/vaiz/skills/vaiz-help` | Built-in Vaiz help and MCP resources |

## Development

Validate the compatibility package before publishing:

```bash
python3 /path/to/plugin-creator/scripts/validate_plugin.py plugins/vaiz
```

After changing a locally installed plugin, refresh or reinstall it and start a new conversation so ChatGPT or Codex loads the updated package.

## Links

- [Vaiz MCP setup guide](https://vaiz.com/help/tutorials/how-to-connect-vaiz-to-mcp)
- [Privacy policy](https://vaiz.com/legal/privacy-policy)
- [Support](https://vaiz.com/support)
- [OpenAI plugin packaging guide](https://developers.openai.com/plugins/build/plugins)

## License

MIT — see [LICENSE](LICENSE).
