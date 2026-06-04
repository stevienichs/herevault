[English](README.md) | [中文](README_zh.md)

# MCP 使用指南

### 概述

MCP（模型上下文协议）是一种允许 AI 代理与外部工具和数据源交互的协议。HereVault 实现了一个 MCP 服务器，通过 stdio 传输方式向 AI 代理暴露记忆和知识管理工具。

核心能力：
- 7 个 MCP 工具用于记忆和知识管理
- stdio 传输方式，可与 MCP 客户端无缝集成
- 保存操作后自动后台索引
- 混合搜索：向量 + BM25 + RRF 重排序

### 客户端配置

#### Claude Desktop

添加到你的 `claude_desktop_config.json`：

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

在项目根目录创建 `.cursor/mcp.json`：

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

#### 其他 MCP 客户端

对于任何兼容 MCP 的客户端，使用以下配置：
- **命令**：`herevault`
- **参数**：`["--vault", "/path/to/your/vault"]`
- **传输方式**：stdio

#### MCP 上下文中的环境变量

你可以通过环境变量来控制 HereVault 的行为：

| 变量 | 描述 | 默认值 |
|------|------|--------|
| `HEREVAULT_HTTP_ENABLED` | 启用 HTTP API | `false` |
| `HEREVAULT_ENABLE_RERANKER` | 启用重排序模型 | `true` |
| `HEREVAULT_LOG_LEVEL` | 日志级别 | `info` |
| `HEREVAULT_MODE` | 服务器模式 | `mcp` |

### MCP 工具参考

#### 记忆工具

##### `memory_save`

将记忆保存到 Obsidian 仓库，使用结构化 Markdown 格式。

**参数：**

| 名称 | 类型 | 必填 | 描述 |
|------|------|------|------|
| `type` | string | 是 | 记忆类型：`preference`、`habit`、`fact`、`conversation`、`skill`、`context` |
| `content` | string | 是 | Markdown 格式的记忆内容（不含 frontmatter） |
| `name` | string | 是 | kebab-case 格式的文件名（例如 "my-memory-name"） |
| `tags` | string[] | 否 | 用于分类的标签 |
| `expiresAt` | string | 否 | ISO 8601 格式的过期时间 |

**内容格式指南：**
- 使用清晰的 Markdown 标题（##、###）划分章节
- 添加相关标签以便分类
- 使用要点列表提供详细描述
- 使用代码块展示技术内容
- 不要包含 frontmatter 字段（memoryId、type、tags、时间戳会自动生成）

**示例：**
```json
{
  "type": "preference",
  "name": "user-coding-style",
  "content": "## 用户编码偏好\n\n### 风格\n- 使用 TypeScript\n- 偏好函数式编程\n- 重视代码可读性",
  "tags": ["coding", "preference"]
}
```

##### `memory_recall`

通过语义搜索检索记忆。

**参数：**

| 名称 | 类型 | 必填 | 描述 |
|------|------|------|------|
| `query` | string | 是 | 搜索查询 |
| `type` | string | 否 | 按记忆类型筛选 |
| `tags` | string[] | 否 | 按标签筛选 |
| `limit` | number | 否 | 最大结果数量 |

**示例：**
```json
{
  "query": "用户编码偏好",
  "type": "preference",
  "limit": 5
}
```

##### `memory_forget`

通过 ID 删除指定记忆。

**参数：**

| 名称 | 类型 | 必填 | 描述 |
|------|------|------|------|
| `memoryId` | string | 是 | 要删除的记忆 ID |

**示例：**
```json
{
  "memoryId": "550e8400-e29b-41d4-a716-446655440000"
}
```

#### 知识库工具

##### `knowledge_add`

向知识库添加知识。

**参数：**

