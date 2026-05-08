# 稳定币收益聚合器 / Stablecoin Yield Aggregator

> 自动在 Sui 上的稳定币收益池间轮动，最大化 USDC/USDT 收益

## 解决什么问题

Sui 生态中有多个稳定币收益来源（Scallop 借贷、Cetus 流动性池、Aftermath 金库等），APY 各不相同且实时变化。用户手动比较和切换效率低下，错过最优收益窗口。稳定币收益聚合器自动监控所有稳定币收益池的 APY，将用户资金自动轮动到最高收益的池子，持续最大化稳定币收益。

## 核心功能

- APY 实时监控：持续追踪 Sui 上所有稳定币收益池的实时 APY
- 自动轮动策略：当最优池变更时自动将资金从低收益池转移到高收益池
- 多稳定币支持：支持 USDC、USDT、 AUSD 等多种稳定币
- Gas 优化轮动：考虑 Gas 成本和滑点，只在净收益为正时执行轮动

## 使用的 Sui 原语

| 原语 | 用途 |
|------|------|
| Move 对象模型 | YieldAggregator 作为共享对象，管理用户资金和策略 |
| PTB | 原子化执行"从旧池提取 → 兑换 → 存入新池"轮动操作 |
| Coin 标准 | 统一处理多种稳定币 |
| 动态字段 | 存储各池子 APY、用户份额和轮动历史 |
| 共享对象 | 聚合器资金池允许多用户并发存取 |

## 实现方案

### 智能合约 (Move)
- `aggregator` 模块：管理用户存款和份额代币的铸造/销毁
- `pool_registry` 模块：注册和追踪各稳定币收益池的 APY
- `rotation_strategy` 模块：决定何时从哪个池子轮动到哪个池子
- `protocol_adapters` 模块：为每个 DeFi 协议提供适配器接口
- 关键数据结构：`AggregatorVault { stablecoin_type: Type, total_deposits: Coin<T>, active_pool: PoolInfo, share_supply: u64 }`

### 前端 / 后端
- 技术栈：Next.js + @mysten/dapp-kit + Sui TypeScript SDK
- 关键页面：Dashboard（收益总览）、Deposit/Withdraw（存取款）、Pool Comparison（池子对比）、Rotation Log（轮动记录）
- 后端持续监控 APY 变化并触发轮动

## 技术架构

用户存入稳定币后，合约铸造份额代币并记录存款。后端 APY 监控服务持续追踪各稳定币池的收益率，当发现更优池子时通过 PTB 执行轮动：从当前池提取全部资金 → 如需兑换则通过 DEX 执行 → 存入新池。轮动前计算 Gas 成本和滑点，确保净收益为正。每次轮动记录在链上动态字段中。前端展示用户累计收益、当前 APY 和轮动历史。

## 难度评估

| 维度 | 难度 | 说明 |
|------|------|------|
| Move 合约 | ⭐⭐⭐⭐ | 多协议适配器和轮动逻辑需要安全设计 |
| 前端 | ⭐⭐⭐ | APY 对比和收益展示需要清晰的数据可视化 |
| 集成复杂度 | ⭐⭐⭐⭐ | 需要对接多个 DeFi 协议的存款/提取接口 |

## 官方参赛要求检查

- [ ] 项目名称和描述
- [ ] 项目 Logo (1:1)
- [ ] 公开 GitHub 仓库
- [ ] Demo 视频 (≤5min)
- [ ] 测试网部署 + Package ID
