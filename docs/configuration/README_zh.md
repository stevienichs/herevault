[English](README.md) | [中文](README_zh.md)

# 配置参考

### 配置文件

配置文件位于 `<vault>/.herevault/config.yml`，首次启动时自动生成并使用默认值。文件使用 YAML 格式。

### 配置优先级

配置值按以下优先级合并（从高到低）：

1. **命令行参数**：`--reranker false`
2. **环境变量**：`HEREVAULT_ENABLE_RERANKER=false`
3. **配置文件**：`model.enableReranker: false`
4. **默认值**：`true`

### 完整配置参考

#### `vault` - Vault 设置

| 字段                 | 类型        | 默认值           | 环境变量                           | 说明                   |
| ------------------ | --------- | ------------- | ------------------------------ | -------------------- |
| `path`             | string    | `""`          | `HEREVAULT_VAULT_PATH`         | Obsidian Vault 的绝对路径 |
| `memoriesDir`      | string    | `"Memories"`  | `HEREVAULT_MEMORIES_DIR`       | 记忆存储的目录名称            |
| `knowledgeBaseDir` | string    | `"Knowledge"` | `HEREVAULT_KNOWLEDGE_BASE_DIR` | 知识库的目录名称             |
| `sourceDirs`       | string\[] | `["Source"]`  | `HEREVAULT_SOURCE_DIRS`        | 源目录名称列表              |

#### `obsidian` - Obsidian 集成

| 字段                     | 类型      | 默认值           | 环境变量                               | 说明                                      |
| ---------------------- | ------- | ------------- | ---------------------------------- | --------------------------------------- |
| `useAsKnowledgeBase`   | boolean | `true`        | `HEREVAULT_USE_AS_KNOWLEDGE_BASE`  | 启用知识库功能                                 |
| `syncMemories`         | boolean | `true`        | `HEREVAULT_SYNC_MEMORIES`          | 启用记忆同步功能                                |
| `knowledgeSearchScope` | string  | `"knowledge"` | `HEREVAULT_KNOWLEDGE_SEARCH_SCOPE` | 搜索范围：`knowledge`（仅知识库）或 `all`（知识库 + 记忆） |

#### `index` - 索引设置

| 字段              | 类型     | 默认值         | 环境变量                      | 说明                                 |
| --------------- | ------ | ----------- | ------------------------- | ---------------------------------- |
| `embeddingDim`  | number | `1024`      | `HEREVAULT_EMBEDDING_DIM` | 嵌入向量维度                             |
| `chunkStrategy` | string | `"heading"` | -                         | 分块策略：`heading`、`paragraph`、`fixed` |
| `chunkSize`     | number | `1000`      | -                         | 最大分块大小（字符数）                        |
| `chunkOverlap`  | number | `150`       | -                         | 相邻分块之间的重叠字符数                       |

#### `model` - 模型设置

| 字段                  | 类型      | 默认值                       | 环境变量                         | 说明                                        |
| ------------------- | ------- | ------------------------- | ---------------------------- | ----------------------------------------- |
| `dir`               | string  | `""`（自动）                  | `HEREVAULT_MODEL_DIR`        | 模型目录路径。为空时默认为 `<vault>/.herevault/models` |
| `embedModel`        | string  | `"bge-m3-ONNX-int8"`      | -                            | 嵌入模型名称                                    |
| `rerankModel`       | string  | `"ms-marco-MiniLM-L6-v2"` | -                            | 重排序模型名称                                   |
| `rerankerType`      | string  | `"auto"`                  | `HEREVAULT_RERANKER_TYPE`    | 重排序类型：`auto`、`bge`、`cross-encoder`        |
| `enableReranker`    | boolean | `true`                    | `HEREVAULT_ENABLE_RERANKER`  | 启用/禁用重排序模型                                |
| `interOpNumThreads` | number  | `1`                       | `HEREVAULT_INTER_OP_THREADS` | ONNX 跨算子线程数                               |
| `intraOpNumThreads` | number  | `4`                       | `HEREVAULT_INTRA_OP_THREADS` | ONNX 算子内线程数                               |

