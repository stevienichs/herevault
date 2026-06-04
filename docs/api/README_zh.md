[English](README.md) | [中文](README_zh.md)

# RESTful API 参考

当 HereVault 以 HTTP 模式（`--mode http` 或 `--mode both`）运行时，它会暴露 RESTful HTTP API。

## 基础信息

- **Base URL**: `http://127.0.0.1:3000/api/v1`
- **健康检查**: `GET /health`
- **Content-Type**: `application/json`

## 通用响应格式

**成功响应：**
```json
{
  "success": true,
  "data": { ... }
}
```

**错误响应：**
```json
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "人类可读的错误信息",
    "details": {}
  }
}
```

## 错误码

| 错误码 | HTTP 状态码 | 说明 |
|--------|-------------|------|
| `MISSING_QUERY` | 400 | 查询参数为必填项 |
| `MISSING_ID` | 400 | ID 参数为必填项 |
| `MISSING_TITLE` | 400 | 标题为必填项 |
| `MISSING_CONTENT` | 400 | 内容为必填项 |
| `INVALID_TYPE` | 400 | 无效的记忆类型 |
| `INVALID_SOURCE_DIR` | 400 | 源目录不在配置中 |
| `NOT_FOUND` | 404 | 资源未找到 |
| `FEATURE_DISABLED` | 503 | 功能在配置中已禁用 |
| `INTERNAL_ERROR` | 500 | 内部服务器错误 |

## 启动 HTTP 服务器

```bash
# 仅 HTTP 模式
herevault serve --vault /path/to/vault --mode http --port 3000

# 同时启用 MCP 和 HTTP
herevault serve --vault /path/to/vault --mode both --port 3000
```

---

## 健康检查

### `GET /health`

返回服务器的健康状态。

**响应：**
```json
{
  "status": "ok"
}
```

---

## 状态

### `GET /api/v1/status`

返回当前系统状态。

**响应：**
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

## 记忆接口

### `POST /api/v1/memories`

保存一条新记忆。

**请求体：**

| 字段 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `type` | string | 是 | 记忆类型：preference、habit、fact、conversation、skill、context |
| `content` | string | 是 | 记忆内容（Markdown 格式） |
| `name` | string | 是 | 文件名（kebab-case 格式） |
| `tags` | string[] | 否 | 分类标签 |
| `expiresAt` | string | 否 | 过期时间（ISO 8601） |

**请求示例：**
```json
{
  "type": "preference",
  "content": "## 用户偏好\n\n喜欢深色主题",
  "name": "user-theme-preference",
  "tags": ["ui", "preference"]
}
```

**响应 (201)：**
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

**错误：** `INVALID_TYPE` (400)、`FEATURE_DISABLED` (503)

---

### `GET /api/v1/memories`

通过语义搜索检索记忆。

**查询参数：**

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `query` | string | 是 | 搜索查询 |
| `type` | string | 否 | 按记忆类型过滤 |
| `tags` | string | 否 | 逗号分隔的标签过滤 |
| `limit` | number | 否 | 最大结果数（默认：10） |

**请求示例：**
```
GET /api/v1/memories?query=用户偏好&type=preference&limit=5
```

**响应 (200)：**
```json
{
  "success": true,
  "data": {
    "query": "用户偏好",
    "total": 3,
    "results": [
      {
        "memoryId": "550e8400-e29b-41d4-a716-446655440000",
        "type": "preference",
        "score": 0.95,
        "content": "喜欢深色主题",
        "filePath": "Memories/user-theme-preference.md",
        "tags": ["ui", "preference"],
        "createdAt": "2024-01-15T10:30:00+08:00",
        "matchedChunks": [
          {
            "chunkId": "chunk-1",
            "content": "喜欢深色主题",
            "score": 0.95
          }
        ]
      }
    ]
  }
}
```

**错误：** `MISSING_QUERY` (400)、`FEATURE_DISABLED` (503)

---

