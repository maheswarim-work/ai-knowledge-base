# GitHub MCP Server for Claude

**Source:** https://github.com/github/github-mcp-server
**Installation Guide:** https://github.com/github/github-mcp-server/blob/main/docs/installation-guides/install-claude.md

## Overview

Official GitHub MCP server for Claude Code and Claude Desktop. Provides GitHub API access through Model Context Protocol.

## Prerequisites

- GitHub Personal Access Token (PAT) with `repo` scope
- Docker (for local setup) or internet access (for remote)

---

## Claude Code CLI

### Remote Server (Recommended)

```bash
claude mcp add-json github '{"type":"http","url":"https://api.githubcopilot.com/mcp","headers":{"Authorization":"Bearer YOUR_GITHUB_PAT"}}'
```

**Scope options:**
- `local` (default) — personal, project-specific
- `project` — shared via `.mcp.json`
- `user` — available across all projects

### Local Server (Docker)

```bash
claude mcp add github -e GITHUB_PERSONAL_ACCESS_TOKEN=YOUR_GITHUB_PAT -- \
  docker run -i --rm -e GITHUB_PERSONAL_ACCESS_TOKEN ghcr.io/github/github-mcp-server
```

### Binary (No Docker)

1. Download from [releases](https://github.com/github/github-mcp-server/releases)
2. Add to PATH
3. Configure:

```bash
claude mcp add-json github '{"command":"github-mcp-server","args":["stdio"],"env":{"GITHUB_PERSONAL_ACCESS_TOKEN":"YOUR_GITHUB_PAT"}}'
```

### Verify

```bash
claude mcp list
```

---

## Claude Desktop

### Config file locations

| OS | Path |
|----|------|
| macOS | `~/Library/Application Support/Claude/claude_desktop_config.json` |
| Windows | `%APPDATA%\Claude\claude_desktop_config.json` |
| Linux | `~/.config/Claude/claude_desktop_config.json` |

### Docker config

```json
{
  "mcpServers": {
    "github": {
      "command": "docker",
      "args": ["run", "-i", "--rm", "-e", "GITHUB_PERSONAL_ACCESS_TOKEN",
               "ghcr.io/github/github-mcp-server"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "YOUR_GITHUB_PAT"
      }
    }
  }
}
```

Restart Claude Desktop after editing.

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Auth failure | Verify PAT has `repo` scope, check expiry |
| Server not starting | Run `claude mcp list`, validate JSON syntax |
| Docker issues | Ensure Docker running, `docker pull ghcr.io/github/github-mcp-server` |

Remove and reconfigure:
```bash
claude mcp remove github
```

---

## Notes

- The npm package `@modelcontextprotocol/server-github` is deprecated (April 2025)
- Keep `.env` and `.mcp.json` in `.gitignore`
