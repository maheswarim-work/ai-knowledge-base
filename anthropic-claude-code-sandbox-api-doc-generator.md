# Sandbox Project: API Doc Generator

A learning project to understand Claude Code extensibility features (skills, commands, agents, MCP).

## Project Overview

Generate OpenAPI documentation from Express/Fastify route files.

| Feature | Implementation |
|---------|---------------|
| **Skill** | OpenAPI 3.0 conventions and formatting rules |
| **Command** | `/gen-api-docs [path]` - generate docs from routes |
| **Agent** | Example generator - creates request/response samples |
| **MCP** | Filesystem server for reading route files |

---

## Setup

```bash
mkdir api-doc-generator && cd api-doc-generator
git init
npm init -y
```

Create a sample Express app to document:

```bash
mkdir -p src/routes
```

---

## 1. Sample API to Document

`src/routes/users.js`:

```javascript
const express = require('express');
const router = express.Router();

// GET /users - List all users
router.get('/', (req, res) => {
  // Query params: limit, offset, role
  res.json({ users: [] });
});

// GET /users/:id - Get user by ID
router.get('/:id', (req, res) => {
  res.json({ id: req.params.id, name: 'John' });
});

// POST /users - Create user
router.post('/', (req, res) => {
  // Body: { name, email, role }
  res.status(201).json({ id: '123', ...req.body });
});

// PUT /users/:id - Update user
router.put('/:id', (req, res) => {
  res.json({ id: req.params.id, ...req.body });
});

// DELETE /users/:id - Delete user
router.delete('/:id', (req, res) => {
  res.status(204).send();
});

module.exports = router;
```

`src/routes/products.js`:

```javascript
const express = require('express');
const router = express.Router();

// GET /products - List products
router.get('/', (req, res) => {
  // Query: category, minPrice, maxPrice, inStock
  res.json({ products: [] });
});

// GET /products/:id - Get product
router.get('/:id', (req, res) => {
  res.json({ id: req.params.id, name: 'Widget', price: 9.99 });
});

// POST /products - Create product
router.post('/', (req, res) => {
  // Body: { name, price, category, inventory }
  res.status(201).json({ id: '456', ...req.body });
});

module.exports = router;
```

---

## 2. Skill: OpenAPI Conventions

`.claude/skills/openapi/SKILL.md`:

```yaml
---
name: openapi
description: OpenAPI 3.0 specification conventions for API documentation. Use when generating or reviewing API docs.
allowed-tools: Read, Grep, Glob
---

# OpenAPI 3.0 Conventions

## Document Structure

Every OpenAPI doc must have:

```yaml
openapi: 3.0.3
info:
  title: API Name
  version: 1.0.0
  description: Brief description
paths:
  /resource:
    get:
      summary: Short description
      operationId: uniqueOperationName
      tags: [Resource]
      parameters: []
      responses:
        '200':
          description: Success
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Resource'
components:
  schemas: {}
```

## Naming Conventions

- **operationId**: camelCase verb+noun (e.g., `getUsers`, `createProduct`)
- **tags**: PascalCase resource name (e.g., `Users`, `Products`)
- **schema names**: PascalCase singular (e.g., `User`, `Product`)

## HTTP Methods Mapping

| Method | Operation | Success Code |
|--------|-----------|--------------|
| GET (list) | List resources | 200 |
| GET (single) | Get by ID | 200, 404 |
| POST | Create | 201 |
| PUT | Full update | 200 |
| PATCH | Partial update | 200 |
| DELETE | Remove | 204 |

## Parameter Types

- **path**: Required, in URL (e.g., `/users/{id}`)
- **query**: Optional filters (e.g., `?limit=10`)
- **header**: Auth tokens, content-type
- **body**: POST/PUT/PATCH payloads

## Response Format

Always include:
- description
- content type (application/json)
- schema reference or inline schema
- example (when helpful)

## Common Schemas

```yaml
components:
  schemas:
    Error:
      type: object
      properties:
        code:
          type: integer
        message:
          type: string
      required: [code, message]

    PaginatedResponse:
      type: object
      properties:
        data:
          type: array
        total:
          type: integer
        limit:
          type: integer
        offset:
          type: integer
```
```

---

## 3. Command: Generate Docs

`.claude/commands/gen-api-docs.md`:

