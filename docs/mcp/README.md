[English](README.md) | [中文](README_zh.md)

# MCP Usage Guide

### Overview

MCP (Model Context Protocol) is a protocol that allows AI agents to interact with external tools and data sources. HereVault implements an MCP server that exposes memory and knowledge management tools to AI agents via the stdio transport.

Key capabilities:
- 7 MCP tools for memory and knowledge management
- stdio transport for seamless integration with MCP clients
- Automatic background indexing after save operations
- Hybrid search with vector + BM25 + RRF reranking

### Client Configuration

#### Claude Desktop

Add to your `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "herevault": {
      "command": "herevault",
      "args": ["--vault", "/path/to/your/vault"],
      "env": {
        "HEREVAULT_HTTP_ENABLED": "false",
        "HEREVAULT_ENABLE_RERANKER": "true"
      }
    }
  }
}
```

#### Cursor

Create `.cursor/mcp.json` in your project root:

```json
{
  "mcpServers": {
    "herevault": {
      "command": "herevault",
      "args": ["--vault", "/path/to/your/vault"]
    }
  }
}
```

#### Other MCP Clients

For any MCP-compatible client, configure the server with:
- **Command**: `herevault`
- **Args**: `["--vault", "/path/to/your/vault"]`
- **Transport**: stdio

#### Environment Variables in MCP Context

You can pass environment variables to control HereVault behavior:

| Variable | Description | Default |
|----------|-------------|---------|
| `HEREVAULT_HTTP_ENABLED` | Enable HTTP API | `false` |
| `HEREVAULT_ENABLE_RERANKER` | Enable reranker model | `true` |
| `HEREVAULT_LOG_LEVEL` | Log level | `info` |
| `HEREVAULT_MODE` | Server mode | `mcp` |

### MCP Tools Reference

#### Memory Tools

##### `memory_save`

Save a memory to the Obsidian Vault with structured Markdown format.

**Parameters:**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `type` | string | Yes | Memory type: `preference`, `habit`, `fact`, `conversation`, `skill`, `context` |
| `content` | string | Yes | Memory content in Markdown format (without frontmatter) |
| `name` | string | Yes | File name in kebab-case (e.g., "my-memory-name") |
| `tags` | string[] | No | Tags for categorization |
| `expiresAt` | string | No | Expiration time in ISO 8601 format |

**Content Format Guidelines:**
- Use clear Markdown headings (##, ###) for sections
- Add relevant tags for categorization
- Provide detailed description with bullet points
- Use code blocks for technical content
- Do NOT include frontmatter fields (memoryId, type, tags, timestamps are auto-generated)

**Example:**
```json
{
  "type": "preference",
  "name": "user-coding-style",
  "content": "## User Coding Preferences\n\n### Style\n- Uses TypeScript\n- Prefers functional programming\n- Values code readability",
  "tags": ["coding", "preference"]
}
```

##### `memory_recall`

Retrieve memories via semantic search.

**Parameters:**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `query` | string | Yes | Search query |
| `type` | string | No | Filter by memory type |
| `tags` | string[] | No | Filter by tags |
| `limit` | number | No | Maximum number of results |

**Example:**
```json
{
  "query": "user coding preferences",
  "type": "preference",
  "limit": 5
}
```

##### `memory_forget`

Delete a specific memory by its ID.

**Parameters:**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `memoryId` | string | Yes | The memory ID to delete |

**Example:**
```json
{
  "memoryId": "550e8400-e29b-41d4-a716-446655440000"
}
```

#### Knowledge Tools

##### `knowledge_add`

Add knowledge to the knowledge base.

**Parameters:**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `title` | string | Yes | Knowledge title |
| `content` | string | No* | Knowledge content (Markdown or plain text) |
| `tags` | string[] | No | Tags for categorization |
| `sourcePath` | string | No | Source file path (metadata only) |
| `overwrite` | boolean | No | Whether to overwrite existing file (default: false) |

*Note: `content` is required when adding new knowledge.

**Example:**
```json
{
  "title": "FastAPI Best Practices",
  "content": "## FastAPI Best Practices\n\n### Project Structure\n...",
  "tags": ["python", "fastapi", "backend"]
}
```

##### `knowledge_search`

Search the knowledge base using semantic search.

**Parameters:**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `query` | string | Yes | Search query |
| `tags` | string[] | No | Filter by tags |
| `limit` | number | No | Maximum number of results |

**Example:**
```json
{
  "query": "FastAPI project structure",
  "tags": ["fastapi"],
  "limit": 10
}
```

##### `knowledge_remove`

Remove a knowledge document from the knowledge base.

**Parameters:**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `documentId` | string | Yes | Document ID to remove |

**Example:**
```json
{
  "documentId": "550e8400-e29b-41d4-a716-446655440000"
}
```

##### `knowledge_sync`

Synchronize knowledge from source directories.

**Parameters:**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `sourceDir` | string | No | Specific source directory (must be in configured sourceDirs) |

**Example:**
```json
{
  "sourceDir": "Source"
}
```

### MCP Resources (Preview)

> **Note**: MCP Resources are implemented but currently disabled. They will be enabled in a future release.

Planned resources:

| URI | Name | Description |
|-----|------|-------------|
| `memory://all` | All Memories | List of all memories |
| `memory://type/preference` | Preference Memories | Preference-type memories |
| `memory://type/habit` | Habit Memories | Habit-type memories |
| `memory://type/fact` | Fact Memories | Fact-type memories |
| `vault://status` | Vault Status | Current vault integration status |

### MCP Prompts (Preview)

> **Note**: MCP Prompts are implemented but currently disabled. They will be enabled in a future release.

Planned prompts:

| Name | Description |
|------|-------------|
| `remember_context` | Retrieve relevant memories and context for the user |
| `search_knowledge` | Search the knowledge base |
| `vault_summary` | Get a summary of the vault status |
| `structure_memory` | Create a structured memory entry |

### Best Practices

#### Memory Type Selection

| Type | Use Case | Example |
|------|----------|---------|
| `preference` | User preferences and settings | "Prefers dark theme", "Uses TypeScript" |
| `habit` | User habits and patterns | "Always reviews code before merging" |
| `fact` | Important facts about the user or project | "Project uses React 18", "Team is in UTC+8" |
| `conversation` | Important conversation summaries | "Discussed API design in yesterday's meeting" |
| `skill` | Skills and capabilities | "Can deploy to AWS", "Knows GraphQL" |
| `context` | Current context and state | "Working on authentication feature" |

#### Content Format Recommendations

- Use Markdown headings for structure
- Keep content focused and concise
- Use bullet points for lists
- Include relevant code snippets in code blocks
- Add links to related resources

#### Tag Strategy

- Use consistent, lowercase tags
- Combine broad and specific tags (e.g., `["coding", "typescript"]`)
- Avoid too many tags (3-5 per entry is ideal)

#### When to Use Sync

- After manually editing or deleting files in the vault
- After adding files directly to the Knowledge directory
- When search results seem outdated
