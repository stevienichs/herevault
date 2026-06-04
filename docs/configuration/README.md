[English](README.md) | [中文](README_zh.md)

# Configuration Reference

### Configuration File

The configuration file is located at `<vault>/.herevault/config.yml` and is auto-generated on first startup with default values. The file uses YAML format.

### Configuration Priority

Configuration values are merged in the following priority (highest to lowest):

1. **Command Line Arguments**: `--reranker false`
2. **Environment Variables**: `HEREVAULT_ENABLE_RERANKER=false`
3. **Config File**: `model.enableReranker: false`
4. **Default Values**: `true`

### Full Configuration Reference

#### `vault` - Vault Settings

| Field              | Type      | Default       | Env Variable                   | Description                         |
| ------------------ | --------- | ------------- | ------------------------------ | ----------------------------------- |
| `path`             | string    | `""`          | `HEREVAULT_VAULT_PATH`         | Absolute path to the Obsidian Vault |
| `memoriesDir`      | string    | `"Memories"`  | `HEREVAULT_MEMORIES_DIR`       | Directory name for memory storage   |
| `knowledgeBaseDir` | string    | `"Knowledge"` | `HEREVAULT_KNOWLEDGE_BASE_DIR` | Directory name for knowledge base   |
| `sourceDirs`       | string\[] | `["Source"]`  | `HEREVAULT_SOURCE_DIRS`        | List of source directory names      |

#### `obsidian` - Obsidian Integration

| Field                  | Type    | Default       | Env Variable                       | Description                                                              |
| ---------------------- | ------- | ------------- | ---------------------------------- | ------------------------------------------------------------------------ |
| `useAsKnowledgeBase`   | boolean | `true`        | `HEREVAULT_USE_AS_KNOWLEDGE_BASE`  | Enable knowledge base features                                           |
| `syncMemories`         | boolean | `true`        | `HEREVAULT_SYNC_MEMORIES`          | Enable memory sync features                                              |
| `knowledgeSearchScope` | string  | `"knowledge"` | `HEREVAULT_KNOWLEDGE_SEARCH_SCOPE` | Search scope: `knowledge` (knowledge only) or `all` (knowledge + memory) |

#### `index` - Indexing Settings

| Field           | Type   | Default     | Env Variable              | Description                                        |
| --------------- | ------ | ----------- | ------------------------- | -------------------------------------------------- |
| `embeddingDim`  | number | `1024`      | `HEREVAULT_EMBEDDING_DIM` | Embedding vector dimension                         |
| `chunkStrategy` | string | `"heading"` | -                         | Chunking strategy: `heading`, `paragraph`, `fixed` |
| `chunkSize`     | number | `1000`      | -                         | Maximum chunk size in characters                   |
| `chunkOverlap`  | number | `150`       | -                         | Overlap between chunks in characters               |

#### `model` - Model Settings

| Field               | Type    | Default                   | Env Variable                 | Description                                                             |
| ------------------- | ------- | ------------------------- | ---------------------------- | ----------------------------------------------------------------------- |
| `dir`               | string  | `""` (auto)               | `HEREVAULT_MODEL_DIR`        | Model directory path. If empty, defaults to `<vault>/.herevault/models` |
| `embedModel`        | string  | `"bge-m3-ONNX-int8"`      | -                            | Embedding model name                                                    |
| `rerankModel`       | string  | `"ms-marco-MiniLM-L6-v2"` | -                            | Reranker model name                                                     |
| `rerankerType`      | string  | `"auto"`                  | `HEREVAULT_RERANKER_TYPE`    | Reranker type: `auto`, `bge`, `cross-encoder`                           |
| `enableReranker`    | boolean | `true`                    | `HEREVAULT_ENABLE_RERANKER`  | Enable/disable reranker model                                           |
| `interOpNumThreads` | number  | `1`                       | `HEREVAULT_INTER_OP_THREADS` | ONNX inter-op thread count                                              |
| `intraOpNumThreads` | number  | `4`                       | `HEREVAULT_INTRA_OP_THREADS` | ONNX intra-op thread count                                              |

#### `search` - Search Settings

