# 止盈止损 Agent / Take-Profit Stop-Loss Agent

> 设定止盈止损条件，Agent 24/7 监控价格并自动执行

## 解决什么问题

加密货币市场 24/7 运行，价格波动剧烈。投资者无法时刻盯盘，常常错过止盈时机或来不及止损。传统止盈止损功能只在中心化交易所提供，链上用户缺乏等效工具。止盈止损 Agent 让用户在链上设定止盈止损条件，Agent 24/7 监控价格，当条件触发时通过 PTB 自动执行卖出，保护投资收益。

## 核心功能

- 双向止盈止损：同时设置止盈价和止损价，先触发的执行后另一个自动取消
- 拖拽止损：价格上涨时止损线自动跟随上移，锁定更多利润
- 多仓位管理：同时为多个代币持仓设置止盈止损
- 执行通知：条件触发执行后实时通知用户交易结果

## 使用的 Sui 原语

| 原语 | 用途 |
|------|------|
| Move 对象模型 | TPSLOrder 作为独立 Object，存储止盈止损参数和资金 |
| PTB | 原子化执行"价格检查 → DEX 卖出 → 通知 → 取消配对订单" |
| Coin 标准 | 处理持仓代币的锁定和卖出 |
| 预言机集成 | 通过 Pyth 获取实时价格判断触发 |
| 动态字段 | 存储拖拽止损的历史轨迹和执行记录 |

## 实现方案

### 智能合约 (Move)
- `order_manager` 模块：创建和管理止盈止损订单
- `price_trigger` 模块：验证价格是否触达止盈或止损条件
- `execution_engine` 模块：通过 DEX 执行卖出操作
- `trailing_stop` 模块：实现拖拽止损逻辑，动态调整止损价
- 关键数据结构：`TPSLOrder { owner: address, token: Type, amount: u64, take_profit_price: u64, stop_loss_price: u64, trailing: bool, trailing_percent: u64, status: u8 }`

### 前端 / 后端
- 技术栈：React + @mysten/dapp-kit + Push Notification
- 关键页面：Create Order（创建止盈止损）、Active Orders（活跃订单）、Trailing Stop Config（拖拽止损配置）、Execution Log（执行日志）
- 后端 Agent 24/7 监控价格并触发执行

## 技术架构

用户通过 PTB 将持仓代币锁定到 TPSLOrder 对象中，设置止盈价、止损价和拖拽参数。Agent 24/7 监控预言机价格：当市价触达止盈价时，通过 PTB 在 DEX 卖出并取消止损单；触达止损价时同理。如果启用拖拽止损，当价格上涨时 Agent 自动通过 PTB 调高止损价。执行完成后通过事件通知前端，前端推送移动端通知告知用户结果。

## 难度评估

| 维度 | 难度 | 说明 |
|------|------|------|
| Move 合约 | ⭐⭐⭐ | 止盈止损逻辑清晰，拖拽止损增加复杂度 |
| 前端 | ⭐⭐⭐ | 需要直观的价格设置和拖拽配置界面 |
| 集成复杂度 | ⭐⭐⭐⭐ | 需要可靠的 24/7 Agent 服务和预言机集成 |

## 官方参赛要求检查

- [ ] 项目名称和描述
- [ ] 项目 Logo (1:1)
- [ ] 公开 GitHub 仓库
- [ ] Demo 视频 (≤5min)
- [ ] 测试网部署 + Package ID