#### `search` - 搜索设置

| 字段                   | 类型      | 默认值     | 环境变量                             | 说明                 |
| -------------------- | ------- | ------- | -------------------------------- | ------------------ |
| `searchLimit`        | number  | `10`    | `HEREVAULT_SEARCH_LIMIT`         | 返回的最大搜索结果数         |
| `includeFullContent` | boolean | `false` | `HEREVAULT_INCLUDE_FULL_CONTENT` | 在搜索结果中包含完整内容       |
| `maxContentLength`   | number  | `500`   | `HEREVAULT_MAX_CONTENT_LENGTH`   | 搜索结果中的最大内容长度       |
| `bm25K1`             | number  | `1.5`   | `HEREVAULT_BM25_K1`              | BM25 K1 参数（词频饱和度）  |
| `bm25B`              | number  | `0.75`  | `HEREVAULT_BM25_B`               | BM25 B 参数（文档长度归一化） |
| `rrfK`               | number  | `60`    | `HEREVAULT_RRF_K`                | RRF K 参数（倒数排名融合常数） |
| `vectorSearchLimit`  | number  | `50`    | `HEREVAULT_VECTOR_SEARCH_LIMIT`  | 向量搜索最大候选数          |
| `bm25SearchLimit`    | number  | `50`    | `HEREVAULT_BM25_SEARCH_LIMIT`    | BM25 搜索最大候选数       |
| `rrfFusionLimit`     | number  | `20`    | `HEREVAULT_RRF_FUSION_LIMIT`     | RRF 融合后最大结果数       |
| `contextWindowSize`  | number  | `1`     | `HEREVAULT_CONTEXT_WINDOW_SIZE`  | 匹配分块周围的上下文分块数      |

#### `sync` - 同步设置

| 字段             | 类型      | 默认值      | 环境变量                      | 说明                 |
| -------------- | ------- | -------- | ------------------------- | ------------------ |
| `startupSync`  | boolean | `true`   | -                         | 服务器启动时执行同步         |
| `watchChanges` | boolean | `true`   | -                         | 监听文件变更             |
| `interval`     | number  | `300000` | `HEREVAULT_SYNC_INTERVAL` | 定期同步间隔（毫秒，默认 5 分钟） |
| `debounceMs`   | number  | `500`    | -                         | 文件变更防抖时间（毫秒）       |

#### `log` - 日志

| 字段      | 类型     | 默认值      | 环境变量                  | 说明                                 |
| ------- | ------ | -------- | --------------------- | ---------------------------------- |
| `level` | string | `"info"` | `HEREVAULT_LOG_LEVEL` | 日志级别：`debug`、`info`、`warn`、`error` |

#### `mode` - 服务器模式

| 字段   | 类型     | 默认值     | 环境变量             | 说明                        |
| ---- | ------ | ------- | ---------------- | ------------------------- |
| (根级) | string | `"mcp"` | `HEREVAULT_MODE` | 服务器模式：`mcp`、`http`、`both` |

#### `http` - HTTP 服务器

| 字段     | 类型     | 默认值           | 环境变量                  | 说明         |
| ------ | ------ | ------------- | --------------------- | ---------- |
| `port` | number | `3000`        | `HEREVAULT_HTTP_PORT` | HTTP 服务器端口 |
| `host` | string | `"127.0.0.1"` | `HEREVAULT_HTTP_HOST` | HTTP 服务器主机 |

### 环境变量完整列表