### `DELETE /api/v1/memories/:id`

根据 ID 删除一条记忆。

**路径参数：**

| 参数 | 类型 | 说明 |
|------|------|------|
| `id` | string | 记忆 ID |

**响应 (200)：**
```json
{
  "success": true,
  "data": {
    "memoryId": "550e8400-e29b-41d4-a716-446655440000",
    "status": "deleted",
    "message": "记忆已从仓库和索引中永久删除"
  }
}
```

**错误：** `NOT_FOUND` (404)、`FEATURE_DISABLED` (503)

---

## 知识库接口

### `POST /api/v1/knowledge`

向知识库添加知识。

**请求体：**

| 字段 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `title` | string | 是 | 知识标题 |
| `content` | string | 是 | 知识内容（Markdown 或纯文本） |
| `tags` | string[] | 否 | 分类标签 |
| `sourcePath` | string | 否 | 源文件路径（仅作为元数据） |
| `overwrite` | boolean | 否 | 是否覆盖已有文件（默认：false） |

**请求示例：**
```json
{
  "title": "React Hooks 指南",
  "content": "## React Hooks\n\n### useState\n用于为函数组件添加状态...",
  "tags": ["react", "javascript", "frontend"]
}
```

**响应 (201)：**
```json
{
  "success": true,
  "data": {
    "documentId": "660e8400-e29b-41d4-a716-446655440001",
    "type": "knowledge",
    "title": "React Hooks 指南",
    "tags": ["react", "javascript", "frontend"],
    "createdAt": "2024-01-15T11:00:00+08:00",
    "updatedAt": "2024-01-15T11:00:00+08:00",
    "checksum": "def456...",
    "filePath": "/path/to/vault/Knowledge/react-hooks-guide.md",
    "status": "saved",
    "note": "知识文件已保存。索引正在后台处理，可能需要几秒钟才能完成。"
  }
}
```

**冲突响应 (409)：**
```json
{
  "success": true,
  "data": {
    "documentId": "existing-id",
    "type": "knowledge",
    "title": "React Hooks 指南",
    "status": "exists",
    "message": "知识已存在。使用 overwrite=true 进行替换。"
  }
}
```

**错误：** `MISSING_TITLE` (400)、`MISSING_CONTENT` (400)、`FEATURE_DISABLED` (503)

---

### `GET /api/v1/knowledge`

使用语义搜索查询知识。

**查询参数：**

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `query` | string | 是 | 搜索查询 |
| `tags` | string | 否 | 逗号分隔的标签过滤 |
| `limit` | number | 否 | 最大结果数（默认：10） |

**请求示例：**
```
GET /api/v1/knowledge?query=React%20Hooks&tags=react&limit=10
```