```yaml
---
description: Generate OpenAPI docs from Express route files
allowed-tools: Read, Glob, Grep, Write
argument-hint: [path]
---

# Generate API Documentation

Scan route files and generate OpenAPI 3.0 documentation.

Target path: $ARGUMENTS (default: src/routes/)

## Process

1. Find all route files in the target path
2. Parse each file for route definitions (router.get, router.post, etc.)
3. Extract:
   - HTTP method
   - Path pattern
   - Route comments for descriptions
   - Parameter hints from comments
4. Generate OpenAPI spec following conventions in the openapi skill
5. Write to `docs/openapi.yaml`

## Output

Create `docs/openapi.yaml` with:
- All discovered endpoints
- Inferred parameters from path patterns
- Placeholder schemas for request/response bodies
- Tags grouped by route file

After generating, summarize:
- Total endpoints documented
- Any routes that need manual review
```

**Usage:** `/gen-api-docs src/routes/`

---

## 4. Agent: Example Generator

`.claude/agents/example-generator.md`:

```yaml
---
name: example-generator
description: Generate realistic request/response examples for API documentation
tools: Read, Write
model: haiku
---

# Example Generator Agent

You generate realistic example data for API documentation.

## Guidelines

1. **Realistic data**: Use plausible names, emails, prices
   - Names: "Jane Smith", "Alex Johnson" (not "Test User")
   - Emails: "jane.smith@example.com"
   - IDs: UUIDs or realistic numeric IDs
   - Prices: Realistic values like 29.99, 149.00

2. **Consistent relationships**:
   - Same user ID in request and response
   - Related data should reference each other

3. **Edge cases**: Include examples for:
   - Minimal required fields
   - Full payload with optional fields
   - Error responses

## Output Format

For each endpoint, generate:

```yaml
example:
  summary: Brief description
  value:
    # realistic example data
```

## Example Output

For `POST /users`:

```yaml
requestBody:
  content:
    application/json:
      examples:
        basic:
          summary: Create user with required fields
          value:
            name: "Jane Smith"
            email: "jane.smith@example.com"
        full:
          summary: Create user with all fields
          value:
            name: "Jane Smith"
            email: "jane.smith@example.com"
            role: "admin"
            department: "Engineering"
responses:
  '201':
    content:
      application/json:
        example:
          id: "usr_a1b2c3d4"
          name: "Jane Smith"
          email: "jane.smith@example.com"
          createdAt: "2024-01-15T10:30:00Z"
```
```

---

## 5. MCP: Filesystem Access

Add filesystem MCP for reading project files:

```bash
claude mcp add --transport stdio filesystem -- npx -y @anthropic-ai/mcp-server-filesystem /path/to/api-doc-generator
```

Or create `.mcp.json`:

```json
{
  "mcpServers": {
    "filesystem": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@anthropic-ai/mcp-server-filesystem", "."],
      "env": {}
    }
  }
}
```

---

## 6. Project Structure

```
api-doc-generator/
├── .claude/
│   ├── skills/
│   │   └── openapi/
│   │       └── SKILL.md
│   ├── commands/
│   │   └── gen-api-docs.md
│   └── agents/
│       └── example-generator.md
├── .mcp.json
├── src/
│   └── routes/
│       ├── users.js
│       └── products.js
├── docs/
│   └── openapi.yaml (generated)
├── package.json
└── README.md
```

---

## 7. Usage Flow

1. **Setup project** with sample routes

2. **Run command** to generate base docs:
   ```
   /gen-api-docs src/routes/
   ```

3. **Claude uses skill** automatically to follow OpenAPI conventions

4. **Agent generates examples** when asked:
   ```
   Add realistic examples to the generated OpenAPI spec
   ```

5. **Review output** in `docs/openapi.yaml`

---

## 8. Testing the Features

### Test Skill (auto-triggered)
Ask: "Review my OpenAPI spec for convention violations"
→ Claude applies openapi skill automatically

### Test Command
Type: `/gen-api-docs src/routes/`
→ Generates documentation

### Test Agent
Ask: "Generate realistic examples for all endpoints"
→ Delegates to example-generator agent

### Test MCP
Ask: "List all route files in the project"
→ Uses filesystem MCP to read directory

---

## Extensions

Once basics work, try adding:

1. **More skills**: JSDoc conventions, markdown formatting
2. **More commands**: `/validate-spec`, `/export-postman`
3. **More agents**: Schema inferrer, error response generator
4. **More MCP**: GitHub Gist publishing, Postman API