| Field                | Type    | Default | Env Variable                     | Description                                       |
| -------------------- | ------- | ------- | -------------------------------- | ------------------------------------------------- |
| `searchLimit`        | number  | `10`    | `HEREVAULT_SEARCH_LIMIT`         | Maximum search results returned                   |
| `includeFullContent` | boolean | `false` | `HEREVAULT_INCLUDE_FULL_CONTENT` | Include full content in search results            |
| `maxContentLength`   | number  | `500`   | `HEREVAULT_MAX_CONTENT_LENGTH`   | Maximum content length in search results          |
| `bm25K1`             | number  | `1.5`   | `HEREVAULT_BM25_K1`              | BM25 K1 parameter (term frequency saturation)     |
| `bm25B`              | number  | `0.75`  | `HEREVAULT_BM25_B`               | BM25 B parameter (document length normalization)  |
| `rrfK`               | number  | `60`    | `HEREVAULT_RRF_K`                | RRF K parameter (reciprocal rank fusion constant) |
| `vectorSearchLimit`  | number  | `50`    | `HEREVAULT_VECTOR_SEARCH_LIMIT`  | Maximum vector search candidates                  |
| `bm25SearchLimit`    | number  | `50`    | `HEREVAULT_BM25_SEARCH_LIMIT`    | Maximum BM25 search candidates                    |
| `rrfFusionLimit`     | number  | `20`    | `HEREVAULT_RRF_FUSION_LIMIT`     | Maximum results after RRF fusion                  |
| `contextWindowSize`  | number  | `1`     | `HEREVAULT_CONTEXT_WINDOW_SIZE`  | Number of context chunks around matched chunks    |

#### `sync` - Sync Settings

| Field          | Type    | Default  | Env Variable              | Description                                            |
| -------------- | ------- | -------- | ------------------------- | ------------------------------------------------------ |
| `startupSync`  | boolean | `true`   | -                         | Perform sync on server startup                         |
| `watchChanges` | boolean | `true`   | -                         | Watch for file changes                                 |
| `interval`     | number  | `300000` | `HEREVAULT_SYNC_INTERVAL` | Periodic sync interval in milliseconds (5 min default) |
| `debounceMs`   | number  | `500`    | -                         | File change debounce time in milliseconds              |

#### `log` - Logging

| Field   | Type   | Default  | Env Variable          | Description                                 |
| ------- | ------ | -------- | --------------------- | ------------------------------------------- |
| `level` | string | `"info"` | `HEREVAULT_LOG_LEVEL` | Log level: `debug`, `info`, `warn`, `error` |

#### `mode` - Server Mode

| Field  | Type   | Default | Env Variable     | Description                        |
| ------ | ------ | ------- | ---------------- | ---------------------------------- |
| (root) | string | `"mcp"` | `HEREVAULT_MODE` | Server mode: `mcp`, `http`, `both` |

#### `http` - HTTP Server

| Field  | Type   | Default       | Env Variable          | Description      |
| ------ | ------ | ------------- | --------------------- | ---------------- |
| `port` | number | `3000`        | `HEREVAULT_HTTP_PORT` | HTTP server port |
| `host` | string | `"127.0.0.1"` | `HEREVAULT_HTTP_HOST` | HTTP server host |

### Environment Variables Complete Table

| Environment Variable               | Config Path                   | Type                        |
| ---------------------------------- | ----------------------------- | --------------------------- |
| `HEREVAULT_VAULT_PATH`             | vault.path                    | string                      |
| `HEREVAULT_MEMORIES_DIR`           | vault.memoriesDir             | string                      |
| `HEREVAULT_KNOWLEDGE_BASE_DIR`     | vault.knowledgeBaseDir        | string                      |
| `HEREVAULT_SOURCE_DIRS`            | vault.sourceDirs              | string\[] (comma-separated) |
| `HEREVAULT_USE_AS_KNOWLEDGE_BASE`  | obsidian.useAsKnowledgeBase   | boolean                     |
| `HEREVAULT_SYNC_MEMORIES`          | obsidian.syncMemories         | boolean                     |
| `HEREVAULT_KNOWLEDGE_SEARCH_SCOPE` | obsidian.knowledgeSearchScope | string                      |
| `HEREVAULT_EMBEDDING_DIM`          | index.embeddingDim            | number                      |
| `HEREVAULT_MODEL_DIR`              | model.dir                     | string                      |
| `HEREVAULT_ENABLE_RERANKER`        | model.enableReranker          | boolean                     |
| `HEREVAULT_RERANKER_TYPE`          | model.rerankerType            | string                      |
| `HEREVAULT_INTER_OP_THREADS`       | model.interOpNumThreads       | number                      |
| `HEREVAULT_INTRA_OP_THREADS`       | model.intraOpNumThreads       | number                      |
| `HEREVAULT_SEARCH_LIMIT`           | search.searchLimit            | number                      |
| `HEREVAULT_INCLUDE_FULL_CONTENT`   | search.includeFullContent     | boolean                     |
| `HEREVAULT_MAX_CONTENT_LENGTH`     | search.maxContentLength       | number                      |
| `HEREVAULT_BM25_K1`                | search.bm25K1                 | number                      |
| `HEREVAULT_BM25_B`                 | search.bm25B                  | number                      |
| `HEREVAULT_RRF_K`                  | search.rrfK                   | number                      |
| `HEREVAULT_VECTOR_SEARCH_LIMIT`    | search.vectorSearchLimit      | number                      |
| `HEREVAULT_BM25_SEARCH_LIMIT`      | search.bm25SearchLimit        | number                      |
| `HEREVAULT_RRF_FUSION_LIMIT`       | search.rrfFusionLimit         | number                      |
| `HEREVAULT_CONTEXT_WINDOW_SIZE`    | search.contextWindowSize      | number                      |
| `HEREVAULT_SYNC_INTERVAL`          | sync.interval                 | number                      |
| `HEREVAULT_LOG_LEVEL`              | log.level                     | string                      |
| `HEREVAULT_MODE`                   | mode                          | string                      |
| `HEREVAULT_HTTP_PORT`              | http.port                     | number                      |
| `HEREVAULT_HTTP_HOST`              | http.host                     | string                      |

