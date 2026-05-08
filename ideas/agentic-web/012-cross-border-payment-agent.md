# Cross-border Payment Agent / 跨境支付 Agent

> AI 解析支付意图，自动选择最优路径（DEX 路由、桥接），通过 PTB 原子化完成多步支付

## 解决什么问题

跨境支付和跨链转账涉及多个中间步骤（法币兑换、链上路由、桥接、目标链确认），用户需要手动选择路径并分步执行，容易因路径选择不优而损失汇率。本项目利用 AI Agent 解析用户的自然语言支付意图，自动计算最优路径（综合汇率、Gas、速度和安全性），通过 Sui PTB 原子化完成整个支付流程。

## 核心功能

- AI 解析自然语言支付意图（"向 Bob 的以太坊地址发送 100 美元等值的 USDC"）
- 自动搜索最优路径：比较 DEX 路由、跨链桥方案，综合评估汇率、手续费和速度
- 通过 PTB 原子化执行多步操作（代币兑换 → 桥接 → 目标地址转账）
- 支付状态实时追踪，异常自动重试或回滚

## 使用的 Sui 原语

| 原语 | 用途 |
|------|------|
| PTB | 原子化执行整个支付流程：兑换 → 路由 → 桥接 → 转账 |
| DeepBook | 最优汇率兑换路径执行 |
| Coin 管理 | 支付金额的精确拆分、合并和类型转换 |
| 事件（Event） | 发出 `PaymentStatusEvent` 追踪支付各阶段状态 |

## 实现方案

### 智能合约 (Move)
- **`payment_intent` 模块**：定义 `PaymentIntent` 对象，字段包括 `sender: address`、`recipient: vector<u8>`、`amount: u64`、`input_coin_type: Type`、`output_coin_type: Type`、`destination_chain: String`、`status: u8`、`created_at: u64`
- **`payment_router` 模块**：`execute_payment(intent: &mut PaymentIntent, route: RouteProof)` 按选定路径执行支付
- **`payment_tracker` 模块**：`update_status(intent: &mut PaymentIntent, status: u8, tx_hash: vector<u8>)` 更新支付状态
- 关键数据结构：`PaymentRoute { dex_hops: vector<ID>, bridge_id: ID, estimated_output: u64, estimated_time_secs: u64, total_fee_bp: u64 }`

### 前端 / 后端
- **前端**：React + Next.js，自然语言输入框、路径选择可视化、支付状态追踪页
- **后端**：Python Agent 服务，意图解析、路径搜索、PTB 构造和提交
- 关键功能：聊天式支付界面、路径对比卡片、支付历史

### AI / Agent 部分
- 意图解析：GPT-4o 从自然语言中提取支付参数（金额、币种、目标链、目标地址）
- 路径优化：强化学习模型在历史支付数据上训练，综合评估路径成本
- Agent 工作流：解析意图 → 查询可用路径 → 排序比较 → 用户确认 → 构造 PTB → 提交追踪

## 技术架构

用户通过前端输入自然语言支付指令，GPT-4o 提取结构化支付参数。路径搜索引擎查询可用的 DEX 路由和跨链桥方案，RL 模型综合排序。用户确认路径后，Agent 构造 PTB（Coin 拆分 → DeepBook 兑换 → 桥接合约调用），原子化提交。支付各阶段通过 PaymentStatusEvent 上链追踪，前端实时展示进度。失败时 PTB 自动回滚，用户资金安全。

## 难度评估

| 维度 | 难度 | 说明 |
|------|------|------|
| Move 合约 | ⭐⭐⭐ | 支付意图管理和多路径执行逻辑 |
| 前端 | ⭐⭐⭐ | 聊天式界面和支付状态可视化 |
| AI/ML | ⭐⭐⭐ | 意图解析和路径优化 RL 模型 |
| 集成复杂度 | ⭐⭐⭐⭐ | 跨链桥对接和多路径编排 |

## 官方参赛要求检查

- [ ] 项目名称和描述
- [ ] 项目 Logo (1:1)
- [ ] 公开 GitHub 仓库
- [ ] Demo 视频 (≤5min)
- [ ] 测试网部署 + Package ID
