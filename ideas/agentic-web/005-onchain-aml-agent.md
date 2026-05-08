# On-chain AML Agent / 链上反洗钱 Agent

> 追踪可疑资金流向，AI 分析地址行为模式，通过对象所有权模型标记风险地址

## 解决什么问题

链上匿名性为洗钱和非法资金转移提供了便利，DeFi 协议和合规机构缺乏有效的链上资金追踪工具。现有链上分析工具大多针对 EVM 生态，无法利用 Sui 的对象模型优势。本项目利用 Sui 对象所有权模型精确追踪资金流向，AI 模型分析地址行为模式，自动标记高风险地址并生成可审计的调查报告，帮助 Sui 生态应对合规挑战。

## 核心功能

- 自动追踪目标地址的资金流向图，利用 Sui 对象所有权模型精确追踪 Coin 对象转移路径
- AI 模型分析地址行为模式（交易频率、金额分布、时间规律、协议交互），计算洗钱风险评分
- 高风险地址自动标记并发布到链上风险注册表，供 DeFi 协议集成进行实时风控
- 生成可视化资金流向图和调查报告，支持导出

## 使用的 Sui 原语

| 原语 | 用途 |
|------|------|
| Move 对象模型 | `RiskRegistry` 共享对象维护风险地址列表和评分 |
| 对象所有权 | 利用对象转移记录精确追踪资金流动路径 |
| 动态字段 | 为每个风险地址附加行为分析详情和证据链 |
| 事件（Event） | 发出 `RiskAddressFlaggedEvent` 通知集成方 |

## 实现方案

### 智能合约 (Move)
- **`risk_registry` 模块**：定义 `RiskRegistry` 共享对象，存储 `flagged_addresses: vector<address>`、`risk_scores: Bag`、`last_update: u64`
- **`address_profile` 模块**：`flag_address(registry: &mut RiskRegistry, addr: address, risk_score: u64, evidence_hash: vector<u8>)` 标记风险地址
- **`compliance_hook` 模块**：提供 `check_address(registry: &RiskRegistry, addr: address): bool` 供其他协议集成，在交易前检查对方风险等级
- 关键数据结构：`AddressRisk { addr: address, score: u64, category: u8, flagged_epoch: u64, evidence_count: u64 }`

### 前端 / 后端
- **前端**：React + D3.js，交互式资金流向图、地址风险详情页、风险仪表盘
- **后端**：Rust Agent 服务，通过 Sui RPC 全量同步交易数据构建资金图，调用 AI 模型做行为分析
- 关键组件：交易图数据库（Neo4j）、地址画像引擎、风险评分 API

### AI / Agent 部分
- 图神经网络（GNN）分析资金流向图的拓扑特征，识别典型洗钱模式（分层、混币、跨链跳转）
- 行为聚类：K-Means + DBSCAN 对地址行为特征聚类，识别异常群体
- Agent 工作流：新交易监听 → 资金图更新 → GNN 推理 → 风险评分更新 → 超阈值标记上链

## 技术架构

Agent 服务通过 Sui RPC 订阅最新交易，解析 Coin 对象转移事件并更新 Neo4j 资金流向图。GNN 模型对图结构做实时推理，识别异常资金路径和行为模式。风险评分超过阈值的地址通过 PTB 写入链上 RiskRegistry，其他 DeFi 协议可在交易前调用 compliance_hook 模块检查地址风险。前端提供可视化资金追踪和调查工具，支持监管机构协作。

## 难度评估

| 维度 | 难度 | 说明 |
|------|------|------|
| Move 合约 | ⭐⭐ | 风险注册表和合规检查接口设计 |
| 前端 | ⭐⭐⭐ | 资金流向图可视化和交互复杂 |
| AI/ML | ⭐⭐⭐⭐⭐ | GNN 图神经网络训练和洗钱模式识别 |
| 集成复杂度 | ⭐⭐⭐⭐ | 全量交易同步和图数据库维护 |

## 官方参赛要求检查

- [ ] 项目名称和描述
- [ ] 项目 Logo (1:1)
- [ ] 公开 GitHub 仓库
- [ ] Demo 视频 (≤5min)
- [ ] 测试网部署 + Package ID
