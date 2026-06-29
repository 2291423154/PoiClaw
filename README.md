# PoiClaw

基于 [pi-mono](https://github.com/badlogic/pi-mono) 二次开发的自研 AI Coding Agent。

## 项目简介

PoiClaw 是一个 AI 编程助手，能够读取文件、执行命令、修改代码和编写新文件。它支持通过自定义 API 端点接入任意兼容 Anthropic 协议的 LLM 服务。

## 架构设计

```text
┌──────────────────┐
│   coding-agent    │  ← 终端入口、工具定义、会话管理
└────────┬─────────┘
         │
┌────────▼─────────┐
│   agent-core      │  ← Agent 循环引擎：LLM → 工具 → LLM
└────────┬─────────┘
         │
┌────────▼─────────┐
│      ai           │  ← 统一 LLM API（支持多 provider）
└────────┬─────────┘
         │
┌────────▼─────────┐
│   LLM 服务提供商    │  ← 自定义 API 端点（如中科大 LLM 平台）
└──────────────────┘
```

## 与 pi-mono 的区别

- 新增 `ANTHROPIC_BASE_URL` 环境变量，支持自定义 API 端点
- 修改 Anthropic provider 支持 `authToken` 认证方式，适配兼容 API
- 适配自托管 / 自定义 LLM 提供商

## 技术栈

- TypeScript
- Node.js (>= 20)
- Anthropic SDK
- pm2（长期进程管理）

## 快速开始

### 环境要求

- Node.js >= 20
- 一个兼容 OpenAI 或 Anthropic API 格式的 LLM 服务

### 安装

```bash
git clone https://github.com/2291423154/PoiClaw.git
cd PoiClaw
git checkout 3ffc2b43
npm install
npm run build
```

### 使用

```bash
cd packages/coding-agent

# 设置环境变量
export ANTHROPIC_API_KEY="你的API密钥"
export ANTHROPIC_BASE_URL="你的API地址"

# 交互模式
node dist/cli.js --provider anthropic --model 你的模型ID

# 非交互模式（一问一答）
node dist/cli.js --provider anthropic --model 你的模型ID -p "你的问题"
```

## 内置工具

| 工具 | 说明 |
| --- | --- |
| `read` | 读取文件内容 |
| `write` | 创建或覆盖文件 |
| `edit` | 精确替换文件中的文本 |
| `bash` | 执行 Shell 命令 |
| `grep` | 搜索文件内容（只读，默认关闭） |
| `find` | 按模式查找文件（只读，默认关闭） |
| `ls` | 列出目录内容（只读，默认关闭） |

## 为什么 4 个工具就够了？

顶级 LLM 已经经过强化学习（RL），天然理解工具调用。不需要 LangChain、Dify 等重量级框架。pi-mono 作者 Mario Zechner 在他的博客中提到：

> "默认只保留 4 个工具（read、write、edit、bash），其他只读工具按需开启。模型已经会用了，不需要额外的框架。"

## 许可证

本项目基于 [pi-mono](https://github.com/badlogic/pi-mono) 二次开发。原项目采用 AGPL-3.0 许可证。
