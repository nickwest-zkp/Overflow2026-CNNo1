# 商户收款 SaaS / Merchant Payment SaaS

> 商户接入即用，支持多币种收款、自动结算、链上对账和发票生成

## 解决什么问题

传统商户收款方案手续费高（2-3%）、结算周期长（T+2 到 T+7）、跨境支付成本更高。链上支付虽有低成本优势，但商户接入门槛高，需要技术团队对接区块链。商户收款 SaaS 提供即插即用的链上收款解决方案，商户无需了解区块链即可享受低手续费、即时结算和自动化对账。

## 核心功能

- 一键接入：商户注册后获得收款地址和 API Key，5 分钟完成接入
- 多币种收款：支持 SUI、USDC、USDT 等多种代币，自动兑换为商户指定币种
- 即时结算：交易确认即到账，通过 PTB 自动兑换和结算
- 链上对账：所有交易记录在链上，自动生成可验证的对账单和发票

## 使用的 Sui 原语

| 原语 | 用途 |
|------|------|
| PTB | 原子化执行"收款 → 兑换 → 结算到商户地址"复合操作 |
| Coin 标准 | 统一处理多种支付代币 |
| 对象所有权 | MerchantAccount 作为对象，绑定商户身份和配置 |
| 动态字段 | 存储商户配置（接受的币种、结算地址、手续费率） |
| 事件 (Events) | 发出支付事件，触发对账和通知流程 |

## 实现方案

### 智能合约 (Move)
- `merchant_registry` 模块：注册商户，创建 MerchantAccount 对象
- `payment_processor` 模块：处理支付请求，验证金额和币种
- `auto_swap` 模块：通过 DeepBook 集成自动兑换代币
- `settlement` 模块：将结算资金转入商户指定地址
- 关键数据结构：`MerchantAccount { merchant_id: address, accepted_tokens: vector<Type>, settlement_token: Type, fee_rate: u64, settlement_address: address }`

### 前端 / 后端
- 技术栈：Next.js（商户管理后台）+ 支付 Widget（嵌入式收款组件）
- 关键页面：Merchant Dashboard（交易总览）、Payment Link（生成收款链接）、Settlements（结算记录）、Invoices（发票管理）
- 后端提供 REST API 供商户系统集成，监听链上事件更新交易状态

## 技术架构

商户注册后获得 MerchantAccount 对象和 API Key。用户在商户网站付款时，支付 Widget 构建 PTB：从用户钱包扣除代币 → 通过 DeepBook 兑换为目标币种 → 扣除手续费 → 结算到商户地址。整个过程原子化完成，无需信任第三方。后端监听链上支付事件，更新商户后台的交易记录和对账单，并自动生成发票。商户可选择定期批量结算或即时结算。

## 难度评估

| 维度 | 难度 | 说明 |
|------|------|------|
| Move 合约 | ⭐⭐⭐ | 支付处理和兑换逻辑较直接 |
| 前端 | ⭐⭐⭐⭐ | 需要嵌入式支付 Widget 和商户管理后台 |
| 集成复杂度 | ⭐⭐⭐ | 需要对接 DEX 实现自动兑换 |

## 官方参赛要求检查

- [ ] 项目名称和描述
- [ ] 项目 Logo (1:1)
- [ ] 公开 GitHub 仓库
- [ ] Demo 视频 (≤5min)
- [ ] 测试网部署 + Package ID
