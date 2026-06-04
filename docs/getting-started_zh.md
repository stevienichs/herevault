[English](getting-started.md) | [中文](getting-started_zh.md)

# 快速开始

## 环境要求

- Node.js >= 18.0.0
- 支持的操作系统: Windows, macOS, Linux

## 安装

```bash
npm install -g herevault
```

## 模型下载

HereVault 需要 ONNX 模型文件：

1. **嵌入模型**: `bge-m3-ONNX-int8` - 用于生成文本向量嵌入
2. **重排序模型**: `ms-marco-MiniLM-L6-v2`（默认）或 `bge-reranker-v2-m3-ONNX-int8` - 用于对搜索结果进行重排序

首次启动服务时，模型会自动下载到 `<vault>/.herevault/models/` 目录。您也可以手动下载模型。

## 快速开始（3 步）

### 第 1 步：初始化 Vault

```bash
herevault init --vault /path/to/your/vault
```

此命令会创建以下 Vault 目录结构：

```
your-vault/
├── .herevault/          # HereVault 数据和配置
│   ├── config.yml       # 配置文件
│   ├── db/              # LanceDB 数据库
│   └── models/          # ONNX 模型文件
├── Memories/            # 记忆存储（6 个类型子目录）
│   ├── preference/
│   ├── habit/
│   ├── fact/
│   ├── conversation/
│   ├── skill/
│   └── context/
├── Knowledge/           # 知识库存储
└── Source/              # 来源目录（可选）
```

### 第 2 步：下载模型

```bash
herevault download-models --vault /path/to/your/vault
```

选项：

- `--skip-rerank` - 跳过重排序模型下载
- `--embed-model <name>` - 指定嵌入模型（默认：bge-m3-ONNX-int8）
- `--rerank-model <name>` - 指定重排序模型（默认：ms-marco-MiniLM-L6-v2）

### 第 3 步：启动服务器

```bash
herevault serve --vault /path/to/your/vault
```

## MCP 配置

对于 Claude Desktop，在 `claude_desktop_config.json` 中添加：

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

对于 Cursor，创建 `.cursor/mcp.json`：

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

## HTTP 模式

启动 HTTP API 服务器：

```bash
herevault serve --vault /path/to/your/vault --mode http --port 3000
```

或同时启动 MCP 和 HTTP：

```bash
herevault serve --vault /path/to/your/vault --mode both --port 3000
```

## 下一步

- [CLI 参考](cli/README.md) - 完整的命令行文档
- [MCP 使用指南](mcp/README.md) - MCP 协议集成
- [RESTful API 参考](api/README.md) - HTTP API 文档
- [配置参考](configuration/README.md) - 所有配置选项
