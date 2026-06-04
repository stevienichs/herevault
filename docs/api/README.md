[English](README.md) | [中文](README_zh.md)

# RESTful API Reference

When HereVault is running in HTTP mode (`--mode http` or `--mode both`), it exposes a RESTful HTTP API.

## Base Information

- **Base URL**: `http://127.0.0.1:3000/api/v1`
- **Health Check**: `GET /health`
- **Content-Type**: `application/json`

## Common Response Format

**Success Response:**
```json
{
  "success": true,
  "data": { ... }
}
```

**Error Response:**
```json
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "Human-readable error message",
    "details": {}
  }
}
```

## Error Codes

| Code | HTTP Status | Description |
|------|-------------|-------------|
| `MISSING_QUERY` | 400 | Query parameter is required |
| `MISSING_ID` | 400 | ID parameter is required |
| `MISSING_TITLE` | 400 | Title is required |
| `MISSING_CONTENT` | 400 | Content is required |
| `INVALID_TYPE` | 400 | Invalid memory type |
| `INVALID_SOURCE_DIR` | 400 | Source directory not in config |
| `NOT_FOUND` | 404 | Resource not found |
| `FEATURE_DISABLED` | 503 | Feature is disabled in config |
| `INTERNAL_ERROR` | 500 | Internal server error |

## Starting the HTTP Server

```bash
# HTTP mode only
herevault serve --vault /path/to/vault --mode http --port 3000

# Both MCP and HTTP
herevault serve --vault /path/to/vault --mode both --port 3000
```

---

## Health Check

### `GET /health`

Returns the health status of the server.

**Response:**
```json
{
  "status": "ok"
}
```

---

## Status

### `GET /api/v1/status`

Returns the current system status.

**Response:**
```json
{
  "success": true,
  "data": {
    "vault": {
      "path": "/path/to/vault",
      "memoriesDir": "Memories",
      "knowledgeBaseDir": "Knowledge",
      "sourceDirs": ["Source"]
    },
    "features": {
      "useAsKnowledgeBase": true,
      "syncMemories": true
    },
    "mode": "mcp",
    "http": {
      "port": 3000,
      "host": "127.0.0.1"
    },
    "models": {
      "embedderReady": true,
      "rerankerReady": true,
      "enableReranker": true
    },
    "stats": {
      "totalMemories": 50,
      "totalKnowledge": 100
    }
  }
}
```

---

## Memory Endpoints

### `POST /api/v1/memories`

Save a new memory.

**Request Body:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `type` | string | Yes | Memory type: preference, habit, fact, conversation, skill, context |
| `content` | string | Yes | Memory content (Markdown format) |
| `name` | string | Yes | File name (kebab-case) |
| `tags` | string[] | No | Tags for categorization |
| `expiresAt` | string | No | Expiration time (ISO 8601) |

**Request Example:**
```json
{
  "type": "preference",
  "content": "## User Preference\n\nLikes dark theme",
  "name": "user-theme-preference",
  "tags": ["ui", "preference"]
}
```

**Response (201):**
```json
{
  "success": true,
  "data": {
    "memoryId": "550e8400-e29b-41d4-a716-446655440000",
    "type": "preference",
    "tags": ["ui", "preference"],
    "createdAt": "2024-01-15T10:30:00+08:00",
    "updatedAt": "2024-01-15T10:30:00+08:00",
    "expiresAt": null,
    "checksum": "abc123...",
    "filePath": "/path/to/vault/Memories/user-theme-preference.md",
    "status": "saved"
  }
}
```

**Errors:** `INVALID_TYPE` (400), `FEATURE_DISABLED` (503)

---

### `GET /api/v1/memories`

Retrieve memories via semantic search.

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `query` | string | Yes | Search query |
| `type` | string | No | Filter by memory type |
| `tags` | string | No | Comma-separated tags filter |
| `limit` | number | No | Maximum results (default: 10) |

**Request Example:**
```
GET /api/v1/memories?query=user%20preference&type=preference&limit=5
```

