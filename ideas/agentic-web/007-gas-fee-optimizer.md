# Gas Fee Optimizer / Gas 费优化 Agent

> 分析历史 Gas 模式，AI 推荐 PTB 打包策略和最佳提交时机

## 解决什么问题

Sui 用户和 DeFi 协议在高拥堵时段面临 Gas 费飙升的问题，缺乏科学的 Gas 优化策略。批量交易和复杂 PTB 的 Gas 估算困难，容易造成 Gas 浪费或交易失败。本项目利用 AI 模型分析历史 Gas 模式和网络拥堵趋势，为用户推荐最佳交易提交时机和 PTB 打包策略，帮助 Sui 生态用户显著降低交易成本。

## 核心功能

- 实时监控 Sui 网络 Gas 价格和拥堵状况，预测未来 Gas 趋势（5 分钟 / 30 分钟 / 1 小时）
- 分析用户 PTB 结构，推荐 Gas 优化方案（合并交易、调整顺序、拆分 PTB）
- 定时交易功能：用户设定目标 Gas 价格，Agent 自动在 Gas 最低时段提交交易
- Gas 费历史分析和节省统计报告

## 使用的 Sui 原语

| 原语 | 用途 |
|------|------|
| PTB | 优化 PTB 结构，合并关联交易减少总 Gas 消耗 |
| Gas 代币 | 分析 Gas Coin 对象使用效率，推荐最优 Gas 分配 |
| 共享对象 | `GasStrategy` 共享对象存储用户 Gas 优化配置和统计 |
| 事件（Event） | 发出 `GasOptimizedEvent` 记录每次 Gas 节省 |

## 实现方案

### 智能合约 (Move)
- **`gas_strategy` 模块**：定义 `GasStrategy` 对象，字段包括 `target_gas_price: u64`、`max_wait_epochs: u64`、`auto_submit: bool`、`pending_txns: vector<ID>`
- **`gas_analyzer` 模块**：`estimate_optimized_gas(ptb_bytes: vector<u8>): u64` 链上 Gas 估算辅助函数
- **`scheduled_submit` 模块**：`schedule_transaction(strategy: &mut GasStrategy, ptb_bytes: vector<u8>, max_gas: u64)` 定时提交待执行交易
- 关键数据结构：`GasRecord { tx_digest: vector<u8>, gas_used: u64, gas_price: u64, saved_pct: u64, timestamp: u64 }`

### 前端 / 后端
- **前端**：React + Recharts，Gas 价格趋势图、优化建议卡片、定时交易管理面板
- **后端**：Rust Agent 服务，实时采集网络 Gas 数据，运行 AI 预测模型，管理定时交易队列
- 关键功能：Gas 价格看板、PTB 分析器、定时任务管理

### AI / Agent 部分
- 时序预测：LSTM 模型基于历史 Gas 数据预测短期 Gas 价格走势
- PTB 优化：GPT-4o 分析 PTB 结构，推荐合并、拆分或重排序方案
- Agent 工作流：实时采集 Gas 数据 → LSTM 预测 → 用户交易队列匹配 → 等待最佳时机 → 自动提交

## 技术架构

Agent 服务通过 Sui RPC 实时采集最近 epoch 的 Gas 使用数据，输入 LSTM 模型预测未来 Gas 走势。用户提交待执行交易后进入定时队列，Agent 在 Gas 价格低于目标值时自动通过付费节点提交。对于复杂 PTB，GPT-4o 分析交易结构并推荐优化方案。所有 Gas 节省记录通过 GasRecord 对象上链，前端实时展示 Gas 趋势和节省统计。

## 难度评估

| 维度 | 难度 | 说明 |
|------|------|------|
| Move 合约 | ⭐⭐ | Gas 策略管理和定时提交逻辑 |
| 前端 | ⭐⭐ | Gas 趋势图和交易管理界面 |
| AI/ML | ⭐⭐⭐ | LSTM Gas 预测和 PTB 结构优化 |
| 集成复杂度 | ⭐⭐⭐ | Gas 数据采集和定时交易执行引擎 |

## 官方参赛要求检查

- [ ] 项目名称和描述
- [ ] 项目 Logo (1:1)
- [ ] 公开 GitHub 仓库
- [ ] Demo 视频 (≤5min)
- [ ] 测试网部署 + Package ID
