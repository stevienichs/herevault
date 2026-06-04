[English](README.md) | [中文](README_zh.md)

# CLI Reference

HereVault provides two types of CLI commands: **server commands** and **direct operation commands**. Server commands start the HereVault service, while direct operation commands allow you to perform memory and knowledge operations without starting the server.

## Server Commands

### `herevault serve`

Start the HereVault server in MCP, HTTP, or dual mode.

**Parameters:**

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `--vault <path>` | string | (required) | Obsidian Vault path |
| `--config <path>` | string | auto | Config file path |
| `--no-sync` | flag | false | Skip startup sync |
| `--log-level <level>` | string | `info` | Log level: debug, info, warn, error |
| `--mode <mode>` | string | `mcp` | Server mode: mcp, http, both |
| `--port <number>` | number | `3000` | HTTP server port |
| `--reranker <boolean>` | boolean | `true` | Enable/disable reranker |
| `--reranker-type <type>` | string | `auto` | Reranker type: auto, bge, cross-encoder |

**Examples:**

```bash
# Start in MCP mode (default, for AI agents like Claude)
herevault serve --vault /path/to/vault

# Start with custom config
herevault serve --vault /path/to/vault --config /path/to/config.yml

# Skip startup sync
herevault serve --vault /path/to/vault --no-sync

# Set log level to debug
herevault serve --vault /path/to/vault --log-level debug

# Disable reranker (skip reranker model download and loading)
herevault serve --vault /path/to/vault --reranker false

# Start in HTTP mode
herevault serve --vault /path/to/vault --mode http --port 3000

# Start in dual mode (MCP + HTTP)
herevault serve --vault /path/to/vault --mode both --port 3000
```

## Vault Setup Commands

### `herevault init`

Initialize a new HereVault vault with required directories and config file.

**Parameters:**

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `--vault <path>` | string | (required) | Obsidian Vault path |
| `--config <path>` | string | auto | Config file path |
| `--force` | flag | false | Overwrite existing config file |

**Example:**
```bash
herevault init --vault /path/to/vault
```

### `herevault download-models`

Download embedding and reranker ONNX models.

**Parameters:**

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `--vault <path>` | string | (required) | Obsidian Vault path |
| `--config <path>` | string | auto | Config file path |
| `--embed-model <name>` | string | `bge-m3-ONNX-int8` | Embedding model name |
| `--rerank-model <name>` | string | `ms-marco-MiniLM-L6-v2` | Reranker model name |
| `--skip-embed` | flag | false | Skip embedding model download |
| `--skip-rerank` | flag | false | Skip reranker model download |

**Example:**
```bash
# Download all models
herevault download-models --vault /path/to/vault

# Skip reranker model
herevault download-models --vault /path/to/vault --skip-rerank

# Specify models
herevault download-models --vault /path/to/vault \
  --embed-model bge-m3-ONNX-int8 \
  --rerank-model ms-marco-MiniLM-L6-v2
```

## Memory Commands

### `herevault memory-save`

Save a memory to the Obsidian Vault.

**Parameters:**

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `--vault <path>` | string | (required) | Obsidian Vault path |
| `--config <path>` | string | auto | Config file path |
| `--type <type>` | string | (required) | Memory type: preference, habit, fact, conversation, skill, context |
| `--content <content>` | string | (required) | Memory content (Markdown format) |
| `--name <name>` | string | (required) | File name (kebab-case) |
| `--tags <tags>` | string | - | Comma-separated tags |
| `--expires <date>` | string | - | Expiration date (ISO 8601) |

**Example:**
```bash
herevault memory-save --vault /path/to/vault \
  --type fact \
  --name "my-memory" \
  --content "## Important Note\n\nThis is a test memory." \
  --tags "test,important"
```

### `herevault memory-recall`

Recall memories by semantic search.

**Parameters:**

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `--vault <path>` | string | (required) | Obsidian Vault path |
| `--config <path>` | string | auto | Config file path |
| `--query <query>` | string | (required) | Search query |
| `--type <type>` | string | - | Filter by memory type |
| `--tags <tags>` | string | - | Comma-separated tags filter |
| `--limit <number>` | number | `10` | Maximum results |

