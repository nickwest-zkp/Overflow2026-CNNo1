# 订阅支付协议 / Subscription Payment Protocol

> 用户授权定期扣款，每次扣款通过 PTB 自动执行，可随时取消

## 解决什么问题

Web3 项目的订阅收入管理困难，现有方案依赖链下支付处理器或手动续费，用户体验差。用户需要记住续费时间，忘记续费则服务中断。传统订阅服务如 Stripe 只支持法币，且手续费高昂。订阅支付协议让用户一次授权即可自动定期扣款，服务方获得可预测的链上收入流。

## 核心功能

- 灵活订阅计划：支持按周/月/年扣款，自定义金额和代币类型
- 一键授权扣款：用户签署授权交易后，后续扣款自动执行
- 随时取消订阅：用户可通过 PTB 随时取消授权，停止扣款
- 订阅状态 NFT：活跃订阅铸造为 NFT，可作为服务访问凭证

## 使用的 Sui 原语

| 原语 | 用途 |
|------|------|
| Move 对象模型 | Subscription 作为独立 Object，存储计划和授权状态 |
| PTB | 原子化执行"检查授权 → 扣款 → 续期 → 更新 NFT" |
| Coin 标准 | 订阅费用使用 Coin<T>，支持任意代币 |
| NFT | 订阅状态铸造为 NFT，作为服务访问通行证 |
| 动态字段 | 存储扣款历史、下次扣款时间、取消状态 |

## 实现方案

### 智能合约 (Move)
- `subscription_factory` 模块：服务商创建订阅计划（金额、周期、代币类型）
- `subscription_manager` 模块：管理用户订阅的创建、续费和取消
- `auto_billing` 模块：定期扣款逻辑，验证授权和余额后执行转账
- `subscription_nft` 模块：铸造和管理订阅状态 NFT
- 关键数据结构：`Subscription { plan_id: ID, subscriber: address, amount: Coin<T>, period: u64, next_billing: u64, active: bool, nft_id: Option<ID> }`

### 前端 / 后端
- 技术栈：React + @mysten/dapp-kit + Sui TypeScript SDK
- 关键页面：Browse Plans（浏览订阅计划）、My Subscriptions（我的订阅）、Merchant Dashboard（商户管理后台）
- 后端 Keeper 服务在扣款日触发 PTB 执行自动扣款

## 技术架构

服务商通过合约创建订阅计划（定义金额、周期、代币）。用户浏览计划后签署授权交易，合约创建 Subscription 对象并铸造订阅 NFT。每个计费周期，Keeper 通过 PTB 执行扣款：验证订阅活跃 → 检查用户余额 → 转账给服务商 → 更新下次扣款时间。用户可随时通过 PTB 取消订阅，合约将状态标记为 inactive 并销毁 NFT。服务商可通过 API 查询订阅 NFT 验证用户身份。

## 难度评估

| 维度 | 难度 | 说明 |
|------|------|------|
| Move 合约 | ⭐⭐⭐ | 授权和扣款逻辑清晰，需注意并发安全 |
| 前端 | ⭐⭐⭐ | 订阅管理界面需要清晰的状态展示 |
| 集成复杂度 | ⭐⭐⭐ | 需要可靠的 Keeper 服务执行定期扣款 |

## 官方参赛要求检查

- [ ] 项目名称和描述
- [ ] 项目 Logo (1:1)
- [ ] 公开 GitHub 仓库
- [ ] Demo 视频 (≤5min)
- [ ] 测试网部署 + Package ID