| 环境变量                               | 配置路径                          | 类型              |
| ---------------------------------- | ----------------------------- | --------------- |
| `HEREVAULT_VAULT_PATH`             | vault.path                    | string          |
| `HEREVAULT_MEMORIES_DIR`           | vault.memoriesDir             | string          |
| `HEREVAULT_KNOWLEDGE_BASE_DIR`     | vault.knowledgeBaseDir        | string          |
| `HEREVAULT_SOURCE_DIRS`            | vault.sourceDirs              | string\[]（逗号分隔） |
| `HEREVAULT_USE_AS_KNOWLEDGE_BASE`  | obsidian.useAsKnowledgeBase   | boolean         |
| `HEREVAULT_SYNC_MEMORIES`          | obsidian.syncMemories         | boolean         |
| `HEREVAULT_KNOWLEDGE_SEARCH_SCOPE` | obsidian.knowledgeSearchScope | string          |
| `HEREVAULT_EMBEDDING_DIM`          | index.embeddingDim            | number          |
| `HEREVAULT_MODEL_DIR`              | model.dir                     | string          |
| `HEREVAULT_ENABLE_RERANKER`        | model.enableReranker          | boolean         |
| `HEREVAULT_RERANKER_TYPE`          | model.rerankerType            | string          |
| `HEREVAULT_INTER_OP_THREADS`       | model.interOpNumThreads       | number          |
| `HEREVAULT_INTRA_OP_THREADS`       | model.intraOpNumThreads       | number          |
| `HEREVAULT_SEARCH_LIMIT`           | search.searchLimit            | number          |
| `HEREVAULT_INCLUDE_FULL_CONTENT`   | search.includeFullContent     | boolean         |
| `HEREVAULT_MAX_CONTENT_LENGTH`     | search.maxContentLength       | number          |
| `HEREVAULT_BM25_K1`                | search.bm25K1                 | number          |
| `HEREVAULT_BM25_B`                 | search.bm25B                  | number          |
| `HEREVAULT_RRF_K`                  | search.rrfK                   | number          |
| `HEREVAULT_VECTOR_SEARCH_LIMIT`    | search.vectorSearchLimit      | number          |
| `HEREVAULT_BM25_SEARCH_LIMIT`      | search.bm25SearchLimit        | number          |
| `HEREVAULT_RRF_FUSION_LIMIT`       | search.rrfFusionLimit         | number          |
| `HEREVAULT_CONTEXT_WINDOW_SIZE`    | search.contextWindowSize      | number          |
| `HEREVAULT_SYNC_INTERVAL`          | sync.interval                 | number          |
| `HEREVAULT_LOG_LEVEL`              | log.level                     | string          |
| `HEREVAULT_MODE`                   | mode                          | string          |
| `HEREVAULT_HTTP_PORT`              | http.port                     | number          |
| `HEREVAULT_HTTP_HOST`              | http.host                     | string          |

### Reranker 配置指南

#### 支持的重排序模型

| 模型                             | 类型            | 大小      | 说明                       |
| ------------------------------ | ------------- | ------- | ------------------------ |
| `ms-marco-MiniLM-L6-v2`        | Cross-Encoder | \~87MB  | **默认** - 轻量级，推理速度快，准确率良好 |
| `bge-reranker-v2-m3-ONNX-int8` | BGE-Reranker  | \~570MB | 更高准确率，内存占用更大             |

#### `rerankerType` 选项

- `auto`（默认）：自动检测模型架构
- `bge`：强制使用 BGE-Reranker 模式（适用于 bge-reranker 模型）
- `cross-encoder`：强制使用 Cross-Encoder 模式（适用于 ms-marco 模型）

#### `enableReranker` 使用场景

- **`true`（默认）**：完整混合搜索，包含重排序（向量 + BM25 + 重排序）
- **`false`**：不含重排序的混合搜索（仅向量 + BM25）

适合禁用重排序的场景：

- 更快的启动速度（无需下载/加载重排序模型）
- 更低的内存占用
- 向量 + BM25 已能满足搜索质量需求
- 运行在资源受限的环境中

#### 自定义模型路径

支持绝对路径和相对路径（相对于 Vault 根目录）：

**配置文件：**

