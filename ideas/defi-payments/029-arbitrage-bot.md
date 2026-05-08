# 套利机器人 / Arbitrage Bot

> 监控 Sui DEX 间价差，发现机会时通过 PTB 原子化套利

## 解决什么问题

Sui 生态中有多个 DEX（DeepBook、Cetus、Turbos 等），同一种代币在不同 DEX 上价格存在差异。这种价差代表无风险套利机会，但手动捕捉几乎不可能——价差转瞬即逝且需要同时在不同 DEX 上执行买卖操作。套利机器人持续监控各 DEX 价格，发现价差时通过 PTB 原子化执行套利交易，确保买卖同时完成，消除单边风险。

## 核心功能

- 全市场价差监控：实时监控 Sui 上所有 DEX 的代币对价格
- 自动套利执行：发现价差时通过 PTB 原子化执行买入和卖出
- 多路径套利：支持三角套利（A→B→C→A）和跨 DEX 直接套利
- 收益追踪：记录每笔套利的利润、Gas 消耗和净收益

## 使用的 Sui 原语

| 原语 | 用途 |
|------|------|
| PTB | 原子化执行"DEX A 买入 → DEX B 卖出"或三角路径套利 |
| Coin 标准 | 处理多种代币的兑换 |
| 共享对象 | 套利策略注册表作为共享对象 |
| 动态字段 | 存储支持的 DEX 列表、交易对和手续费 |
| 事件 (Events) | 发出套利执行事件记录利润 |

## 实现方案

### 智能合约 (Move)
- `arb_executor` 模块：执行套利路径的买卖操作
- `path_finder` 模块：计算最优套利路径（直接/三角/多跳）
- `profit_calculator` 模块：扣除 Gas 和手续费后计算净收益
- `dex_adapters` 模块：为每个 DEX 提供统一的交易接口
- 关键数据结构：`ArbPath { steps: vector<SwapStep>, expected_profit: u64, dex_addresses: vector<ID> }`

### 前端 / 后端
- 技术栈：Next.js + @mysten/dapp-kit + WebSocket
- 关键页面：Arbitrage Dashboard（套利总览）、Price Matrix（价格矩阵）、Execution Log（执行日志）、Profit Analytics（收益分析）
- 后端高频监控价格并发现套利机会

## 技术架构

后端价格监控服务通过 WebSocket 订阅各 DEX 的池子状态，构建实时价格矩阵。当发现价差超过阈值（扣除 Gas 和手续费后仍有正收益）时，path_finder 计算最优路径（如 DeepBook 买入 SUI → Cetus 卖出 SUI）。构建 PTB 原子化执行所有步骤：如果任何步骤失败则整个交易回滚，确保无单边风险。套利利润自动累积在合约中，用户可提取。

## 难度评估

| 维度 | 难度 | 说明 |
|------|------|------|
| Move 合约 | ⭐⭐⭐⭐ | 多 DEX 适配器和原子化路径执行需要精细设计 |
| 前端 | ⭐⭐⭐ | 价格矩阵和实时更新需要高效渲染 |
| 集成复杂度 | ⭐⭐⭐⭐⭐ | 需要对接所有 Sui DEX 的交易接口 |

## 官方参赛要求检查

- [ ] 项目名称和描述
- [ ] 项目 Logo (1:1)
- [ ] 公开 GitHub 仓库
- [ ] Demo 视频 (≤5min)
- [ ] 测试网部署 + Package ID
