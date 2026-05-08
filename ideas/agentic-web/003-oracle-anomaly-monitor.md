# Oracle Anomaly Monitor / 预言机异常监控

> AI 监控多个预言机价格源，检测偏差和操纵，异常时自动告警并建议协议参数调整

## 解决什么问题

预言机是 DeFi 协议的核心基础设施，价格偏差或操纵可导致错误清算、套利攻击和资金损失。单一预言机源存在单点风险，而现有监控方案缺乏智能化异常检测能力。本项目聚合 Sui 生态多个预言机源，利用 AI 模型实时检测价格异常和操纵行为，为协议提供早期预警和自动化参数调整建议。

## 核心功能

- 同时接入 Pyth、Supra、Switchboard 等多个 Sui 预言机源，实时对比价格数据
- AI 模型检测价格偏差、延迟异常、操纵模式（如闪电贷引发的价格尖峰）
- 异常事件链上记录，自动发出告警通知（链上 Event + 链下推送）
- 基于异常严重度建议协议参数调整（调整清算阈值、暂停特定交易对）

## 使用的 Sui 原语

| 原语 | 用途 |
|------|------|
| Move 对象模型 | `OracleWatchdog` 对象存储监控配置、偏差阈值和异常记录 |
| PTB | 原子化执行「读取多预言机价格 → 比对偏差 → 记录异常 → 调整参数」 |
| 事件（Event） | 发出 `PriceAnomalyEvent` 包含偏差详情和建议操作 |
| 动态字段 | 为每个监控的预言机源存储独立配置和历史数据 |

## 实现方案

### 智能合约 (Move)
- **`oracle_watchdog` 模块**：定义 `OracleWatchdog` 对象，字段包括 `sources: vector<address>`、`max_deviation_bp: u64`、`last_check_ts: u64`、`anomaly_count: u64`
- **`price_snapshot` 模块**：`record_prices(watchdog: &mut OracleWatchdog, prices: vector<CoinPrice>)` 在链上记录价格快照用于审计
- **`param_advisor` 模块**：`suggest_adjustment(watchdog: &OracleWatchdog, severity: u8)` 根据异常严重度生成参数调整建议
- 关键数据结构：`PriceAnomaly { source_a: address, source_b: address, deviation_bp: u64, timestamp: u64, severity: u8 }`

### 前端 / 后端
- **前端**：React + TailwindCSS，多预言机价格对比图表、异常事件时间线、协议参数建议面板
- **后端**：TypeScript Agent 服务，定时轮询各预言机源价格，调用 AI 模型分析偏差模式
- 关键页面：实时价格监控、异常历史、协议参数调整建议、告警配置

### AI / Agent 部分
- 统计模型：计算多源价格的加权均值和标准差，检测偏离阈值的异常
- 时序分析：LSTM 模型学习价格时序模式，识别异常跳变（非正常波动）
- Agent 工作流：每 10 秒轮询预言机 → 统计分析 + LSTM 推理 → 异常分级 → 链上记录 + 告警推送

## 技术架构

Agent 服务定时通过 Sui RPC 读取多个预言机 CoinPrice 对象的当前价格，构建多源价格向量。统计层计算偏差百分位，LSTM 层分析时序异常。检测到异常时构造 PTB：读取 OracleWatchdog 对象 → 写入 PriceAnomaly 记录 → 发出 Event。前端通过 WebSocket 订阅链上异常事件实时展示。协议方可订阅告警并在确认后通过 PTB 执行参数调整。

## 难度评估

| 维度 | 难度 | 说明 |
|------|------|------|
| Move 合约 | ⭐⭐⭐ | 多预言机源对接和偏差计算逻辑 |
| 前端 | ⭐⭐ | 价格图表 + 事件时间线，中等复杂度 |
| AI/ML | ⭐⭐⭐ | LSTM 时序模型训练和实时推理 |
| 集成复杂度 | ⭐⭐⭐ | 需要对接多个预言机协议的不同接口 |

## 官方参赛要求检查

- [ ] 项目名称和描述
- [ ] 项目 Logo (1:1)
- [ ] 公开 GitHub 仓库
- [ ] Demo 视频 (≤5min)
- [ ] 测试网部署 + Package ID
