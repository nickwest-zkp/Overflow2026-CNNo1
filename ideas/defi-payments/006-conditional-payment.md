# 条件支付协议 / Conditional Payment Protocol

> "如果预言机价格达到 X，则自动支付 Y 给 Z"，全部通过 Move 合约执行

## 解决什么问题

传统条件支付（如限价买入、止盈卖出、事件触发的转账）需要依赖中心化交易所或第三方服务来监控条件并执行。用户必须信任平台会如实执行，且这些服务通常收取高额手续费。条件支付协议将触发条件和支付逻辑完全编码在 Move 合约中，由 Keeper 网络监控条件并触发自动执行，消除对第三方的信任依赖。

## 核心功能

- 条件表达式引擎：支持价格条件、时间条件、组合条件（AND/OR）的声明式定义
- 自动触发支付：条件满足时，Keeper 通过 PTB 自动执行资金转移
- 条件组合模板：提供常见模板（止盈止损、DCA 触发、锚定偏差套利）
- 委托执行权限：用户将有限的执行权限委托给 Keeper 网络

## 使用的 Sui 原语

| 原语 | 用途 |
|------|------|
| Move 对象模型 | ConditionalPayment 作为独立 Object，存储条件和资金 |
| PTB | 原子化执行"条件检查 → 资金释放"复合操作 |
| 预言机集成 | 通过 Pyth/Wormhole 获取链上价格数据 |
| 动态字段 | 存储条件表达式、触发历史和执行参数 |
| 共享对象 | 支付条件注册表作为共享对象，供 Keeper 读取 |

## 实现方案

### 智能合约 (Move)
- `conditional_payment` 模块：创建条件支付订单，锁定资金并定义触发条件
- `condition_engine` 模块：条件表达式解析和评估逻辑
- `keeper_registry` 模块：管理 Keeper 注册、绩效追踪和奖励
- `oracle_adapter` 模块：适配 Pyth 和其他预言机数据源
- 关键数据结构：`ConditionalPayment { amount: Coin<T>, recipient: address, condition: Condition, created_at: u64, status: u8 }`

### 前端 / 后端
- 技术栈：Next.js + @mysten/dapp-kit + TailwindCSS
- 关键页面：Create Payment（创建条件支付）、Active Orders（活跃订单）、History（执行历史）、Templates（条件模板库）
- 后端运行 Keeper 服务，监控共享对象中的条件并触发执行

## 技术架构

用户通过前端定义条件（如"SUI 价格 > $5"）和支付目标，通过 PTB 将资金锁定到 ConditionalPayment 对象中。Keeper 服务持续监控链上条件注册表和预言机价格，当条件满足时通过 PTB 原子化执行条件检查和资金释放。条件引擎支持组合逻辑（如价格条件 AND 时间条件）。执行成功后 Keeper 获得奖励，资金转入接收方地址。所有操作记录在链上事件中。

## 难度评估

| 维度 | 难度 | 说明 |
|------|------|------|
| Move 合约 | ⭐⭐⭐⭐ | 条件表达式引擎需要灵活且安全的设计 |
| 前端 | ⭐⭐⭐ | 条件编辑器需要直观的 UI |
| 集成复杂度 | ⭐⭐⭐⭐ | 需要对接预言机和可靠的 Keeper 网络 |

## 官方参赛要求检查

- [ ] 项目名称和描述
- [ ] 项目 Logo (1:1)
- [ ] 公开 GitHub 仓库
- [ ] Demo 视频 (≤5min)
- [ ] 测试网部署 + Package ID