### Reranker Configuration Guide

#### Supported Reranker Models

| Model                          | Type          | Size    | Description                                              |
| ------------------------------ | ------------- | ------- | -------------------------------------------------------- |
| `ms-marco-MiniLM-L6-v2`        | Cross-Encoder | \~87MB  | **Default** - Lightweight, fast inference, good accuracy |
| `bge-reranker-v2-m3-ONNX-int8` | BGE-Reranker  | \~570MB | Higher accuracy, larger memory footprint                 |

#### `rerankerType` Options

- `auto` (default): Automatically detect model architecture
- `bge`: Force BGE-Reranker mode (for bge-reranker models)
- `cross-encoder`: Force Cross-Encoder mode (for ms-marco models)

#### `enableReranker` Use Cases

- **`true`** **(default)**: Full hybrid search with reranking (vector + BM25 + Reranker)
- **`false`**: Hybrid search without reranking (vector + BM25 only)

When to disable reranker:

- Faster startup (no reranker model download/loading)
- Lower memory usage
- Sufficient search quality from vector + BM25 alone
- Running on resource-constrained environments

#### Custom Model Path

Supports both absolute and relative paths (relative to Vault root):

**Config file:**

```yaml
model:
  dir: /home/user/models     # Absolute path
  # or
  dir: ./models               # Relative path (resolved from vault root)
```

**Environment variable:**

```bash
export HEREVAULT_MODEL_DIR=/home/user/models    # Absolute path
# or
export HEREVAULT_MODEL_DIR=./models              # Relative path
```

**Path resolution rules:**

- Absolute path: Used directly (e.g., `/home/user/models`, `C:\Models`)
- Relative path: Resolved based on Vault root (e.g., `./models` resolves to `<vault>/models`)

### Search Tuning Guide

HereVault uses a hybrid search approach combining vector search, BM25 text search, and Reciprocal Rank Fusion (RRF):

1. **Vector Search**: Finds semantically similar content using BGE-M3 embeddings
2. **BM25 Search**: Finds keyword-matching content using BM25 algorithm
3. **RRF Fusion**: Combines results from both searches using Reciprocal Rank Fusion
4. **Reranking** (optional): Re-scores fused results using a cross-encoder model

#### Tuning BM25 Parameters

- **`bm25K1`** (default: 1.5): Controls term frequency saturation. Higher values increase the impact of term frequency. Range: 0-3.
- **`bm25B`** (default: 0.75): Controls document length normalization. Higher values penalize longer documents more. Range: 0-1.

#### Tuning RRF Parameter

- **`rrfK`** (default: 60): Constant in RRF formula. Lower values give more weight to top-ranked items. Range: 1-100.

#### Search Pipeline Limits

- **`vectorSearchLimit`** (default: 50): Number of candidates from vector search
- **`bm25SearchLimit`** (default: 50): Number of candidates from BM25 search
- **`rrfFusionLimit`** (default: 20): Number of results after RRF fusion (before reranking)
- **`searchLimit`** (default: 10): Final number of results returned to the user

### Chunk Strategy Guide

The chunking strategy controls how documents are split into smaller pieces for indexing:

| Strategy    | Description                    | Best For                                 |
| ----------- | ------------------------------ | ---------------------------------------- |
| `heading`   | Split by Markdown headings     | Structured documents with clear headings |
| `paragraph` | Split by paragraphs            | Natural text, articles, prose            |
| `fixed`     | Split by fixed character count | Uniform documents, logs                  |

#### Chunk Size and Overlap

- **`chunkSize`** (default: 1000): Maximum characters per chunk
  - Smaller chunks: More precise matching, but may lose context
  - Larger chunks: More context, but may include irrelevant content
- **`chunkOverlap`** (default: 150): Characters of overlap between adjacent chunks
  - Ensures content at chunk boundaries is not lost
  - Higher overlap: Better boundary handling, but more storage

### Default Configuration File

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

