# zkLogin Agent Wallet / zkLogin Agent 钱包

> 使用 zkLogin 为 AI Agent 创建受限钱包（预算上限、协议白名单、时间限制），链上活动全记录

## 解决什么问题

AI Agent 在执行链上操作时缺乏安全的身份认证和权限控制机制。直接使用私钥的 Agent 存在被滥用、超支和执行未授权操作的风险。项目方难以追踪和管理多个 Agent 的链上行为。本项目利用 Sui 的 zkLogin 原语为 AI Agent 创建链上身份，通过 Move 策略对象限制 Agent 的操作权限和预算，实现安全可审计的 Agent 自主执行。

## 核心功能

- 使用 zkLogin 为 AI Agent 生成链上身份（关联 OAuth 账户），无需管理原始私钥
- 创建受限 Agent 钱包：设定预算上限、协议白名单、单笔交易限额、有效期
- Agent 链上操作全记录，支持按 Agent ID 查询交易历史和行为统计
- 紧急冻结功能：发现异常时一键冻结 Agent 钱包所有操作

## 使用的 Sui 原语

| 原语 | 用途 |
|------|------|
| zkLogin | 为 Agent 创建基于 OAuth 的链上身份，替代私钥认证 |
| Move 对象模型 | `AgentWallet` 对象存储权限配置、预算余额和白名单 |
| PTB | Agent 操作打包为 PTB 执行，每步可校验权限约束 |
| 能力（Capability） | `AgentCapability` 授权对象控制 Agent 可调用的合约函数 |
| 共享对象 | `AgentRegistry` 共享对象维护所有已注册 Agent 的索引 |

## 实现方案

### 智能合约 (Move)
- **`agent_wallet` 模块**：定义 `AgentWallet` 对象，字段包括 `agent_id: ID`、`owner: address`、`budget_remaining: Coin<SUI>`、`max_per_tx: u64`、`whitelisted_protocols: vector<ID>`、`expires_at: u64`、`frozen: bool`
- **`agent_capability` 模块**：定义 `AgentCapability` 能力对象，约束 Agent 可调用的特定函数集合
- **`agent_registry` 模块**：`register_agent(registry: &mut AgentRegistry, zklogin_id: vector<u8>, wallet: AgentWallet)` 注册新 Agent
- **`guard` 模块**：`check_permission(wallet: &AgentWallet, target_protocol: ID, amount: u64): bool` 交易前权限校验
- 关键数据结构：`AgentAction { agent_id: ID, action_type: u8, target: ID, amount: u64, timestamp: u64, success: bool }`

### 前端 / 后端
- **前端**：React + Next.js，Agent 创建向导、权限配置面板、活动监控仪表盘、紧急操作按钮
- **后端**：TypeScript Agent 框架，管理 zkLogin 会话、构造受限 PTB、执行链上操作
- 关键功能：Agent 创建流程、权限模板库、实时活动流

### AI / Agent 部分
- Agent 框架：基于 LangChain 构建 Agent 执行引擎，每步操作前自动校验链上权限
- 智能预算管理：AI 分析 Agent 历史消耗模式，预测剩余预算可用操作数
- 异常行为自检：Agent 执行前通过 LLM 评估操作合理性，防止被诱导执行恶意操作

## 技术架构

用户通过前端使用 zkLogin（Google/GitHub OAuth）创建 Agent 身份，Move 合约铸造 AgentWallet 对象并设定权限参数。Agent 运行时通过 zkLogin 会话签名交易，每笔交易先经过 guard 模块校验（白名单、预算、有效期、冻结状态），通过后才执行实际操作。所有 AgentAction 通过事件记录上链。前端实时订阅事件展示 Agent 活动流，发现异常时用户可通过 PTB 紧急冻结钱包。

## 难度评估

| 维度 | 难度 | 说明 |
|------|------|------|
| Move 合约 | ⭐⭐⭐⭐ | 权限模型设计、能力系统和预算约束逻辑复杂 |
| 前端 | ⭐⭐⭐ | zkLogin 集成、Agent 管理界面 |
| AI/ML | ⭐⭐ | Agent 框架和预算预测，AI 层相对轻量 |
| 集成复杂度 | ⭐⭐⭐⭐ | zkLogin 认证流程和 Agent 会话管理 |

## 官方参赛要求检查

- [ ] 项目名称和描述
- [ ] 项目 Logo (1:1)
- [ ] 公开 GitHub 仓库
- [ ] Demo 视频 (≤5min)
- [ ] 测试网部署 + Package ID
