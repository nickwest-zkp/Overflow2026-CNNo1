# Flash Loan Attack Detector / 闪电贷攻击检测器

> 监控链上 mempool 交易模式，AI 识别闪电贷攻击特征，通过 PTB 自动触发保护机制

## 解决什么问题

DeFi 协议频繁遭受闪电贷攻击，攻击者在单个区块内完成借贷、操纵价格、套利获利，导致普通用户资金损失。传统监控依赖静态规则，难以捕捉新型攻击模式。本项目利用 AI 模型实时分析交易模式，在攻击执行前识别并阻断，为 Sui 生态 DeFi 协议提供主动安全防护。

## 核心功能

- 实时监控 Sui mempool 中待确认交易，提取交易结构特征（多步 PTB、大额借贷、DEX 交互组合）
- AI 模型（XGBoost + Transformer）对交易意图分类，识别闪电贷攻击特征模式
- 检测到攻击时自动构造 PTB 提交防御交易（紧急撤除流动性、暂停交易对）
- 攻击事件链上记录与事后分析报告生成

## 使用的 Sui 原语

| 原语 | 用途 |
|------|------|
| PTB | 分析攻击后构造原子化防御交易：读取防御策略 → 验证攻击签名 → 执行保护动作 |
| 共享对象 | `DefensePolicy` 共享对象存储防御规则和白名单，支持多协议共用 |
| 事件（Event） | 发出 `AttackAlertEvent` 供协议方和社区实时订阅 |
| 动态字段 | 为每笔攻击事件附加详细分析数据（攻击类型、涉及金额、路径） |

## 实现方案

### 智能合约 (Move)
- **`defense_policy` 模块**：定义 `DefensePolicy` 共享对象，字段包括 `enabled_protocols: vector<ID>`、`auto_defend: bool`、`alert_threshold: u64`、`cooldown_epochs: u64`
- **`attack_registry` 模块**：`record_attack(policy: &mut DefensePolicy, attack_sig: vector<u8>, attack_type: u8, severity: u8)` 记录攻击事件
- **`emergency_guard` 模块**：`trigger_protection(policy: &mut DefensePolicy, protocol_id: ID)` 执行紧急保护，包含冷却期逻辑防止误触发
- 关键数据结构：`AttackRecord { attack_hash: vector<u8>, attack_type: u8, severity: u8, timestamp: u64, defended: bool }`

### 前端 / 后端
- **前端**：React + Next.js，攻击实时监控大屏、历史攻击地图、防御策略配置面板
- **后端**：Rust Agent 服务通过 WebSocket 订阅 Sui 全节点的 mempool 数据，调用 AI 推理服务分析交易
- 关键组件：交易解析器、特征提取管道、攻击分类引擎、PTB 构造器

### AI / Agent 部分
- 特征工程：从 PTB 结构中提取交易步数、金额分布、协议交互顺序、地址历史等 50+ 维特征
- 模型方案：XGBoost 做快速初步筛选（<50ms），Transformer 模型做深度分析（<200ms）
- Agent 工作流：mempool 订阅 → 特征提取 → 模型推理 → 阈值判断 → 构造防御 PTB → 提交上链

## 技术架构

Agent 服务通过 Sui 全节点 WebSocket 订阅 mempool 中待确认交易，解析 PTB 结构并提取多维特征向量。特征输入 XGBoost 快速筛选层，可疑交易进入 Transformer 深度分析层，输出攻击概率和类型。超过阈值时，Agent 自动构造防御 PTB（读取 DefensePolicy → 验证攻击签名 → 调用 trigger_protection），通过付费节点加速提交争取在攻击交易之前确认。所有攻击事件通过 AttackRecord 对象链上存证。

## 难度评估

| 维度 | 难度 | 说明 |
|------|------|------|
| Move 合约 | ⭐⭐⭐ | 共享对象权限管理和冷却期逻辑设计 |
| 前端 | ⭐⭐ | 监控大屏 + 配置面板，中等复杂度 |
| AI/ML | ⭐⭐⭐⭐⭐ | 需要标注数据集训练攻击识别模型，实时推理延迟要求高 |
| 集成复杂度 | ⭐⭐⭐⭐ | 需要全节点 mempool 访问和与多 DeFi 协议对接 |

## 官方参赛要求检查

- [ ] 项目名称和描述
- [ ] 项目 Logo (1:1)
- [ ] 公开 GitHub 仓库
- [ ] Demo 视频 (≤5min)
- [ ] 测试网部署 + Package ID