**Response (200):**
```json
{
  "success": true,
  "data": {
    "query": "user preference",
    "total": 3,
    "results": [
      {
        "memoryId": "550e8400-e29b-41d4-a716-446655440000",
        "type": "preference",
        "score": 0.95,
        "content": "Likes dark theme",
        "filePath": "Memories/user-theme-preference.md",
        "tags": ["ui", "preference"],
        "createdAt": "2024-01-15T10:30:00+08:00",
        "matchedChunks": [
          {
            "chunkId": "chunk-1",
            "content": "Likes dark theme",
            "score": 0.95
          }
        ]
      }
    ]
  }
}
```

**Errors:** `MISSING_QUERY` (400), `FEATURE_DISABLED` (503)

---

### `DELETE /api/v1/memories/:id`

Delete a memory by its ID.

**Path Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `id` | string | Memory ID |

**Response (200):**
```json
{
  "success": true,
  "data": {
    "memoryId": "550e8400-e29b-41d4-a716-446655440000",
    "status": "deleted",
    "message": "Memory permanently removed from vault and index"
  }
}
```

**Errors:** `NOT_FOUND` (404), `FEATURE_DISABLED` (503)

---

## Knowledge Endpoints

### `POST /api/v1/knowledge`

Add knowledge to the knowledge base.

**Request Body:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `title` | string | Yes | Knowledge title |
| `content` | string | Yes | Knowledge content (Markdown or plain text) |
| `tags` | string[] | No | Tags for categorization |
| `sourcePath` | string | No | Source file path (metadata only) |
| `overwrite` | boolean | No | Overwrite existing file (default: false) |

**Request Example:**
```json
{
  "title": "React Hooks Guide",
  "content": "## React Hooks\n\n### useState\nFor adding state to function components...",
  "tags": ["react", "javascript", "frontend"]
}
```

**Response (201):**
```json
{
  "success": true,
  "data": {
    "documentId": "660e8400-e29b-41d4-a716-446655440001",
    "type": "knowledge",
    "title": "React Hooks Guide",
    "tags": ["react", "javascript", "frontend"],
    "createdAt": "2024-01-15T11:00:00+08:00",
    "updatedAt": "2024-01-15T11:00:00+08:00",
    "checksum": "def456...",
    "filePath": "/path/to/vault/Knowledge/react-hooks-guide.md",
    "status": "saved",
    "note": "The knowledge file has been saved. Indexing is processing in the background and may take a few seconds to complete."
  }
}
```

**Conflict Response (409):**
```json
{
  "success": true,
  "data": {
    "documentId": "existing-id",
    "type": "knowledge",
    "title": "React Hooks Guide",
    "status": "exists",
    "message": "Knowledge already exists. Use overwrite=true to replace."
  }
}
```

**Errors:** `MISSING_TITLE` (400), `MISSING_CONTENT` (400), `FEATURE_DISABLED` (503)

---

### `GET /api/v1/knowledge`

Search knowledge using semantic search.

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `query` | string | Yes | Search query |
| `tags` | string | No | Comma-separated tags filter |
| `limit` | number | No | Maximum results (default: 10) |

**Request Example:**
```
GET /api/v1/knowledge?query=React%20Hooks&tags=react&limit=10
```

**Response (200):**
```json
{
  "success": true,
  "data": {
    "query": "React Hooks",
    "total": 5,
    "results": [
      {
        "documentId": "660e8400-e29b-41d4-a716-446655440001",
        "documentType": "knowledge",
        "title": "React Hooks Guide",
        "score": 0.92,
        "filePath": "Knowledge/react-hooks-guide.md",
        "tags": ["react", "javascript", "frontend"],
        "createdAt": "2024-01-15T11:00:00+08:00",
        "matchedChunks": [
          {
            "chunkId": "chunk-1",
            "headlinePath": "React Hooks > useState",
            "content": "For adding state to function components",
            "score": 0.92
          }
        ]
      }
    ]
  }
}
```

**Errors:** `MISSING_QUERY` (400), `FEATURE_DISABLED` (503)

---

### `DELETE /api/v1/knowledge/:id`

Delete a knowledge document by its ID.

**Path Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `id` | string | Document ID |

