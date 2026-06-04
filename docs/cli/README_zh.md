[English](README.md) | [中文](README_zh.md)

# 命令行参考

HereVault 提供两种类型的 CLI 命令：**服务器命令**和**直接操作命令**。服务器命令用于启动 HereVault 服务，直接操作命令则允许你在不启动服务器的情况下执行记忆和知识库操作。

## 服务器命令

### `herevault serve`

以 MCP、HTTP 或双模式启动 HereVault 服务器。

**参数：**

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `--vault <path>` | string | (必需) | Obsidian Vault 路径 |
| `--config <path>` | string | auto | 配置文件路径 |
| `--no-sync` | flag | false | 跳过启动同步 |
| `--log-level <level>` | string | `info` | 日志级别：debug、info、warn、error |
| `--mode <mode>` | string | `mcp` | 服务器模式：mcp、http、both |
| `--port <number>` | number | `3000` | HTTP 服务器端口 |
| `--reranker <boolean>` | boolean | `true` | 启用/禁用Reranker |
| `--reranker-type <type>` | string | `auto` | Reranker类型：auto、bge、cross-encoder |

**示例：**

```bash
# 以 MCP 模式启动（默认，适用于 Claude 等 AI 代理）
herevault serve --vault /path/to/vault

# 使用自定义配置启动
herevault serve --vault /path/to/vault --config /path/to/config.yml

# 跳过启动同步
herevault serve --vault /path/to/vault --no-sync

# 设置日志级别为 debug
herevault serve --vault /path/to/vault --log-level debug

# 禁用Reranker（跳过重排序模型下载和加载）
herevault serve --vault /path/to/vault --reranker false

# 以 HTTP 模式启动
herevault serve --vault /path/to/vault --mode http --port 3000

# 以双模式启动（MCP + HTTP）
herevault serve --vault /path/to/vault --mode both --port 3000
```

## Vault 初始化命令

### `herevault init`

初始化一个新的 HereVault vault，创建所需目录和配置文件。

**参数：**

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `--vault <path>` | string | (必需) | Obsidian Vault 路径 |
| `--config <path>` | string | auto | 配置文件路径 |
| `--force` | flag | false | 覆盖已有配置文件 |

**示例：**
```bash
herevault init --vault /path/to/vault
```

### `herevault download-models`

下载嵌入模型和Reranker的 ONNX 模型。

**参数：**

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `--vault <path>` | string | (必需) | Obsidian Vault 路径 |
| `--config <path>` | string | auto | 配置文件路径 |
| `--embed-model <name>` | string | `bge-m3-ONNX-int8` | 嵌入模型名称 |
| `--rerank-model <name>` | string | `ms-marco-MiniLM-L6-v2` | 重排序模型名称 |
| `--skip-embed` | flag | false | 跳过嵌入模型下载 |
| `--skip-rerank` | flag | false | 跳过重排序模型下载 |

**示例：**
```bash
# 下载所有模型
herevault download-models --vault /path/to/vault

# 跳过重排序模型
herevault download-models --vault /path/to/vault --skip-rerank

# 指定模型
herevault download-models --vault /path/to/vault \
  --embed-model bge-m3-ONNX-int8 \
  --rerank-model ms-marco-MiniLM-L6-v2
```

## 记忆命令

### `herevault memory-save`

保存一条记忆到 Obsidian Vault。

**参数：**

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `--vault <path>` | string | (必需) | Obsidian Vault 路径 |
| `--config <path>` | string | auto | 配置文件路径 |
| `--type <type>` | string | (必需) | 记忆类型：preference、habit、fact、conversation、skill、context |
| `--content <content>` | string | (必需) | 记忆内容（Markdown 格式） |
| `--name <name>` | string | (必需) | 文件名（kebab-case 格式） |
| `--tags <tags>` | string | - | 逗号分隔的标签 |
| `--expires <date>` | string | - | 过期日期（ISO 8601 格式） |

**示例：**
```bash
herevault memory-save --vault /path/to/vault \
  --type fact \
  --name "my-memory" \
  --content "## Important Note\n\nThis is a test memory." \
  --tags "test,important"
```

### `herevault memory-recall`

通过语义搜索召回记忆。

**参数：**

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `--vault <path>` | string | (必需) | Obsidian Vault 路径 |
| `--config <path>` | string | auto | 配置文件路径 |
| `--query <query>` | string | (必需) | 搜索查询 |
| `--type <type>` | string | - | 按记忆类型过滤 |
| `--tags <tags>` | string | - | 逗号分隔的标签过滤 |
| `--limit <number>` | number | `10` | 最大结果数 |