| 名称 | 类型 | 必填 | 描述 |
|------|------|------|------|
| `title` | string | 是 | 知识标题 |
| `content` | string | 否* | 知识内容（Markdown 或纯文本） |
| `tags` | string[] | 否 | 用于分类的标签 |
| `sourcePath` | string | 否 | 源文件路径（仅元数据） |
| `overwrite` | boolean | 否 | 是否覆盖已有文件（默认：false） |

*注意：添加新知识时 `content` 为必填项。

**示例：**
```json
{
  "title": "FastAPI 最佳实践",
  "content": "## FastAPI 最佳实践\n\n### 项目结构\n...",
  "tags": ["python", "fastapi", "backend"]
}
```

##### `knowledge_search`

使用语义搜索查询知识库。

**参数：**

| 名称 | 类型 | 必填 | 描述 |
|------|------|------|------|
| `query` | string | 是 | 搜索查询 |
| `tags` | string[] | 否 | 按标签筛选 |
| `limit` | number | 否 | 最大结果数量 |

**示例：**
```json
{
  "query": "FastAPI 项目结构",
  "tags": ["fastapi"],
  "limit": 10
}
```

##### `knowledge_remove`

从知识库中移除知识文档。

**参数：**

| 名称 | 类型 | 必填 | 描述 |
|------|------|------|------|
| `documentId` | string | 是 | 要移除的文档 ID |

**示例：**
```json
{
  "documentId": "550e8400-e29b-41d4-a716-446655440000"
}
```

##### `knowledge_sync`

从源目录同步知识。

**参数：**

| 名称 | 类型 | 必填 | 描述 |
|------|------|------|------|
| `sourceDir` | string | 否 | 指定源目录（必须在已配置的 sourceDirs 中） |

**示例：**
```json
{
  "sourceDir": "Source"
}
```

### MCP 资源（预览）

> **注意**：MCP 资源已实现但当前处于禁用状态，将在未来版本中启用。

计划中的资源：

| URI | 名称 | 描述 |
|-----|------|------|
| `memory://all` | 所有记忆 | 所有记忆的列表 |
| `memory://type/preference` | 偏好记忆 | 偏好类型的记忆 |
| `memory://type/habit` | 习惯记忆 | 习惯类型的记忆 |
| `memory://type/fact` | 事实记忆 | 事实类型的记忆 |
| `vault://status` | 仓库状态 | 当前仓库集成状态 |

### MCP 提示（预览）

> **注意**：MCP 提示已实现但当前处于禁用状态，将在未来版本中启用。

计划中的提示：

| 名称 | 描述 |
|------|------|
| `remember_context` | 检索与用户相关的记忆和上下文 |
| `search_knowledge` | 搜索知识库 |
| `vault_summary` | 获取仓库状态摘要 |
| `structure_memory` | 创建结构化的记忆条目 |

### 最佳实践

#### 记忆类型选择

| 类型 | 使用场景 | 示例 |
|------|----------|------|
| `preference` | 用户偏好和设置 | "偏好暗色主题"、"使用 TypeScript" |
| `habit` | 用户习惯和行为模式 | "合并前总是审查代码" |
| `fact` | 关于用户或项目的重要事实 | "项目使用 React 18"、"团队在 UTC+8 时区" |
| `conversation` | 重要的对话摘要 | "昨天会议讨论了 API 设计" |
| `skill` | 技能和能力 | "能部署到 AWS"、"了解 GraphQL" |
| `context` | 当前上下文和状态 | "正在开发认证功能" |

#### 内容格式建议

- 使用 Markdown 标题构建结构
- 保持内容聚焦且简洁
- 使用要点列表
- 在代码块中包含相关代码片段
- 添加相关资源的链接

#### 标签策略

- 使用一致的小写标签
- 结合宽泛和具体的标签（例如 `["coding", "typescript"]`）
- 避免过多标签（每条记录 3-5 个为佳）

#### 何时使用同步

- 在仓库中手动编辑或删除文件后
- 直接向 Knowledge 目录添加文件后
- 搜索结果似乎过时时
