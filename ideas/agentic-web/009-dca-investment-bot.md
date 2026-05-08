# DCA Investment Bot / DCA 定投机器人

> 用户设定定投策略，Agent 自动通过 DeepBook 执行定期兑换，预算自限、可撤销

## 解决什么问题

链上定投操作需要用户手动定期执行代币兑换，容易遗忘或因情绪波动偏离计划。现有 DEX 聚合器缺乏自动化定投功能，用户无法设定"每周用 50 SUI 购买 USDC"这样的规则并自动执行。本项目利用 DeepBook 的链上订单簿和 PTB 原子化执行，让用户设定定投策略后由 Agent 自动在 DeepBook 上执行兑换，支持预算上限和随时撤销。

## 核心功能

- 用户创建 DCA 策略：设定投入代币、目标代币、金额、频率（每日/每周/每月）和总预算
- Agent 按计划自动通过 DeepBook 执行兑换，优先选择最优价格路径
- 预算自限机制：总投入不超过设定上限，单次消耗自动从预算余额扣除
- 随时暂停/撤销策略，已购入资产保留在用户钱包

## 使用的 Sui 原语

| 原语 | 用途 |
|------|------|
| DeepBook | 链上订单簿执行代币兑换，获取最优价格 |
| PTB | 原子化执行「从 DCA 策略扣预算 → DeepBook 下单 → 资产转入用户钱包」 |
| Move 对象模型 | `DCAStrategy` 对象存储策略配置、预算余额和执行历史 |
| Coin 管理 | 精确管理预算 Coin 对象的拆分和合并 |

## 实现方案

### 智能合约 (Move)
- **`dca_strategy` 模块**：定义 `DCAStrategy` 对象，字段包括 `owner: address`、`input_coin_type: Type`、`output_coin_type: Type`、`amount_per_round: u64`、`interval_epochs: u64`、`budget_remaining: Coin<SUI>`、`total_executed: u64`、`next_execution_epoch: u64`、`paused: bool`
- **`dca_executor` 模块**：`execute_dca(strategy: &mut DCAStrategy, deepbook_pool: &mut Pool)` 从策略预算中拆分 Coin，调用 DeepBook 下限价单执行兑换
- **`dca_manager` 模块**：`create_strategy()`、`pause_strategy()`、`resume_strategy()`、`withdraw_budget()` 管理策略生命周期
- 关键数据结构：`ExecutionRecord { epoch: u64, input_amount: u64, output_amount: u64, price: u64, gas_used: u64 }`

### 前端 / 后端
- **前端**：React + TailwindCSS，策略创建向导、执行历史图表、收益统计面板
- **后端**：TypeScript Agent 服务，定时检查待执行策略，构造并提交 PTB
- 关键页面：策略列表、新建策略、执行详情、收益分析

### AI / Agent 部分
- 执行调度：Agent 按策略频率定时唤醒，检查 epoch 是否到达执行窗口
- 价格优化：AI 分析 DeepBook 订单簿深度，推荐最优下单价格和时间点
- 策略建议：GPT-4o 根据用户风险偏好和市场状况推荐 DCA 参数（频率、金额、标的）
- Agent 工作流：定时扫描策略 → 筛选到期策略 → 分析订单簿 → 构造 PTB → 提交执行

## 技术架构

用户在前端创建 DCAStrategy 对象并存入预算 Coin。Agent 服务定时扫描所有活跃策略，筛选到达执行窗口的策略。对于每个到期策略，Agent 查询 DeepBook 订单簿获取当前最优价格，构造 PTB（拆分预算 Coin → DeepBook 下单 → 资产归集到用户地址）并提交。执行结果通过事件上链，前端实时更新策略状态和收益统计。用户可随时通过合约调用暂停策略或提取剩余预算。

## 难度评估

| 维度 | 难度 | 说明 |
|------|------|------|
| Move 合约 | ⭐⭐⭐ | DeepBook 集成和 Coin 对象精确管理 |
| 前端 | ⭐⭐ | 策略管理和收益展示，中等复杂度 |
| AI/ML | ⭐⭐ | 订单簿分析和策略建议 |
| 集成复杂度 | ⭐⭐⭐ | DeepBook 接口对接和定时执行引擎 |

## 官方参赛要求检查

- [ ] 项目名称和描述
- [ ] 项目 Logo (1:1)
- [ ] 公开 GitHub 仓库
- [ ] Demo 视频 (≤5min)
- [ ] 测试网部署 + Package ID