**Example:**
```bash
herevault memory-recall --vault /path/to/vault \
  --query "important note" \
  --limit 5
```

### `herevault memory-forget`

Delete a memory by its ID.

**Parameters:**

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `--vault <path>` | string | (required) | Obsidian Vault path |
| `--config <path>` | string | auto | Config file path |
| `--id <memoryId>` | string | (required) | Memory ID to delete |

**Example:**
```bash
herevault memory-forget --vault /path/to/vault \
  --id "550e8400-e29b-41d4-a716-446655440000"
```

## Knowledge Commands

### `herevault knowledge-add`

Add knowledge to the knowledge base.

**Parameters:**

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `--vault <path>` | string | (required) | Obsidian Vault path |
| `--config <path>` | string | auto | Config file path |
| `--title <title>` | string | (required) | Knowledge title |
| `--content <content>` | string | (required) | Knowledge content (Markdown or plain text) |
| `--tags <tags>` | string | - | Comma-separated tags |
| `--source <path>` | string | - | Source file path for metadata |
| `--overwrite` | flag | false | Overwrite if exists |

**Example:**
```bash
herevault knowledge-add --vault /path/to/vault \
  --title "API Design Guide" \
  --content "## REST API Design\n\n### Principles\n- Use nouns for resources" \
  --tags "api,design"
```

### `herevault knowledge-search`

Search knowledge from the knowledge base.

**Parameters:**

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `--vault <path>` | string | (required) | Obsidian Vault path |
| `--config <path>` | string | auto | Config file path |
| `--query <query>` | string | (required) | Search query |
| `--tags <tags>` | string | - | Comma-separated tags filter |
| `--limit <number>` | number | `10` | Maximum results |

**Example:**
```bash
herevault knowledge-search --vault /path/to/vault \
  --query "REST API" \
  --limit 10
```

### `herevault knowledge-remove`

Remove a knowledge document by its ID.

**Parameters:**

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `--vault <path>` | string | (required) | Obsidian Vault path |
| `--config <path>` | string | auto | Config file path |
| `--id <documentId>` | string | (required) | Document ID to remove |

**Example:**
```bash
herevault knowledge-remove --vault /path/to/vault \
  --id "660e8400-e29b-41d4-a716-446655440001"
```

### `herevault knowledge-sync`

Synchronize data from source directories to the knowledge base.

**Parameters:**

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `--vault <path>` | string | (required) | Obsidian Vault path |
| `--config <path>` | string | auto | Config file path |
| `--source <dir>` | string | - | Specific source directory to sync |

**Example:**
```bash
herevault knowledge-sync --vault /path/to/vault \
  --source "Source"
```

## Sync & Search Commands

### `herevault sync`

Perform a full sync: source directories + knowledge scan + memory scan. Use this after manually editing or deleting files in the vault.

**Parameters:**

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `--vault <path>` | string | (required) | Obsidian Vault path |
| `--config <path>` | string | auto | Config file path |
| `--source <dir>` | string | - | Sync only specific source directory |

**Example:**
```bash
herevault sync --vault /path/to/vault
```

### `herevault search`

Unified search across memories and knowledge.

**Parameters:**

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `--vault <path>` | string | (required) | Obsidian Vault path |
| `--config <path>` | string | auto | Config file path |
| `--query <query>` | string | (required) | Search query |
| `--type <type>` | string | `all` | Document type filter: memory, knowledge, all |
| `--limit <number>` | number | `10` | Maximum results |

**Example:**
```bash
herevault search --vault /path/to/vault \
  --query "machine learning" \
  --type all \
  --limit 10
```

## Notes

### `sync` vs `knowledge-sync`

- `knowledge-sync`: Only syncs from source directories to the knowledge base (Step 1)
- `sync`: Performs the complete startup sync: source sync + knowledge directory scan + memory directory scan (Steps 1-3). Use `sync` when you manually modify or delete files in the vault and need to update the vector index.

### Direct Operation Commands

Direct operation commands (`memory-save`, `memory-recall`, `memory-forget`, `knowledge-add`, `knowledge-search`, `knowledge-remove`, `knowledge-sync`, `search`) initialize their own storage and model context. File saves are synchronous, but vector indexing happens asynchronously in the background.
