# Sandbox Project: API Spec Enforcer

Learn Claude Code extensibility features using an existing project.

**Project:** https://github.com/maheswarim-work/api-spec-enforcer

## Project Overview

A spec-driven compliance tool that validates FastAPI implementations against OpenAPI specifications, identifies gaps, and generates code/tests to close them.

## Tech Stack

- Python
- FastAPI
- pytest
- Claude (multi-agent workflows)
- MCP (context management)

---

## Extensibility Mapping

| Feature | Implementation |
|---------|---------------|
| **Skill** | OpenAPI compliance rules, FastAPI conventions |
| **Command** | `/enforce [spec-path]` — run compliance check |
| **Agent** | Gap fixer, test generator, spec validator |
| **MCP** | Selective context loading for spec + service files |

---

## 1. Skill: OpenAPI Compliance

**Location:** `.claude/skills/openapi-compliance/SKILL.md`

**Purpose:** Teach Claude the rules for validating API implementations against OpenAPI specs.

**Content should cover:**
- OpenAPI 3.0 structure requirements
- How to match routes to spec paths
- Parameter validation rules (path, query, body)
- Response schema validation
- Status code conventions
- Gap identification criteria

**Auto-triggered when:** Claude encounters OpenAPI specs or compliance-related questions.

---

## 2. Command: Enforce Spec

**Location:** `.claude/commands/enforce.md`

**Purpose:** Run compliance check from Claude Code CLI.

**Usage:** `/enforce spec/openapi.yaml`

**Should:**
1. Load the OpenAPI spec
2. Scan service files for implemented routes
3. Compare and identify gaps
4. Report coverage percentage
5. List missing/incomplete endpoints

**Arguments:**
- `$ARGUMENTS` — path to OpenAPI spec (default: `spec/openapi.yaml`)

---

## 3. Agents

### Gap Fixer Agent

**Location:** `.claude/agents/gap-fixer.md`

**Purpose:** Generate placeholder implementations for missing endpoints.

**Should:**
- Read the spec for endpoint requirements
- Generate FastAPI route stubs
- Include proper type hints and Pydantic models
- Preserve existing code

### Test Generator Agent

**Location:** `.claude/agents/test-generator.md`

**Purpose:** Generate pytest tests for all specified endpoints.

**Should:**
- Create tests for each endpoint in spec
- Cover success and error cases
- Use FastAPI TestClient
- Match expected response schemas

### Spec Validator Agent

**Location:** `.claude/agents/spec-validator.md`

**Purpose:** Validate OpenAPI spec for completeness and correctness.

**Should:**
- Check for missing descriptions
- Validate schema references
- Identify inconsistencies
- Suggest improvements

---

## 4. MCP: Context Management

**Location:** `.mcp.json`

**Purpose:** Provide selective context loading for efficiency.

**Servers to consider:**
- Filesystem — read spec and service files
- GitHub — fetch remote specs or compare branches

**Benefits:**
- ~90% token reduction
- Only relevant files loaded
- Faster agent execution

---

## 5. Project Structure with Extensibility

```
api-spec-enforcer/
├── .claude/
│   ├── skills/
│   │   └── openapi-compliance/
│   │       └── SKILL.md
│   ├── commands/
│   │   ├── enforce.md
│   │   ├── gen-tests.md
│   │   └── fix-gaps.md
│   └── agents/
│       ├── gap-fixer.md
│       ├── test-generator.md
│       └── spec-validator.md
├── .mcp.json
├── spec/
│   └── openapi.yaml
├── services/
│   └── (FastAPI implementation)
├── core/
│   └── (parsing, inspection logic)
├── agents/
│   └── (existing agent code)
└── tests/
```

---

## 6. Usage Flow

1. **Check compliance:**
   ```
   /enforce spec/openapi.yaml
   ```

2. **Fix gaps** (Claude delegates to gap-fixer agent):
   ```
   Fix the missing endpoints identified above
   ```

3. **Generate tests:**
   ```
   /gen-tests spec/openapi.yaml
   ```

4. **Validate spec:**
   ```
   Review the OpenAPI spec for issues
   ```
   → Claude auto-applies openapi-compliance skill

---

## 7. Testing the Features

| Feature | How to test |
|---------|-------------|
| **Skill** | Ask "What's wrong with this OpenAPI spec?" |
| **Command** | Type `/enforce` |
| **Agent** | Ask "Generate tests for all endpoints" |
| **MCP** | Ask "List files in the spec/ directory" |

---

## 8. Extensions

Once basics work:

1. **More commands:** `/diff-spec` — compare spec versions
2. **More agents:** Breaking change detector
3. **More skills:** REST best practices, security patterns
4. **More MCP:** Postman sync, CI/CD integration
