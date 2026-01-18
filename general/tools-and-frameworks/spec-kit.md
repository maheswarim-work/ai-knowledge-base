# spec-kit

**Source:** https://github.com/github/spec-kit
**By:** GitHub

## Overview

Open-source toolkit for Spec-Driven Development (SDD). Treats specifications as executable blueprints rather than guides—specifications define what to build before implementation begins.

## Core Concept

Inverts traditional development: AI coding agents generate implementations directly from comprehensive specifications instead of casual prompts.

## Quick Start

```bash
uvx --from git+https://github.com/github/spec-kit.git specify init --here
```

**Security Note:** Some agents may store credentials, auth tokens, or other private artifacts in the agent folder. Consider adding `.claude/` (or parts of it) to `.gitignore` to prevent accidental credential leakage.

## CLI Commands

### Core Workflow
| Command | Purpose |
|---------|---------|
| `/speckit.constitution` | Define project governing principles |
| `/speckit.specify` | Document requirements and user stories |
| `/speckit.plan` | Create technical implementation strategies |
| `/speckit.tasks` | Generate actionable task lists |
| `/speckit.implement` | Execute all tasks systematically |

### Optional Enhancement
| Command | Purpose | When to Use |
|---------|---------|-------------|
| `/speckit.clarify` | Ask structured questions to de-risk ambiguous areas | Before `/speckit.plan` |
| `/speckit.analyze` | Cross-artifact consistency & alignment report | After `/speckit.tasks`, before `/speckit.implement` |
| `/speckit.checklist` | Validate requirements completeness, clarity, and consistency | After `/speckit.plan` |

## Supported Agents

Works with 15+ AI coding agents including Claude, Gemini, Copilot, Cursor, and Qwen.

## Use Cases

- Greenfield projects
- Exploratory development
- Brownfield projects

## Related

- [Spec-Driven Development Tools](../ai-development/spec-driven-development-tools.md) - Analysis of SDD tools including spec-kit