**Response (200):**
```json
{
  "success": true,
  "data": {
    "documentId": "660e8400-e29b-41d4-a716-446655440001",
    "status": "deleted",
    "filePath": "Knowledge/react-hooks-guide.md",
    "message": "Knowledge permanently removed from vault and index"
  }
}
```

**Errors:** `NOT_FOUND` (404), `FEATURE_DISABLED` (503)

---

### `POST /api/v1/knowledge/sync`

Synchronize knowledge from source directories.

**Request Body:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `sourceDir` | string | No | Specific source directory (must be in configured sourceDirs) |

**Request Example:**
```json
{
  "sourceDir": "Source"
}
```

**Response (200):**
```json
{
  "success": true,
  "data": {
    "added": 5,
    "updated": 3,
    "skipped": 10,
    "message": "Sync completed successfully"
  }
}
```

**Errors:** `INVALID_SOURCE_DIR` (400), `FEATURE_DISABLED` (503)

---

## Search Endpoint

### `GET /api/v1/search`

Global search across both memories and knowledge.

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `query` | string | Yes | Search query |
| `type` | string | No | Document type filter: memory, knowledge, all (default: all) |
| `limit` | number | No | Maximum results (default: 10) |

**Request Example:**
```
GET /api/v1/search?query=machine%20learning&type=all&limit=10
```

**Response (200):**
```json
{
  "success": true,
  "data": {
    "query": "machine learning",
    "type": "all",
    "total": 8,
    "results": [
      {
        "documentId": "...",
        "documentType": "knowledge",
        "title": "Machine Learning Basics",
        "score": 0.88,
        "filePath": "Knowledge/ml-basics.md",
        "tags": ["ml", "ai"],
        "memoryType": null,
        "createdAt": "2024-01-15T10:00:00+08:00",
        "matchedChunks": [
          {
            "chunkId": "chunk-1",
            "headlinePath": "ML Basics > Introduction",
            "content": "Machine learning is...",
            "score": 0.88
          }
        ]
      }
    ]
  }
}
```

**Errors:** `MISSING_QUERY` (400), `FEATURE_DISABLED` (503, when filtered by a disabled feature)

---

## Data Models

### Memory Object

| Field | Type | Description |
|-------|------|-------------|
| `memoryId` | string | Unique memory identifier |
| `type` | string | Memory type (preference, habit, fact, conversation, skill, context) |
| `tags` | string[] | Tags for categorization |
| `createdAt` | string | Creation timestamp (ISO 8601) |
| `updatedAt` | string | Last update timestamp (ISO 8601) |
| `expiresAt` | string\|null | Expiration timestamp (ISO 8601) |
| `checksum` | string | Content checksum |
| `filePath` | string | File path in vault |
| `status` | string | Operation status |

### Knowledge Object

| Field | Type | Description |
|-------|------|-------------|
| `documentId` | string | Unique document identifier |
| `type` | string | Always "knowledge" |
| `title` | string | Knowledge title |
| `tags` | string[] | Tags for categorization |
| `createdAt` | string | Creation timestamp (ISO 8601) |
| `updatedAt` | string | Last update timestamp (ISO 8601) |
| `checksum` | string | Content checksum |
| `filePath` | string | File path in vault |
| `status` | string | Operation status |

### SearchResult

| Field | Type | Description |
|-------|------|-------------|
| `documentId` | string | Document identifier |
| `documentType` | string | "memory" or "knowledge" |
| `title` | string | Document title |
| `score` | number | Relevance score (0-1) |
| `filePath` | string | File path in vault |
| `tags` | string[] | Tags |
| `memoryType` | string\|null | Memory type (only for memory documents) |
| `createdAt` | string\|null | Creation timestamp |
| `matchedChunks` | MatchedChunk[] | Matched content chunks |

### MatchedChunk

| Field | Type | Description |
|-------|------|-------------|
| `chunkId` | string | Chunk identifier |
| `headlinePath` | string\|undefined | Heading path (e.g., "React Hooks > useState") |
| `content` | string | Chunk content |
| `score` | number | Chunk relevance score (0-1) |
