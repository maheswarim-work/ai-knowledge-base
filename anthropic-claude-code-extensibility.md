# Claude Code Extensibility: Skills, Commands, Agents, MCP

**Source:** Claude Code documentation
**By:** Anthropic

## Overview

Claude Code has four extensibility features for customizing behavior and integrating external tools.

| Feature | Purpose | Invocation | Location |
|---------|---------|------------|----------|
| **Skills** | Domain knowledge | Auto (Claude decides) | `.claude/skills/{name}/SKILL.md` |
| **Commands** | Slash commands | Manual (`/cmd`) | `.claude/commands/{name}.md` |
| **Agents** | Specialized subagents | Delegated | `.claude/agents/{name}.md` |
| **MCP** | External integrations | Auto | `.mcp.json` |

---

## Skills (.skills)

Markdown files that teach Claude specialized knowledge. **Claude automatically discovers and uses them** based on context.

### Structure

```
.claude/skills/{skill-name}/
├── SKILL.md          # Required: main instructions
├── REFERENCE.md      # Optional: additional docs
└── scripts/          # Optional: helper scripts
```

### Format

```yaml
---
name: skill-name
description: When to use this skill
allowed-tools: Read, Grep, Glob
---

# Skill Instructions

Your markdown instructions here.
```

### Example: PR Review Skill

`.claude/skills/pr-review/SKILL.md`:

```yaml
---
name: pr-review
description: Reviews pull requests for code quality and security
allowed-tools: Read, Grep, Bash(git diff:*)
---

# PR Review

1. Run `git diff` to see changes
2. Check for security issues, code quality, missing tests
3. Provide feedback organized by severity
```

---

## Commands (.commands)

Slash commands you invoke explicitly with `/command-name`.

### Structure

```
.claude/commands/
├── commit-msg.md
├── security-audit.md
└── deploy/
    └── staging.md      # Invoked as /deploy/staging
```

### Format

```yaml
---
description: Brief description for /help menu
allowed-tools: Bash(git:*), Read
argument-hint: [file-pattern]
---

# Command prompt

Instructions using $ARGUMENTS for user input.
Use !git status for inline bash.
Use @file.md to reference files.
```

### Example: Commit Message Generator

`.claude/commands/commit-msg.md`:

```yaml
---
description: Generate commit message from staged changes
allowed-tools: Bash(git diff:*), Bash(git status:*)
---

# Generate Commit Message

Staged changes:
!git diff --staged

Write a clear commit message:
- Under 50 chars summary
- Present tense ("Add" not "Added")
- Start with verb: Add, Fix, Refactor
```

**Usage:** `/commit-msg`

---

## Agents (.agents)

Specialized subagents with **isolated context** and custom permissions.

### Structure

```
.claude/agents/
├── code-reviewer.md
├── debugger.md
└── db-analyst.md
```

### Format

```yaml
---
name: agent-name
description: What this agent does
tools: Read, Grep, Glob
model: sonnet
---

# Agent System Prompt

Detailed instructions for this agent.
```

### Example: Debugger Agent

`.claude/agents/debugger.md`:

```yaml
---
name: debugger
description: Debug errors and test failures
tools: Read, Edit, Bash, Grep
model: sonnet
---

# Debugger Agent

1. Capture error message and stack trace
2. Isolate the issue location
3. Form hypothesis about root cause
4. Implement minimal fix
5. Verify with tests
```

---

## MCP (Model Context Protocol)

Connect Claude to external tools and APIs.

### Transport Types

| Type | Use Case |
|------|----------|
| **http** | Remote servers (recommended) |
| **sse** | Server-sent events (deprecated) |
| **stdio** | Local process servers |

### Add Servers via CLI

```bash
# HTTP server
claude mcp add --transport http github https://api.githubcopilot.com/mcp/

# With auth header
claude mcp add --transport http sentry https://mcp.sentry.dev/mcp \
  --header "Authorization: Bearer TOKEN"

# Local stdio server
claude mcp add --transport stdio postgres \
  --env DATABASE_URL="postgresql://user:pass@localhost/db" \
  -- npx -y @bytebase/dbhub
```

### Project Config (.mcp.json)

Checked into git, shared with team:

```json
{
  "mcpServers": {
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/"
    },
    "local-db": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@bytebase/dbhub"],
      "env": {
        "DATABASE_URL": "${DB_URL}"
      }
    }
  }
}
```

### Management

```bash
claude mcp list              # List servers
claude mcp get github        # Server details
claude mcp remove sentry     # Remove server
```

In Claude Code: `/mcp` to manage interactively.

---

## Quick Reference

| What you want | Use |
|--------------|-----|
| Auto-apply domain knowledge | Skills |
| Repeatable manual workflow | Commands |
| Isolated specialized task | Agents |
| External API/tool access | MCP |

### Directory Summary

```
.claude/
├── skills/{name}/SKILL.md    # Auto-discovered knowledge
├── commands/{name}.md        # /slash commands
├── agents/{name}.md          # Subagents
.mcp.json                     # MCP server config
```
