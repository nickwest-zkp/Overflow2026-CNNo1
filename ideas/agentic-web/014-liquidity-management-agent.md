# Liquidity Management Agent / 流动性管理 Agent

> 自动调整 DEX LP 仓位范围，AI 根据波动率预测建议集中或分散流动性

## 解决什么问题

在集中流动性 AMM 中，LP（流动性提供者）需要持续监控价格区间并手动调整仓位，否则资金效率低下且面临无常损失风险。波动率变化时最优区间也随之变化，普通用户缺乏专业知识和时间进行精细管理。本项目利用 AI Agent 预测价格波动率，自动调整 LP 仓位范围，在降低无常损失的同时最大化手续费收益。

## 核心功能

- 实时监控用户在 DeepBook 等 DEX 上的 LP 仓位状态（当前区间、集中度、收益）
- AI 预测交易对短期波动率，建议最优流动性区间（集中/分散）
- 自动执行 LP 仓位调整：撤出旧仓位 → 重新设定区间 → 注入新仓位，PTB 原子化
- 无常损失追踪和收益分析仪表盘

## 使用的 Sui 原语

| 原语 | 用途 |
|------|------|
| DeepBook | LP 仓位管理和流动性提供/撤出 |
| PTB | 原子化执行「撤出旧 LP → 调整区间 → 注入新 LP」 |
| Move 对象模型 | `LPStrategy` 对象存储仓位配置和再平衡规则 |
| Coin 管理 | LP Token 和流动性 Coin 的精确操作 |

## 实现方案

### 智能合约 (Move)
- **`lp_strategy` 模块**：定义 `LPStrategy` 对象，字段包括 `owner: address`、`pool_id: ID`、`current_tick_lower: i64`、`current_tick_upper: i64`、`rebalance_threshold_bp: u64`、`auto_rebalance: bool`、`total_fees_earned: u256`、`last_rebalance_epoch: u64`
- **`rebalance_executor` 模块**：`execute_rebalance(strategy: &mut LPStrategy, pool: &mut Pool, new_tick_lower: i64, new_tick_upper: i64)` 原子化再平衡
- **`il_tracker` 模块**：`record_il(strategy: &mut LPStrategy, il_amount: u256)` 追踪无常损失
- 关键数据结构：`RebalanceRecord { epoch: u64, old_range: (i64, i64), new_range: (i64, i64), fees_harvested: u256, il_at_rebalance: i256 }`

### 前端 / 后端
- **前端**：React + TradingView，LP 仓位可视化（区间在价格轴上的展示）、再平衡历史、收益分析
- **后端**：Python Agent 服务，波动率建模、再平衡决策引擎、PTB 构造
- 关键功能：仓位热力图、波动率预测曲线、收益 vs 无常损失对比

### AI / Agent 部分
- 波动率预测：GARCH 模型 + LSTM 混合模型预测短期价格波动率
- 最优区间计算：基于波动率预测和当前手续费收益计算最优 tick 范围
- 再平衡决策：AI 综合评估 Gas 成本、无常损失风险和收益提升空间决定是否触发再平衡
- Agent 工作流：每小时更新市场数据 → 波动率预测 → 区间优化 → 评估再平衡收益 → 构造 PTB

## 技术架构

Agent 服务每小时从 DeepBook 获取交易对的价格、成交量和流动性分布数据。GARCH+LSTM 混合模型预测未来 24 小时波动率，结合当前手续费率和用户风险偏好计算最优流动性区间。当新区间与当前仓位的偏差超过再平衡阈值时，Agent 构造 PTB（撤出旧 LP → 收割手续费 → 按新区间注入 LP），原子化提交执行。所有再平衡记录上链，前端实时展示仓位状态和收益分析。

## 难度评估

| 维度 | 难度 | 说明 |
|------|------|------|
| Move 合约 | ⭐⭐⭐⭐ | DeepBook LP 操作和再平衡逻辑 |
| 前端 | ⭐⭐⭐ | LP 区间可视化和收益图表 |
| AI/ML | ⭐⭐⭐⭐ | 波动率预测和最优区间模型 |
| 集成复杂度 | ⭐⭐⭐⭐ | DeepBook 深度集成和流动性数学 |

## 官方参赛要求检查

- [ ] 项目名称和描述
- [ ] 项目 Logo (1:1)
- [ ] 公开 GitHub 仓库
- [ ] Demo 视频 (≤5min)
- [ ] 测试网部署 + Package ID
