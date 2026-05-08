# Multi-Strategy Trading Agent / 多策略交易 Agent

> Agent 根据市场信号在 DeepBook 上执行多种交易策略，每笔交易受 Move 策略对象约束

## 解决什么问题

链上交易策略执行需要用户持续盯盘和手动操作，无法同时运行多个策略。现有交易机器人缺乏链上约束机制，存在策略失控和超支风险。本项目在 Sui 上构建多策略交易 Agent 框架，每个策略通过 Move 策略对象设定约束条件（止损、仓位上限、频率），Agent 根据市场信号自动在 DeepBook 上执行交易，确保每笔操作都在链上约束范围内。

## 核心功能

- 支持多种交易策略：网格交易、均值回归、动量跟踪、套利等，用户可组合启用
- 每个策略通过 Move 策略对象设定约束：最大仓位、止损线、最大单笔金额、冷却期
- Agent 根据实时市场信号（价格、深度、交易量）决定是否触发策略执行
- 策略执行记录和绩效分析仪表盘

## 使用的 Sui 原语

| 原语 | 用途 |
|------|------|
| DeepBook | 在链上订单簿执行买卖订单 |
| PTB | 每笔交易打包为 PTB：检查约束 → 下单 → 记录，原子化执行 |
| Move 对象模型 | `TradingStrategy` 对象存储策略参数和约束条件 |
| 能力（Capability） | `TraderCapability` 授权 Agent 调用受限交易函数 |
| 事件（Event） | 发出 `TradeExecutedEvent` 记录每次交易详情 |

## 实现方案

### 智能合约 (Move)
- **`trading_strategy` 模块**：定义 `TradingStrategy` 对象，字段包括 `owner: address`、`strategy_type: u8`、`max_position: u256`、`stop_loss_bp: u64`、`max_per_trade: u64`、`cooldown_epochs: u64`、`current_position: u256`、`pnl: i256`、`enabled: bool`
- **`trade_guard` 模块**：`pre_trade_check(strategy: &TradingStrategy, amount: u64, side: u8): bool` 链上约束校验
- **`trade_executor` 模块**：`execute_trade(strategy: &mut TradingStrategy, pool: &mut DeepBook::Pool, amount: u64, side: u8)` 在约束校验通过后执行 DeepBook 交易
- **`strategy_registry` 模块**：管理用户的所有活跃策略和总仓位统计
- 关键数据结构：`TradeRecord { strategy_id: ID, side: u8, amount: u256, price: u64, pnl: i256, timestamp: u64 }`

### 前端 / 后端
- **前端**：React + TradingView 组件，策略配置界面、K 线图叠加策略信号、持仓和 PnL 实时展示
- **后端**：Python Agent 服务，市场数据采集、策略信号生成、PTB 构造和提交
- 关键组件：行情数据管道、策略引擎、风控模块

### AI / Agent 部分
- 策略信号生成：每种策略类型使用独立模型（均值回归用统计套利模型，动量用 LSTM）
- 风控 AI：GPT-4o 在异常市场条件下评估策略是否应暂停
- Agent 工作流：实时行情订阅 → 策略信号计算 → 风控评估 → 构造 PTB（约束校验 + 下单）→ 提交执行

## 技术架构

Agent 服务通过 WebSocket 订阅 DeepBook 实时行情数据（价格、深度、成交）。各策略引擎独立计算交易信号，通过风控模块评估后进入执行队列。每笔交易构造为 PTB：先调用 trade_guard 做链上约束校验（仓位上限、止损、冷却期），通过后调用 DeepBook 下单。交易结果通过 TradeExecutedEvent 上链，前端实时更新持仓和 PnL。策略参数修改需要 owner 签名确认。

## 难度评估

| 维度 | 难度 | 说明 |
|------|------|------|
| Move 合约 | ⭐⭐⭐⭐ | 策略约束模型和 DeepBook 集成复杂 |
| 前端 | ⭐⭐⭐ | TradingView 集成和策略可视化 |
| AI/ML | ⭐⭐⭐⭐ | 多策略信号模型训练和实时推理 |
| 集成复杂度 | ⭐⭐⭐⭐ | 实时行情、策略引擎和 DeepBook 深度集成 |

## 官方参赛要求检查

- [ ] 项目名称和描述
- [ ] 项目 Logo (1:1)
- [ ] 公开 GitHub 仓库
- [ ] Demo 视频 (≤5min)
- [ ] 测试网部署 + Package ID
