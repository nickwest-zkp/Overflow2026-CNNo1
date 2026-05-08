# 线下扫码支付 / QR Code Payment

> 生成 Sui 支付二维码，商家扫码或用户扫码完成支付，PTB 处理兑换和结算

## 解决什么问题

线下零售商户接入加密货币支付面临技术门槛高、用户体验差的问题。现有方案需要专用 POS 设备，成本高昂且不支持多币种。线下扫码支付让商户通过手机生成收款二维码，用户扫码即可用任意代币完成支付，系统通过 PTB 自动处理代币兑换和结算，体验接近微信支付和支付宝。

## 核心功能

- 商户二维码生成：商户输入金额后生成动态收款二维码
- 多币种支付：用户可用任意代币支付，系统自动兑换为商户目标币种
- 即时确认：交易提交即确认，无需等待多个区块
- 交易凭证：支付完成后生成链上电子收据

## 使用的 Sui 原语

| 原语 | 用途 |
|------|------|
| PTB | 原子化执行"扫码 → 代币兑换 → 支付转账 → 生成收据" |
| Coin 标准 | 支持任意 Coin<T> 作为支付媒介 |
| 对象所有权 | MerchantTerminal 作为商户终端对象 |
| DeepBook 集成 | 通过 DEX 实现支付代币的即时兑换 |
| 事件 (Events) | 发出支付事件通知商户终端 |

## 实现方案

### 智能合约 (Move)
- `merchant_terminal` 模块：管理商户终端注册和配置
- `payment_session` 模块：创建支付会话，锁定金额和有效期
- `auto_swap` 模块：通过 DeepBook 路由最优兑换路径
- `receipt` 模块：生成链上电子收据对象
- 关键数据结构：`PaymentSession { merchant: address, amount: u64, token: Type, expires_at: u64, status: u8 }`

### 前端 / 后端
- 技术栈：React Native（移动端 App）+ QRCode 库 + @mysten/dapp-kit
- 关键页面：Merchant Screen（商户收款界面）、Customer Scan（用户扫码界面）、Transaction History（交易历史）、Receipt Detail（收据详情）
- 后端提供 WebSocket 推送，实时通知商户支付状态

## 技术架构

商户在终端输入金额，系统创建 PaymentSession 共享对象并生成包含 Session ID 的二维码。用户扫描二维码后，App 展示支付详情，用户选择支付代币后构建 PTB：从用户钱包扣除代币 → 通过 DeepBook 兑换为商户目标币种 → 转入商户地址 → 生成 Receipt 对象。商户终端通过 WebSocket 收到支付确认通知。整个过程 2 秒内完成，体验接近传统扫码支付。

## 难度评估

| 维度 | 难度 | 说明 |
|------|------|------|
| Move 合约 | ⭐⭐⭐ | 支付会话和兑换逻辑需要安全设计 |
| 前端 | ⭐⭐⭐⭐ | 移动端扫码体验需要流畅的 UX |
| 集成复杂度 | ⭐⭐⭐ | 需要对接 DEX 和实时通知系统 |

## 官方参赛要求检查

- [ ] 项目名称和描述
- [ ] 项目 Logo (1:1)
- [ ] 公开 GitHub 仓库
- [ ] Demo 视频 (≤5min)
- [ ] 测试网部署 + Package ID
