# 定投机器人 / DCA Bot

> 设定币种、金额、频率，自动执行 DCA 策略，通过 PTB 原子化完成

## 解决什么问题

定投（Dollar-Cost Averaging）是经过验证的投资策略，但手动执行需要用户每次都登录钱包执行交易，容易忘记或受情绪影响偏离计划。现有链上定投工具功能单一，不支持多币种和灵活频率。定投机器人让用户一次设置后自动按计划执行定投，通过 PTB 原子化完成从授权扣款到代币兑换的全流程。

## 核心功能

- 灵活定投计划：自定义投资币种、支付币种、金额和频率（日/周/月）
- 自动执行：Keeper 按计划自动通过 PTB 执行代币兑换
- 多策略管理：可同时运行多个定投计划（如每周买 SUI + 每月买 BTC）
- 智能暂停：价格异常波动时自动暂停定投，避免在高点买入

## 使用的 Sui 原语

| 原语 | 用途 |
|------|------|
| Move 对象模型 | DCAPlan 作为独立 Object，存储计划参数和执行状态 |
| PTB | 原子化执行"从授权账户扣款 → DEX 兑换 → 转入用户钱包" |
| Coin 标准 | 支持任意代币对的定投 |
| 动态字段 | 存储执行历史、累计投入和累计持有量 |
| 对象所有权 | DCAPlan 对象归用户所有，用户可修改或取消 |

## 实现方案

### 智能合约 (Move)
- `dca_factory` 模块：创建定投计划，锁定授权资金
- `execution_engine` 模块：验证执行条件，通过 DEX 执行兑换
- `fund_manager` 模块：管理用户授权的资金池
- `pause_guard` 模块：异常价格检测和定投暂停逻辑
- 关键数据结构：`DCAPlan { owner: address, pay_token: Type, buy_token: Type, amount_per_execution: u64, frequency: u64, next_execution: u64, funded: Coin<T> }`

### 前端 / 后端
- 技术栈：React + @mysten/dapp-kit + Recharts
- 关键页面：Create DCA（创建定投）、Active Plans（活跃计划）、Performance（收益分析）、Fund Management（资金管理）
- 后端 Keeper 按计划触发 PTB 执行

## 技术架构

用户通过 PTB 创建定投计划：将一定量资金锁定到 DCAPlan 对象中，设定买入币种、金额和频率。Keeper 服务按计划时间触发执行：从 DCAPlan 资金池中扣除指定金额 → 通过 DeepBook 在 DEX 上兑换为目标代币 → 将购买的代币转入用户钱包。每次执行记录在动态字段中。用户可随时补充资金、调整参数或取消计划。pause_guard 在价格异常波动时自动暂停执行，保护用户不在极端高点买入。

## 难度评估

| 维度 | 难度 | 说明 |
|------|------|------|
| Move 合约 | ⭐⭐⭐ | 定投逻辑清晰，资金管理需要注意安全 |
| 前端 | ⭐⭐⭐ | 收益分析和计划管理需要清晰的 UI |
| 集成复杂度 | ⭐⭐⭐ | 需要对接 DEX 和可靠的 Keeper 服务 |

## 官方参赛要求检查

- [ ] 项目名称和描述
- [ ] 项目 Logo (1:1)
- [ ] 公开 GitHub 仓库
- [ ] Demo 视频 (≤5min)
- [ ] 测试网部署 + Package ID
