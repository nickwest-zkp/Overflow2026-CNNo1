# API Doc Generator / API 文档自动生成器

> 从智能合约和后端接口自动生成 API 文档与示例代码，存储在 Walrus 始终保持最新版本

## 解决什么问题

API 文档编写耗时且容易过时，尤其是智能合约接口变更频繁时。开发者经常因为文档与实际接口不一致而浪费大量时间调试。本方案通过 AI Agent 自动从源代码生成和维护 API 文档，利用 Walrus 确保文档始终可访问且与最新代码同步。

## 核心功能

- 智能合约接口解析：自动解析 Move/Solidity 合约的函数签名、参数类型、事件定义
- 文档自动生成：为每个接口生成详细描述、参数说明、返回值格式、示例调用
- 多语言 SDK 示例：自动生成 TypeScript、Python、Rust 等多语言调用示例
- 变更追踪：接口变更时自动高亮差异，生成迁移指南

## 使用的 Walrus / Sui 能力

| 能力 | 用途 |
|------|------|
| Walrus Blob 存储 | 存储完整 API 文档、各版本快照、示例代码 |
| Sui 对象模型 | 链上记录包 ID、文档版本、接口哈希 |
| Sui Move 解析 | 直接读取链上包的字节码和 ABI |
| Walrus 读取 API | 支持开发者通过 URL 直接访问最新文档 |

## 实现方案

### Agent / AI 部分
- 解析 Agent 使用 Move 编译器接口和 AST 分析提取合约接口
- 文档生成 Agent 使用 LLM 根据函数签名和注释生成自然语言描述
- 示例生成 Agent 根据接口定义自动生成多语言 SDK 调用示例
- 差异 Agent 对比新旧版本接口，自动生成 Breaking Changes 报告

### Walrus 集成
- 完整文档以 `docs/{package_id}/api_v{V}.md` 存储
- 接口元数据以 `docs/{package_id}/schema.json` 存储
- 示例代码以 `docs/{package_id}/examples/{lang}/` 目录存储
- 变更日志以 `docs/{package_id}/changelog.json` 存储

### 前端 / 后端
- 前端：Next.js + Swagger UI 风格，展示 API 文档和交互式调试
- 后端：Rust 服务处理 Move 解析，Python 服务运行 AI 文档生成
- 关键页面：API 文档浏览页、交互式调试页、版本对比页、示例代码页

## 技术架构

监控链上包部署/升级事件 → 解析 Agent 获取新版本 ABI → 文档生成 Agent 生成接口描述 → 示例生成 Agent 生成代码示例 → 完整文档存入 Walrus → Sui 链上记录文档版本和 Blob 引用 → 差异 Agent 对比生成变更日志 → 前端渲染文档和交互式调试界面。

## 难度评估

| 维度 | 难度 | 说明 |
|------|------|------|
| Walrus 集成 | ⭐⭐ | 文档版本管理和多语言示例存储 |
| AI/Agent | ⭐⭐⭐ | Move 合约语义理解和文档质量保证 |
| 前端 | ⭐⭐⭐ | API 文档 UI 和交互式调试功能 |
| 集成复杂度 | ⭐⭐⭐ | 链上解析 + AI 生成 + 文档托管全链路 |

## 官方参赛要求检查

- [ ] 项目名称和描述
- [ ] 项目 Logo (1:1)
- [ ] 公开 GitHub 仓库
- [ ] Demo 视频 (≤5min)
- [ ] 测试网部署 + Package ID
