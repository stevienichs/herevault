[English](README.md) | [中文](README_zh.md)

# HereVault

[![Node.js Version](https://img.shields.io/badge/node-%3E%3D18.0.0-brightgreen.svg)](https://nodejs.org/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.0-blue.svg)](https://www.typescriptlang.org/)
[![npm version](https://img.shields.io/npm/v/herevault.svg)](https://www.npmjs.com/package/herevault)

> 基于 Obsidian Vault 的 AI Agent 记忆与知识库系统，通过 MCP 协议驱动

HereVault 是为 AI Agent 打造的长期记忆与知识库系统，基于 Obsidian Vault 构建。支持通过 MCP（Model Context Protocol）协议和 RESTful HTTP API 进行交互，使用 LanceDB 向量数据库和语义搜索技术，为 AI Agent 提供持久化的记忆存储与知识检索能力。

## 核心特性

- **多接口支持**: 同时支持 MCP 协议 (stdio)、RESTful HTTP API 和 CLI
- **记忆管理**: 保存、检索、删除 AI Agent 的记忆（6 种类型：偏好、习惯、事实、对话、技能、上下文）
- **知识库**: 构建和管理结构化知识库，支持语义搜索
- **智能搜索**: 基于 BGE-M3 嵌入模型和可配置 Reranker 的混合搜索（向量 + BM25 + RRF 重排序）
- **Obsidian 集成**: 原生支持 Obsidian Vault 格式，Markdown 文件存储
- **高性能**: 支持模型池化、后台索引、增量同步
- **自动同步**: 监控文件变化，自动同步到向量数据库

## 架构

```
Obsidian Vault (Markdown 文件)
    |
    v
HereVault 服务器
    |
    +---> MCP 协议 (stdio) ----> AI 代理 (Claude、Cursor 等)
    |
    +---> RESTful HTTP API ------> 外部应用
    |
    +---> CLI 命令 ----------> 直接操作
```

## 文档导航

| 文档 | 说明 |
|------|------|
| [快速开始](docs/getting-started_zh.md) | 安装和快速入门指南 |
| [命令行参考](docs/cli/README_zh.md) | 完整的命令行界面文档 |
| [MCP 使用指南](docs/mcp/README_zh.md) | MCP 协议集成和工具参考 |
| [RESTful API 参考](docs/api/README_zh.md) | HTTP API 端点和数据模型 |
| [配置参考](docs/configuration/README_zh.md) | 所有配置选项和环境变量 |

## 快速安装

```bash
npm install -g herevault
```

## 快速开始

```bash
# 1. 初始化 Vault
herevault init --vault /path/to/your/vault

# 2. 下载模型
herevault download-models --vault /path/to/your/vault

# 3. 启动服务器
herevault serve --vault /path/to/your/vault
```

详见 [快速开始](docs/getting-started_zh.md)。

## 许可证

本软件为专有软件，未经授权的复制、分发或使用均被严格禁止。