```yaml
model:
  dir: /home/user/models     # 绝对路径
  # 或
  dir: ./models               # 相对路径（基于 Vault 根目录解析）
```

**环境变量：**

```bash
export HEREVAULT_MODEL_DIR=/home/user/models    # 绝对路径
# 或
export HEREVAULT_MODEL_DIR=./models              # 相对路径
```

**路径解析规则：**

- 绝对路径：直接使用（如 `/home/user/models`、`C:\Models`）
- 相对路径：基于 Vault 根目录解析（如 `./models` 解析为 `<vault>/models`）

### 搜索调优指南

HereVault 使用混合搜索方法，结合向量搜索、BM25 文本搜索和倒数排名融合（RRF）：

1. **向量搜索**：使用 BGE-M3 嵌入查找语义相似的内容
2. **BM25 搜索**：使用 BM25 算法查找关键词匹配的内容
3. **RRF 融合**：使用倒数排名融合合并两种搜索的结果
4. **重排序**（可选）：使用交叉编码器模型对融合结果重新评分

#### 调优 BM25 参数

- **`bm25K1`**（默认：1.5）：控制词频饱和度。值越高，词频的影响越大。范围：0-3。
- **`bm25B`**（默认：0.75）：控制文档长度归一化。值越高，对较长文档的惩罚越大。范围：0-1。

#### 调优 RRF 参数

- **`rrfK`**（默认：60）：RRF 公式中的常数。值越低，排名靠前的结果权重越大。范围：1-100。

#### 搜索管道限制

- **`vectorSearchLimit`**（默认：50）：向量搜索的候选数
- **`bm25SearchLimit`**（默认：50）：BM25 搜索的候选数
- **`rrfFusionLimit`**（默认：20）：RRF 融合后的结果数（重排序之前）
- **`searchLimit`**（默认：10）：最终返回给用户的结果数

### 分块策略指南

分块策略控制文档如何被拆分为更小的片段以进行索引：

| 策略          | 说明              | 适用场景         |
| ----------- | --------------- | ------------ |
| `heading`   | 按 Markdown 标题拆分 | 具有清晰标题的结构化文档 |
| `paragraph` | 按段落拆分           | 自然文本、文章、散文   |
| `fixed`     | 按固定字符数拆分        | 格式统一的文档、日志   |

#### 分块大小与重叠

- **`chunkSize`**（默认：1000）：每个分块的最大字符数
  - 较小的分块：匹配更精确，但可能丢失上下文
  - 较大的分块：上下文更完整，但可能包含无关内容
- **`chunkOverlap`**（默认：150）：相邻分块之间的重叠字符数
  - 确保分块边界处的内容不会丢失
  - 较大的重叠：边界处理更好，但存储量更大

### 默认配置文件

```yaml
vault:
  path: /path/to/your/vault
  memoriesDir: Memories
  knowledgeBaseDir: Knowledge
  sourceDirs:
    - Source

obsidian:
  useAsKnowledgeBase: true
  syncMemories: true
  knowledgeSearchScope: knowledge

index:
  embeddingDim: 1024
  chunkStrategy: heading
  chunkSize: 1000
  chunkOverlap: 150

model:
  dir: /path/to/models
  embedModel: bge-m3-ONNX-int8
  rerankModel: ms-marco-MiniLM-L6-v2
  rerankerType: auto
  enableReranker: true
  interOpNumThreads: 1
  intraOpNumThreads: 4

search:
  searchLimit: 10
  includeFullContent: false
  maxContentLength: 500
  bm25K1: 1.5
  bm25B: 0.75
  rrfK: 60
  vectorSearchLimit: 50
  bm25SearchLimit: 50
  rrfFusionLimit: 20
  contextWindowSize: 1

sync:
  startupSync: true
  watchChanges: true
  interval: 300000
  debounceMs: 500

log:
  level: info

mode: mcp

http:
  port: 3000
  host: 127.0.0.1
```

