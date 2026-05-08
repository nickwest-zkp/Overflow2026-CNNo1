# MemWal SDK Wrapper / MemWal SDK 包装器

> 为 LangChain/AutoGen 等主流 AI 框架提供 Walrus 记忆层插件，一行代码即可接入去中心化持久记忆

## 解决什么问题

现有 AI 框架（LangChain、AutoGen、CrewAI）的内置记忆模块都是基于本地或中心化存储的，开发者很难切换到去中心化存储。本方案提供统一的 SDK 包装器，让开发者只需一行代码就能将 Agent 的记忆层替换为 Walrus，获得去中心化、持久化、可验证的记忆能力。

## 核心功能

- 一键记忆替换：通过装饰器或配置将 LangChain Memory 替换为 Walrus 存储
- 多框架兼容：支持 LangChain、AutoGen、CrewAI、LangGraph 等主流框架
- 记忆策略可配：支持摘要记忆、滑动窗口、语义检索等多种记忆策略
- 内置加密支持：通过 Seal 自动加密敏感记忆数据

## 使用的 Walrus / Sui 能力

| 能力 | 用途 |
|------|------|
| Walrus Blob 存储 | 存储 Agent 的对话记忆、摘要、上下文 |
| MemWal 记忆层 | 核心抽象层，管理记忆的生命周期 |
| Seal 加密 | 加密敏感记忆数据 |
| Sui 对象模型 | 链上记录记忆元数据、访问权限 |

## 实现方案

### Agent / AI 部分
- 记忆管理器核心类实现统一的 save/load/search 接口
- 摘要策略使用 LLM 将长对话压缩为关键信息保留
- 语义检索策略使用向量嵌入支持相似度搜索
- 适配器模式为每个框架实现特定的 Memory 接口
- 提供 CLI 工具快速初始化和测试记忆层

### Walrus 集成
- 记忆数据以 `memory/{agent_id}/sessions/{session_id}.json` 存储
- 摘要以 `memory/{agent_id}/summaries/{topic}.json` 存储
- 向量索引以 `memory/{agent_id}/index/embeddings.bin` 存储
- 配置文件以 `memory/{agent_id}/config.json` 存储记忆策略设置
- SDK 自动处理 Blob 创建、读取和版本管理

### 前端 / 后端
- 前端：React 调试面板，可视化展示 Agent 记忆内容
- 后端：Python SDK 包 + Node.js 类型声明
- 关键页面：记忆浏览器、搜索页、配置页、使用统计页

## 技术架构

开发者安装 SDK → 配置 Walrus 连接参数 → 使用装饰器或配置替换框架默认记忆 → Agent 运行时调用 save → SDK 将记忆序列化为 JSON → 写入 Walrus Blob → 更新 Sui 链上索引 → Agent 下次调用 load → SDK 从 Walrus 读取 Blob → 反序列化为框架记忆对象 → 注入 Agent 上下文。

## 难度评估

| 维度 | 难度 | 说明 |
|------|------|------|
| Walrus 集成 | ⭐⭐⭐⭐ | 需要深入理解 Walrus API 和优化存储策略 |
| AI/Agent | ⭐⭐⭐ | 记忆策略设计和语义检索实现 |
| 前端 | ⭐⭐ | 调试面板相对简单 |
| 集成复杂度 | ⭐⭐⭐⭐ | 多框架适配、API 设计、性能优化 |

## 官方参赛要求检查

- [ ] 项目名称和描述
- [ ] 项目 Logo (1:1)
- [ ] 公开 GitHub 仓库
- [ ] Demo 视频 (≤5min)
- [ ] 测试网部署 + Package ID
