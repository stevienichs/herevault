[English](getting-started.md) | [中文](getting-started_zh.md)

# Getting Started

## Prerequisites

- Node.js >= 18.0.0
- Supported OS: Windows, macOS, Linux

## Installation

```bash
npm install -g herevault
```

## Model Download

HereVault requires ONNX model files:

1. **Embedding Model**: `bge-m3-ONNX-int8` - for generating text vector embeddings
2. **Reranking Model**: `ms-marco-MiniLM-L6-v2` (default) or `bge-reranker-v2-m3-ONNX-int8` - for reranking search results

Models are automatically downloaded to `<vault>/.herevault/models/` on first startup. You can also manually download them.

## Quick Start (3 Steps)

### Step 1: Initialize the vault

```bash
herevault init --vault /path/to/your/vault
```

This creates the vault directory structure:

```
your-vault/
├── .herevault/          # HereVault data and config
│   ├── config.yml       # Config file
│   ├── db/              # LanceDB database
│   └── models/          # ONNX model files
├── Memories/            # Memory storage (6 type subdirectories)
│   ├── preference/
│   ├── habit/
│   ├── fact/
│   ├── conversation/
│   ├── skill/
│   └── context/
├── Knowledge/           # Knowledge base storage
└── Source/              # Source directory (optional)
```

### Step 2: Download models

```bash
herevault download-models --vault /path/to/your/vault
```

Options:

- `--skip-rerank` - Skip reranker model download
- `--embed-model <name>` - Specify embedding model (default: bge-m3-ONNX-int8)
- `--rerank-model <name>` - Specify reranker model (default: ms-marco-MiniLM-L6-v2)

### Step 3: Start the server

```bash
herevault serve --vault /path/to/your/vault
```

## MCP Configuration

For Claude Desktop, add to `claude_desktop_config.json`:

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

For Cursor, create `.cursor/mcp.json`:

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

## HTTP Mode

To start the HTTP API server:

```bash
herevault serve --vault /path/to/your/vault --mode http --port 3000
```

Or both MCP and HTTP:

```bash
herevault serve --vault /path/to/your/vault --mode both --port 3000
```

## Next Steps

- [CLI Reference](cli/README.md) - Complete command-line documentation
- [MCP Usage Guide](mcp/README.md) - MCP protocol integration
- [RESTful API Reference](api/README.md) - HTTP API documentation
- [Configuration Reference](configuration/README.md) - All configuration options
