# Distributed Data Labeling / 分布式数据标注

> 多个 AI Agent 对数据集进行协同标注，标注结果存入 Walrus 并交叉验证，构建高质量去中心化标注数据集

## 解决什么问题

传统数据标注依赖大量人工，成本高且质量参差不齐。单一 AI 标注缺乏交叉验证，容易引入系统性偏差。本方案通过多个独立 AI Agent 同时标注同一数据集，结果持久化在 Walrus 上实现去中心化验证，确保标注质量可追溯、可审计。

## 核心功能

- 多 Agent 并行标注：多个 AI Agent 独立对同一批数据进行分类、实体识别、情感分析等标注
- 一致性自动检测：系统自动比对各 Agent 标注结果，标记不一致样本供人工审核
- 标注历史追溯：所有标注版本和最终共识存储在 Walrus，任何时刻可回溯
- 数据集版本管理：支持数据集迭代更新，每次标注结果作为新版本存入 Walrus

## 使用的 Walrus / Sui 能力

| 能力 | 用途 |
|------|------|
| Walrus Blob 存储 | 存储原始数据集、各 Agent 标注结果 JSON、最终共识数据 |
| Sui 对象模型 | 链上记录数据集元信息、Agent 列表、共识状态、版本号 |
| Walrus 读取 API | 按版本号检索历史标注结果，支持数据集回溯 |
| Seal 加密 | 对含敏感信息的原始数据进行加密存储，仅授权 Agent 可解密 |

## 实现方案

### Agent / AI 部分
- 主控 Agent 负责任务分发和结果汇总，子 Agent 各自独立标注
- 使用 LangChain 多 Agent 架构，每个标注 Agent 有不同的 prompt 策略降低偏差
- 一致性检测使用投票机制 + 置信度评分，低一致样本自动触发额外 Agent 复核
- 采用 GPT-4o 和 Claude 双模型交叉标注，提升结果可靠性

### Walrus 集成
- 原始数据以 `datasets/{dataset_id}/raw.json` 存储
- 各 Agent 标注结果以 `labels/{dataset_id}/{agent_id}/{version}.json` 分别存储
- 共识结果以 `consensus/{dataset_id}/{version}.json` 聚合存储
- Sui 链上对象记录数据集 ID、参与 Agent 地址、各 Blob 引用和共识状态

### 前端 / 后端
- 前端：React + Ant Design，展示数据集列表、标注进度、一致性热力图
- 后端：Python FastAPI，提供标注任务调度 API 和一致性分析接口
- 关键页面：数据集管理页、标注进度看板、一致性分析页、版本对比页

## 技术架构

用户上传原始数据集 → 后端将数据存入 Walrus Blob → 主控 Agent 分发任务给多个标注 Agent → 各 Agent 独立标注并将结果写入 Walrus → 一致性检测模块自动比对 → 共识结果聚合存入 Walrus → Sui 链上更新数据集状态 → 前端展示标注结果和一致性报告。

## 难度评估

| 维度 | 难度 | 说明 |
|------|------|------|
| Walrus 集成 | ⭐⭐ | 多 Blob 并发读写，版本管理 |
| AI/Agent | ⭐⭐⭐⭐ | 多 Agent 协调、一致性算法、偏差控制 |
| 前端 | ⭐⭐⭐ | 一致性热力图和版本对比较复杂 |
| 集成复杂度 | ⭐⭐⭐ | 多 Agent 并发 + Walrus + 链上状态同步 |

## 官方参赛要求检查

- [ ] 项目名称和描述
- [ ] 项目 Logo (1:1)
- [ ] 公开 GitHub 仓库
- [ ] Demo 视频 (≤5min)
- [ ] 测试网部署 + Package ID
