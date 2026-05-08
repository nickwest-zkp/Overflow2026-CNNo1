# NFT Floor Price Sniper / NFT 地板价狙击 Agent

> 监控目标集合地板价，达到阈值时 Agent 自动购买，预算上限保护

## 解决什么问题

NFT 市场价格波动剧烈，用户难以及时捕捉地板价跌破目标价的机会。手动盯盘耗时且容易错过短暂的价格窗口，而现有的限价单功能在 NFT 市场上并不普遍。本项目利用 AI Agent 持续监控目标 NFT 集合的地板价变化，当价格达到用户设定的目标阈值时自动执行购买，同时通过 Move 策略对象保证预算安全和操作可控。

## 核心功能

- 用户设定狙击条件：目标 NFT 集合、最高出价、预算上限、过期时间
- Agent 实时监控链上 NFT 挂单和地板价变化，检测到符合条件的 NFT 时自动出价
- AI 分析 NFT 稀有度和属性，在同等价格下优先狙击稀有度更高的 NFT
- 狙击历史和收益统计，支持多集合同时监控

## 使用的 Sui 原语

| 原语 | 用途 |
|------|------|
| Move 对象模型 | `SniperOrder` 对象存储狙击条件、预算和执行状态 |
| PTB | 原子化执行「检查条件 → 验证预算 → 购买 NFT → 剩余退回」 |
| Coin 管理 | 预算 Coin 的锁定和精确分配 |
| 事件（Event） | 发出 `NFTSnipedEvent` 通知用户购买成功 |

## 实现方案

### 智能合约 (Move)
- **`sniper_order` 模块**：定义 `SniperOrder` 对象，字段包括 `owner: address`、`collection_id: ID`、`max_price: u64`、`budget: Coin<SUI>`、`min_rarity_rank: u64`、`expires_at: u64`、`filled: bool`、`purchased_nft: Option<ID>`
- **`sniper_engine` 模块**：`execute_snipe(order: &mut SniperOrder, nft_id: ID, listing_price: u64, rarity_proof: RarityProof)` 执行购买并校验条件
- **`order_manager` 模块**：`create_order()`、`cancel_order()`、`extend_order()` 管理订单生命周期
- 关键数据结构：`SnipeRecord { order_id: ID, nft_id: ID, purchase_price: u64, rarity_rank: u64, timestamp: u64 }`

### 前端 / 后端
- **前端**：React + TailwindCSS，狙击订单创建面板、地板价实时图表、已购 NFT 展示
- **后端**：TypeScript Agent 服务，监控 NFT 市场挂单、稀有度分析、自动出价执行
- 关键功能：集合搜索、地板价追踪、狙击管理

### AI / Agent 部分
- 地板价预测：LSTM 模型基于历史交易数据预测短期地板价走势
- 稀有度评估：AI 分析 NFT metadata 属性组合，计算相对稀有度排名
- Agent 工作流：订阅市场挂单事件 → 过滤目标集合 → 价格和稀有度检查 → 构造购买 PTB → 提交执行

## 技术架构

用户通过前端创建 SniperOrder 对象并存入预算 Coin。Agent 服务通过 Sui RPC 和市场索引器订阅目标集合的新挂单和价格变更事件。当检测到符合条件的 NFT（价格低于 max_price 且稀有度满足要求）时，Agent 构造 PTB（校验订单状态 → 拆分预算 → 调用市场购买函数 → 剩余退回），通过付费节点加速提交争取成交。购买成功后发出 NFTSnipedEvent，前端实时通知用户。

## 难度评估

| 维度 | 难度 | 说明 |
|------|------|------|
| Move 合约 | ⭐⭐⭐ | 订单管理和 NFT 购买条件校验 |
| 前端 | ⭐⭐ | 价格图表和订单管理界面 |
| AI/ML | ⭐⭐⭐ | 地板价预测和稀有度评估模型 |
| 集成复杂度 | ⭐⭐⭐ | NFT 市场协议对接和实时监控 |

## 官方参赛要求检查

- [ ] 项目名称和描述
- [ ] 项目 Logo (1:1)
- [ ] 公开 GitHub 仓库
- [ ] Demo 视频 (≤5min)
- [ ] 测试网部署 + Package ID