**示例：**
```bash
herevault memory-recall --vault /path/to/vault \
  --query "important note" \
  --limit 5
```

### `herevault memory-forget`

通过 ID 删除一条记忆。

**参数：**

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `--vault <path>` | string | (必需) | Obsidian Vault 路径 |
| `--config <path>` | string | auto | 配置文件路径 |
| `--id <memoryId>` | string | (必需) | 要删除的记忆 ID |

**示例：**
```bash
herevault memory-forget --vault /path/to/vault \
  --id "550e8400-e29b-41d4-a716-446655440000"
```

## 知识库命令

### `herevault knowledge-add`

向知识库添加知识。

**参数：**

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `--vault <path>` | string | (必需) | Obsidian Vault 路径 |
| `--config <path>` | string | auto | 配置文件路径 |
| `--title <title>` | string | (必需) | 知识标题 |
| `--content <content>` | string | (必需) | 知识内容（Markdown 或纯文本） |
| `--tags <tags>` | string | - | 逗号分隔的标签 |
| `--source <path>` | string | - | 源文件路径（用于元数据） |
| `--overwrite` | flag | false | 存在时覆盖 |

**示例：**
```bash
herevault knowledge-add --vault /path/to/vault \
  --title "API Design Guide" \
  --content "## REST API Design\n\n### Principles\n- Use nouns for resources" \
  --tags "api,design"
```

### `herevault knowledge-search`

从知识库中搜索知识。

**参数：**

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `--vault <path>` | string | (必需) | Obsidian Vault 路径 |
| `--config <path>` | string | auto | 配置文件路径 |
| `--query <query>` | string | (必需) | 搜索查询 |
| `--tags <tags>` | string | - | 逗号分隔的标签过滤 |
| `--limit <number>` | number | `10` | 最大结果数 |

**示例：**
```bash
herevault knowledge-search --vault /path/to/vault \
  --query "REST API" \
  --limit 10
```

### `herevault knowledge-remove`

通过 ID 移除知识库文档。

**参数：**

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `--vault <path>` | string | (必需) | Obsidian Vault 路径 |
| `--config <path>` | string | auto | 配置文件路径 |
| `--id <documentId>` | string | (必需) | 要移除的文档 ID |

**示例：**
```bash
herevault knowledge-remove --vault /path/to/vault \
  --id "660e8400-e29b-41d4-a716-446655440001"
```

### `herevault knowledge-sync`

将源目录中的数据同步到知识库。

**参数：**

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `--vault <path>` | string | (必需) | Obsidian Vault 路径 |
| `--config <path>` | string | auto | 配置文件路径 |
| `--source <dir>` | string | - | 指定要同步的源目录 |

**示例：**
```bash
herevault knowledge-sync --vault /path/to/vault \
  --source "Source"
```

## 同步与搜索命令

### `herevault sync`

执行完整同步：源目录 + 知识库扫描 + 记忆扫描。在手动编辑或删除 vault 中的文件后使用此命令。

**参数：**

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `--vault <path>` | string | (必需) | Obsidian Vault 路径 |
| `--config <path>` | string | auto | 配置文件路径 |
| `--source <dir>` | string | - | 仅同步指定源目录 |

**示例：**
```bash
herevault sync --vault /path/to/vault
```

### `herevault search`

跨记忆和知识库的统一搜索。

**参数：**

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `--vault <path>` | string | (必需) | Obsidian Vault 路径 |
| `--config <path>` | string | auto | 配置文件路径 |
| `--query <query>` | string | (必需) | 搜索查询 |
| `--type <type>` | string | `all` | 文档类型过滤：memory、knowledge、all |
| `--limit <number>` | number | `10` | 最大结果数 |

**示例：**
```bash
herevault search --vault /path/to/vault \
  --query "machine learning" \
  --type all \
  --limit 10
```

## 注意事项

### `sync` 与 `knowledge-sync` 的区别

- `knowledge-sync`：仅将源目录同步到知识库（步骤 1）
- `sync`：执行完整的启动同步：源目录同步 + 知识库目录扫描 + 记忆目录扫描（步骤 1-3）。当你手动修改或删除 vault 中的文件并需要更新向量索引时，请使用 `sync`。

### 直接操作命令

直接操作命令（`memory-save`、`memory-recall`、`memory-forget`、`knowledge-add`、`knowledge-search`、`knowledge-remove`、`knowledge-sync`、`search`）会自行初始化存储和模型上下文。文件保存是同步的，但向量索引在后台异步进行。