**响应 (200)：**
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
        "title": "React Hooks 指南",
        "score": 0.92,
        "filePath": "Knowledge/react-hooks-guide.md",
        "tags": ["react", "javascript", "frontend"],
        "createdAt": "2024-01-15T11:00:00+08:00",
        "matchedChunks": [
          {
            "chunkId": "chunk-1",
            "headlinePath": "React Hooks > useState",
            "content": "用于为函数组件添加状态",
            "score": 0.92
          }
        ]
      }
    ]
  }
}
```

**错误：** `MISSING_QUERY` (400)、`FEATURE_DISABLED` (503)

---

### `DELETE /api/v1/knowledge/:id`

根据 ID 删除一篇知识文档。

**路径参数：**

| 参数 | 类型 | 说明 |
|------|------|------|
| `id` | string | 文档 ID |

**响应 (200)：**
```json
{
  "success": true,
  "data": {
    "documentId": "660e8400-e29b-41d4-a716-446655440001",
    "status": "deleted",
    "filePath": "Knowledge/react-hooks-guide.md",
    "message": "知识已从仓库和索引中永久删除"
  }
}
```

**错误：** `NOT_FOUND` (404)、`FEATURE_DISABLED` (503)

---

### `POST /api/v1/knowledge/sync`

从源目录同步知识。

**请求体：**

| 字段 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `sourceDir` | string | 否 | 指定源目录（必须在已配置的 sourceDirs 中） |

**请求示例：**
```json
{
  "sourceDir": "Source"
}
```

**响应 (200)：**
```json
{
  "success": true,
  "data": {
    "added": 5,
    "updated": 3,
    "skipped": 10,
    "message": "同步成功完成"
  }
}
```

**错误：** `INVALID_SOURCE_DIR` (400)、`FEATURE_DISABLED` (503)

---

## 搜索接口

### `GET /api/v1/search`

跨记忆和知识的全局搜索。

**查询参数：**

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `query` | string | 是 | 搜索查询 |
| `type` | string | 否 | 文档类型过滤：memory、knowledge、all（默认：all） |
| `limit` | number | 否 | 最大结果数（默认：10） |

**请求示例：**
```
GET /api/v1/search?query=机器学习&type=all&limit=10
```

**响应 (200)：**
```json
{
  "success": true,
  "data": {
    "query": "机器学习",
    "type": "all",
    "total": 8,
    "results": [
      {
        "documentId": "...",
        "documentType": "knowledge",
        "title": "机器学习基础",
        "score": 0.88,
        "filePath": "Knowledge/ml-basics.md",
        "tags": ["ml", "ai"],
        "memoryType": null,
        "createdAt": "2024-01-15T10:00:00+08:00",
        "matchedChunks": [
          {
            "chunkId": "chunk-1",
            "headlinePath": "机器学习基础 > 简介",
            "content": "机器学习是...",
            "score": 0.88
          }
        ]
      }
    ]
  }
}
```

**错误：** `MISSING_QUERY` (400)、`FEATURE_DISABLED` (503，当按已禁用的功能过滤时)

---

## 数据模型

### 记忆对象

| 字段 | 类型 | 说明 |
|------|------|------|
| `memoryId` | string | 唯一记忆标识符 |
| `type` | string | 记忆类型（preference、habit、fact、conversation、skill、context） |
| `tags` | string[] | 分类标签 |
| `createdAt` | string | 创建时间戳（ISO 8601） |
| `updatedAt` | string | 最后更新时间戳（ISO 8601） |
| `expiresAt` | string\|null | 过期时间戳（ISO 8601） |
| `checksum` | string | 内容校验和 |
| `filePath` | string | 仓库中的文件路径 |
| `status` | string | 操作状态 |

### 知识对象

| 字段 | 类型 | 说明 |
|------|------|------|
| `documentId` | string | 唯一文档标识符 |
| `type` | string | 始终为 "knowledge" |
| `title` | string | 知识标题 |
| `tags` | string[] | 分类标签 |
| `createdAt` | string | 创建时间戳（ISO 8601） |
| `updatedAt` | string | 最后更新时间戳（ISO 8601） |
| `checksum` | string | 内容校验和 |
| `filePath` | string | 仓库中的文件路径 |
| `status` | string | 操作状态 |

### 搜索结果

| 字段 | 类型 | 说明 |
|------|------|------|
| `documentId` | string | 文档标识符 |
| `documentType` | string | "memory" 或 "knowledge" |
| `title` | string | 文档标题 |
| `score` | number | 相关性分数（0-1） |
| `filePath` | string | 仓库中的文件路径 |
| `tags` | string[] | 标签 |
| `memoryType` | string\|null | 记忆类型（仅对记忆文档有效） |
| `createdAt` | string\|null | 创建时间戳 |
| `matchedChunks` | MatchedChunk[] | 匹配的内容片段 |

### 匹配片段

| 字段 | 类型 | 说明 |
|------|------|------|
| `chunkId` | string | 片段标识符 |
| `headlinePath` | string\|undefined | 标题路径（如 "React Hooks > useState"） |
| `content` | string | 片段内容 |
| `score` | number | 片段相关性分数（0-1） |
