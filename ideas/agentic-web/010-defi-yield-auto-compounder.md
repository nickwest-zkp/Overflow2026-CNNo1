# DeFi Yield Auto-Compounder / DeFi 收益自动复投

> Agent 监控收益池 APY 变化，自动将收益复投到最优池，通过 PTB 原子化执行

## 解决什么问题

DeFi 用户需要手动领取收益并重新投入最优池，操作繁琐且容易错过高收益窗口。收益池 APY 实时变化，用户难以及时发现和切换到更优策略。本项目利用 AI Agent 实时监控 Sui 生态收益池 APY 变化，自动将已产生的收益复投到当前最优池，通过 PTB 原子化执行「领取收益 → 兑换 → 重新投入」全流程，最大化用户收益。

## 核心功能

- 实时聚合 Sui 生态收益池（Scallop、Navi、Aftermath 等）APY 和 TVL 数据
- AI 模型预测短期 APY 走势，推荐最优复投目标池
- 自动执行「领取收益 → DEX 兑换 → 存入目标池」PTB，原子化不可分割
- 复投策略可配置：最低复投金额、目标池筛选条件、风险偏好

## 使用的 Sui 原语

| 原语 | 用途 |
|------|------|
| PTB | 原子化执行「领取收益 → DeepBook 兑换 → 存入目标池」，保证要么全部成功要么全部回滚 |
| DeepBook | 收益代币兑换为目标池所需代币 |
| Move 对象模型 | `CompoundStrategy` 对象存储复投配置和执行统计 |
| Coin 管理 | 收益 Coin 的拆分、合并和类型转换 |

## 实现方案

### 智能合约 (Move)
- **`compound_strategy` 模块**：定义 `CompoundStrategy` 对象，字段包括 `owner: address`、`source_pools: vector<ID>`、`target_pool_criteria: u8`、`min_compound_amount: u64`、`max_slippage_bp: u64`、`total_compounded: u256`、`last_compound_epoch: u64`
- **`yield_harvester` 模块**：`harvest_and_compound(strategy: &mut CompoundStrategy, proof: HarvestProof)` 在单个 PTB 中完成收益领取和复投
- **`pool_registry` 模块**：维护收益池白名单和元数据，供策略引用
- 关键数据结构：`CompoundRecord { epoch: u64, amount_harvested: u256, amount_compounded: u256, target_pool: ID, apy_at_execution: u64 }`

### 前端 / 后端
- **前端**：React + ECharts，收益池 APY 对比图、复投历史时间线、收益增长曲线
- **后端**：Rust Agent 服务，聚合各协议收益数据，运行 APY 预测模型，管理复投执行队列
- 关键功能：策略配置器、APY 看板、收益计算器

### AI / Agent 部分
- APY 预测：时序 Transformer 模型预测未来 24-72 小时 APY 走势
- 池选择优化：多目标优化算法平衡 APY、风险、Gas 成本和滑点
- Agent 工作流：每小时更新 APY 数据 → 预测模型推理 → 评估待复投策略 → 构造 PTB → 执行复投

## 技术架构

Agent 服务通过 Sui RPC 和各协议索引器每小时更新所有收益池的 APY、TVL 和风险指标。Transformer 模型预测短期 APY 走势，多目标优化算法为每个用户的 CompoundStrategy 选择最优目标池。当收益累积超过最低复投金额时，Agent 构造 PTB（调用源池领取收益 → DeepBook 兑换代币 → 调用目标池存入），原子化提交。执行结果上链，前端实时展示收益增长曲线。

## 难度评估

| 维度 | 难度 | 说明 |
|------|------|------|
| Move 合约 | ⭐⭐⭐⭐ | 多协议收益领取接口对接和 Coin 类型泛型处理 |
| 前端 | ⭐⭐⭐ | APY 图表和收益可视化 |
| AI/ML | ⭐⭐⭐ | APY 时序预测和多目标池选择优化 |
| 集成复杂度 | ⭐⭐⭐⭐ | 需对接 Scallop/Naviprotocol/Aftermath 多个协议 |

## 官方参赛要求检查

- [ ] 项目名称和描述
- [ ] 项目 Logo (1:1)
- [ ] 公开 GitHub 仓库
- [ ] Demo 视频 (≤5min)
- [ ] 测试网部署 + Package ID
