[English](README.md) | [中文](README_zh.md)

# HereVault

[![Node.js Version](https://img.shields.io/badge/node-%3E%3D18.0.0-brightgreen.svg)](https://nodejs.org/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.0-blue.svg)](https://www.typescriptlang.org/)
[![npm version](https://img.shields.io/npm/v/herevault.svg)](https://www.npmjs.com/package/herevault)

> AI agent memory and knowledge base system powered by Obsidian Vault via MCP protocol

HereVault is a memory and knowledge base system designed for AI Agents, built on Obsidian Vault. It supports interaction via the MCP (Model Context Protocol) and RESTful HTTP API. Using vector databases (LanceDB) and semantic search technology, it provides long-term memory storage and knowledge retrieval capabilities for AI Agents.

## Features

- **Multi-Interface Support**: Supports MCP protocol (stdio), RESTful HTTP API, and CLI
- **Memory Management**: Save, retrieve, and delete AI Agent memories (6 types: preference, habit, fact, conversation, skill, context)
- **Knowledge Base**: Build and manage structured knowledge bases with semantic search
- **Smart Search**: Hybrid search (vector + BM25 + RRF reranking) powered by BGE-M3 embedding model and configurable Reranker
- **Obsidian Integration**: Native support for Obsidian Vault format with Markdown file storage
- **High Performance**: Model pooling, background indexing, and incremental sync
- **Auto Sync**: Monitor file changes and automatically sync to the vector database

## Architecture

```
Obsidian Vault (Markdown Files)
    |
    v
HereVault Server
    |
    +---> MCP Protocol (stdio) ----> AI Agents (Claude, Cursor, etc.)
    |
    +---> RESTful HTTP API ------> External Applications
    |
    +---> CLI Commands ----------> Direct Operations
```

## Documentation

| Document | Description |
|----------|-------------|
| [Getting Started](docs/getting-started.md) | Installation and quick start guide |
| [CLI Reference](docs/cli/README.md) | Complete command-line interface documentation |
| [MCP Usage Guide](docs/mcp/README.md) | MCP protocol integration and tool reference |
| [RESTful API Reference](docs/api/README.md) | HTTP API endpoints and schemas |
| [Configuration Reference](docs/configuration/README.md) | All configuration options and environment variables |

## Quick Install

```bash
npm install -g herevault
```

## Quick Start

```bash
# 1. Initialize vault
herevault init --vault /path/to/your/vault

# 2. Download models
herevault download-models --vault /path/to/your/vault

# 3. Start server
herevault serve --vault /path/to/your/vault
```

See [Getting Started](docs/getting-started.md) for detailed instructions.

## License

This software is proprietary and confidential. Unauthorized copying, distribution, or use is strictly prohibited.
