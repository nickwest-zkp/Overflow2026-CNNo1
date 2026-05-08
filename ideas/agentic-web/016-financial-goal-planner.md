# Financial Goal Planner / 财务目标规划器

> 用户描述储蓄目标，AI 编排定期买入 + 收益农耕的 PTB 策略

## 解决什么问题

用户有储蓄和投资目标（如"一年内存 5000 USDC"），但缺乏系统化的链上工具来规划和自动执行达成路径。手动操作需要定期买入、选择收益池、复投收益，步骤繁琐且难以坚持。本项目让用户用自然语言描述财务目标，AI 自动生成包含定期买入、收益农耕和复投的多步 PTB 策略，Agent 按计划自动执行并追踪进度。

## 核心功能

- 自然语言描述财务目标，AI 生成达成路径规划（定期买入频率、金额、收益池选择）
- 将规划编译为链上自动化策略：定期 DCA + 收益农耕 + 自动复投
- 目标进度实时追踪：已达成百分比、预计完成时间、偏差分析
- 策略动态调整：AI 根据市场变化推荐调整方案（增加投入、切换池、延长期限）

## 使用的 Sui 原语

| 原语 | 用途 |
|------|------|
| PTB | 编排多步操作：定期买入 → DeepBook 兑换 → 存入收益池 → 复投 |
| DeepBook | 定期代币兑换执行 |
| Move 对象模型 | `GoalPlan` 对象存储目标配置、进度和策略参数 |
| Coin 管理 | 目标资金的拆分、归集和复投操作 |

## 实现方案

### 智能合约 (Move)
- **`goal_plan` 模块**：定义 `GoalPlan` 对象，字段包括 `owner: address`、`target_amount: u256`、`current_amount: u256`、`target_coin_type: Type`、`deadline_epoch: u64`、`strategy_type: u8`、`auto_execute: bool`、`created_at: u64`
- **`plan_executor` 模块**：`execute_plan_step(plan: &mut GoalPlan, step: PlanStep)` 执行计划中的单步操作（买入/存入/复投）
- **`progress_tracker` 模块**：`update_progress(plan: &mut GoalPlan, new_balance: u256)` 更新目标进度
- 关键数据结构：`PlanStep { step_type: u8, amount: u64, target_pool: Option<ID>, frequency_epochs: u64, next_execution: u64 }`

### 前端 / 后端
- **前端**：React + ECharts，目标进度环形图、策略时间线、收益归因分析
- **后端**：Python Agent 服务，目标规划算法、策略生成、自动执行引擎
- 关键功能：目标创建向导、进度看板、策略调整建议

### AI / Agent 部分
- 目标规划：GPT-4o 根据用户描述和风险偏好生成多步达成计划
- 策略优化：线性规划模型分配资金到不同收益池，最大化预期收益
- 动态调整：AI 定期评估计划执行偏差，建议参数调整
- Agent 工作流：解析目标 → 生成计划 → 链上创建 GoalPlan → 定时执行步骤 → 追踪进度 → 动态调整

## 技术架构

用户通过前端描述财务目标（金额、币种、期限），GPT-4o 结合当前市场数据生成达成规划（DCA 计划 + 收益池分配 + 复投策略）。规划确认后创建 GoalPlan 链上对象并存入初始资金。Agent 服务按计划频率定时唤醒，构造 PTB 执行当期操作（从预算中拆分 Coin → DeepBook 兑换 → 存入推荐池）。每次执行后更新进度，AI 定期评估是否需要调整策略，前端实时展示目标进度环和建议。

## 难度评估

| 维度 | 难度 | 说明 |
|------|------|------|
| Move 合约 | ⭐⭐⭐ | 目标计划管理和多步执行逻辑 |
| 前端 | ⭐⭐⭐ | 进度可视化和目标管理界面 |
| AI/ML | ⭐⭐⭐ | 目标规划和资金分配优化算法 |
| 集成复杂度 | ⭐⭐⭐ | 多协议对接和定时执行调度 |

## 官方参赛要求检查

- [ ] 项目名称和描述
- [ ] 项目 Logo (1:1)
- [ ] 公开 GitHub 仓库
- [ ] Demo 视频 (≤5min)
- [ ] 测试网部署 + Package ID
