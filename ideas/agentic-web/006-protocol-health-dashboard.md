# Protocol Health Dashboard / 协议健康度仪表盘

> 聚合 TVL、流动性深度、清算数据，AI 生成协议健康评分和风险预警

## 解决什么问题

DeFi 用户和投资者缺乏对协议整体健康状况的全面了解，TVL 等单一指标无法反映协议真实风险。用户需要在多个平台间切换才能获取碎片化的协议数据。本项目聚合 Sui 生态各 DeFi 协议的多维健康指标，AI 模型综合评估协议健康度，生成可理解的风险预警，帮助用户做出更明智的投资决策。

## 核心功能

- 自动聚合 Sui 生态 DeFi 协议的 TVL、流动性深度、清算量、用户数、交易量等多维数据
- AI 模型综合评估协议健康评分（A+ 到 D 级），输出关键风险因子和改善建议
- 异常指标实时预警（TVL 异常流出、清算量激增、流动性骤降）
- 协议健康趋势图表和历史对比分析

## 使用的 Sui 原语

| 原语 | 用途 |
|------|------|
| Move 对象模型 | `HealthReport` 对象存储协议健康评分和关键指标快照 |
| PTB | 原子化执行「读取协议数据 → 计算评分 → 更新报告 → 触发预警」 |
| 事件（Event） | 发出 `HealthAlertEvent` 在健康评分大幅变动时通知订阅方 |
| 链上数据 | 直接读取协议的共享对象（流动性池、借贷市场）获取实时数据 |

## 实现方案

### 智能合约 (Move)
- **`health_report` 模块**：定义 `HealthReport` 对象，字段包括 `protocol_id: ID`、`health_score: u64`、`grade: String`、`tvl: u256`、`risk_factors: vector<String>`、`timestamp: u64`
- **`metric_collector` 模块**：`update_metrics(report: &mut HealthReport, tvl: u256, volume_24h: u256, liquidations: u64)` 更新指标数据
- **`alert_engine` 模块**：`check_and_alert(report: &HealthReport, prev_score: u64)` 对比历史评分触发告警
- 关键数据结构：`ProtocolMetrics { tvl: u256, volume_24h: u256, unique_users_24h: u64, liquidation_count: u64, liquidity_depth: u256 }`

### 前端 / 后端
- **前端**：React + Recharts/ECharts，协议健康总览仪表盘、单协议详情页、趋势对比工具
- **后端**：TypeScript Agent 服务，定时从链上和索引服务获取协议数据，调用 AI 模型计算健康评分
- 关键页面：生态总览、协议排行、健康趋势图、预警中心

### AI / Agent 部分
- 多维指标归一化和权重分配：基于历史数据训练的线性回归模型确定各指标权重
- 异常检测：Isolation Forest 检测指标中的异常值和趋势突变
- LLM 生成自然语言健康报告：GPT-4o 将评分和指标转化为用户可理解的分析文本
- Agent 工作流：每小时抓取链上数据 → 指标归一化 → 异常检测 → 评分计算 → 报告上链 → 预警推送

## 技术架构

Agent 服务每小时通过 Sui RPC 和索引服务（SuiVision/SuiScan API）聚合各协议的链上数据。原始指标经归一化处理后输入评分模型（加权线性模型 + Isolation Forest 异常检测），输出协议健康评分和风险因子。结果通过 PTB 写入 HealthReport 对象上链。GPT-4o 生成自然语言分析摘要供前端展示。异常变化触发 HealthAlertEvent，前端通过 WebSocket 实时推送预警。

## 难度评估

| 维度 | 难度 | 说明 |
|------|------|------|
| Move 合约 | ⭐⭐ | 报告存储和告警逻辑，合约相对简单 |
| 前端 | ⭐⭐⭐ | 多维数据可视化和仪表盘设计 |
| AI/ML | ⭐⭐⭐ | 指标权重模型训练和异常检测 |
| 集成复杂度 | ⭐⭐⭐ | 需要对接多个 DeFi 协议的数据接口 |

## 官方参赛要求检查

- [ ] 项目名称和描述
- [ ] 项目 Logo (1:1)
- [ ] 公开 GitHub 仓库
- [ ] Demo 视频 (≤5min)
- [ ] 测试网部署 + Package ID
