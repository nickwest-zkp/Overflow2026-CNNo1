# Agentic Web

**AI 原生智能体**

## Problem Statement / 核心命题

The AI track rewards projects that use Sui as a meaningful part of the AI stack — not as a payment rail bolted on at the end. Every submission must show why Sui specifically (Move objects, zkLogin, PTBs, DeepBook, Walrus, or Seal) makes the AI component better, safer, or more composable. Generic LLM wrappers that happen to hold SUI will not place.

本赛道奖励将 Sui 作为 AI 技术栈重要组成部分的项目——而不是最后拼接的支付通道。每个提交必须说明为什么 Sui（Move 对象、zkLogin、PTB、DeepBook、Walrus 或 Seal）能让 AI 组件更好、更安全、更可组合。碰巧持有 SUI 的通用 LLM 套壳不会入选。

## Prize / 奖金

| Place | Prize |
|-------|-------|
| 1st | $30,000 |
| 2nd | $15,000 |
| 3rd | $10,000 |
| 4th | $7,500 |

## 参考方向 / Suggested Topics

### 1. 自主风险监控与链上干预 / Autonomous Risk Monitor

DeFi 协议依赖静态风险参数，闪电崩盘时几秒内就会失效。构建一个 Sui 借贷或永续协议的实时风险监控器，接入预言机价格馈送，运行 AI 风险模型，并通过 Move 策略对象自主执行参数调整或市场暂停——所有操作上链记录，可由 DAO 覆盖。

**要求：** 实时价格馈送、可视化 AI 风险评分、至少一个自主链上操作、人工覆盖机制。

### 2. AI Agent 钱包 / Agent Wallet with zkLogin

AI 智能体被困在"审批墙"之后——每个操作都需要人类签名。在 Sui 上使用 zkLogin 或 Move 策略对象构建 Agent 钱包，为 AI Agent 分配有上限的预算和协议范围（如"最多 500 USDC，仅限 DeepBook，24 小时有效"）。Agent 必须自主执行策略、执行预算上限、并生成链上活动日志。必须可演示所有者撤销。

**要求：** 真实 DeepBook 订单、自执行预算上限、链上活动日志、所有者撤销演示。

### 3. 自然语言意图引擎 / Intent Engine with Guardian

用户不应该需要知道什么是流动性池。构建一个意图引擎，解析自然语言金融目标，编译为 Sui PTB，在签名前运行守护检查，用通俗语言展示风险（高滑点、集中度、过时池）。用户必须明确确认后才会执行。没有守护层的兑换聊天机器人不是意图引擎。

**要求：** 文本 → PTB → 执行流程、人类可读的 PTB 预览、守护层至少捕获 2 种风险类别、明确确认步骤。

## 评估要点 / Evaluation Criteria

- 项目必须展示 Sui 在 AI 系统中的**具体价值**（而非仅作为支付通道）
- 优先考虑真正利用 Sui 原语的智能系统
