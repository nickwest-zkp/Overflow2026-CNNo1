# Natural Language Trading Engine / 自然语言交易引擎

> 用户输入"用 100 SUI 换 USDC 并存入 Scallop"，AI 解析为 PTB 并执行，带风险守护检查

## 解决什么问题

区块链交易操作复杂，用户需要理解钱包、Gas、DEX、流动性池等概念才能完成简单的金融操作。这极大地限制了 DeFi 的普及。本项目让用户通过自然语言描述交易意图，AI 自动解析为 Sui PTB 并执行，同时内置风险守护检查防止误操作和被钓鱼攻击，将 DeFi 的使用门槛降到最低。

## 核心功能

- 自然语言交易意图解析："帮我把一半 SUI 换成 USDC 存到 Scallop" → 结构化交易参数
- 自动构造多步 PTB：代币兑换、转账、存入协议，原子化执行不可分割
- 风险守护：AI 在执行前评估交易合理性（滑点、授权风险、金额异常），异常时拦截并警告
- 交易预览：展示 PTB 每一步操作和预计结果，用户确认后执行

## 使用的 Sui 原语

| 原语 | 用途 |
|------|------|
| PTB | 将自然语言意图编译为多步 PTB，原子化执行 |
| DeepBook | 代币兑换路径执行 |
| Move 对象模型 | `IntentSession` 对象存储解析结果和执行状态 |
| 事件（Event） | 发出 `IntentExecutedEvent` 记录意图执行结果 |

## 实现方案

### 智能合约 (Move)
- **`intent_session` 模块**：定义 `IntentSession` 对象，字段包括 `user: address`、`intent_hash: vector<u8>`、`ptb_bytes: vector<u8>`、`risk_score: u64`、`status: u8`（pending/confirmed/executed/rejected）、`created_at: u64`
- **`intent_executor` 模块**：`execute_intent(session: &mut IntentSession)` 验证签名后执行预编译 PTB
- **`risk_guard` 模块**：`check_risk(session: &IntentSession): bool` 链上风控校验（白名单协议、金额上限）
- 关键数据结构：`IntentRecord { session_id: ID, intent_text_hash: vector<u8>, steps_executed: u8, total_steps: u8, result: u8, timestamp: u64 }`

### 前端 / 后端
- **前端**：React + Next.js，聊天式交易界面、PTB 预览卡片、风险提示弹窗
- **后端**：Python Agent 服务，NLP 意图解析、PTB 构造器、风控引擎
- 关键功能：对话式交互、PTB 可视化分解、一键确认/拒绝

### AI / Agent 部分
- 意图解析：GPT-4o 结合 Sui 协议知识库将自然语言转换为结构化交易意图 JSON
- PTB 编译：意图 JSON 通过模板引擎映射为 Sui PTB 字节码
- 风险守护：独立 LLM 审查每笔交易合理性（金额是否异常、目标是否可信、滑点是否可接受）
- Agent 工作流：接收用户输入 → GPT-4o 解析 → 风险审查 → 构造 PTB → 预览展示 → 用户确认 → 执行

## 技术架构

用户在前端聊天框输入自然语言交易指令，GPT-4o 结合 Sui 协议知识库（预训练的协议接口描述）解析为结构化意图 JSON。风险守护 LLM 独立审查意图合理性，通过后进入 PTB 编译阶段——模板引擎将意图映射为对应的 Sui SDK 调用序列。编译后的 PTB 在前端以卡片形式逐步展示预览，用户确认后签名提交。执行结果通过 IntentExecutedEvent 上链，前端展示交易回执。

## 难度评估

| 维度 | 难度 | 说明 |
|------|------|------|
| Move 合约 | ⭐⭐⭐ | 意图会话管理和风控校验 |
| 前端 | ⭐⭐⭐ | 聊天式 UI 和 PTB 可视化 |
| AI/ML | ⭐⭐⭐⭐ | 意图解析准确性和风险判断 LLM |
| 集成复杂度 | ⭐⭐⭐ | 多协议 PTB 模板库维护 |

## 官方参赛要求检查

- [ ] 项目名称和描述
- [ ] 项目 Logo (1:1)
- [ ] 公开 GitHub 仓库
- [ ] Demo 视频 (≤5min)
- [ ] 测试网部署 + Package ID
