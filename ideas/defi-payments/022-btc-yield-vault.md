# BTC 收益金库 / BTC Yield Vault

> 存入 BTC，自动通过 DeepBook 兑换为收益资产，赚取收益后换回 BTC

## 解决什么问题

BTC 持有者希望在不卖出 BTC 的前提下赚取收益，但大多数 DeFi 收益策略以稳定币或其他代币计价。BTC 持有者面临两难：持有 BTC 不产生收益，或者将 BTC 换成其他资产赚取收益但承担 BTC 涨跌的错配风险。BTC 收益金库让用户存入 BTC，系统自动通过 DEX 兑换为高收益资产赚取收益，定期将收益换回 BTC，让用户始终以 BTC 计价增值。

## 核心功能

- BTC 原生存入：支持存入原生 BTC（通过 Sui 包装资产）
- 自动收益循环：BTC → 兑换为收益资产 → 存入 DeFi 赚取收益 → 收益换回 BTC
- BTC 计价报告：所有收益以 BTC 为单位展示，直观反映 BTC 增长
- 风险对冲：预留部分 BTC 不参与兑换，对冲价格波动风险

## 使用的 Sui 原语

| 原语 | 用途 |
|------|------|
| Move 对象模型 | BTCVault 作为独立 Object，管理 BTC 存款和策略 |
| PTB | 原子化执行"BTC 兑换 → 存入策略 → 收益提取 → 换回 BTC" |
| Coin 标准 | 处理 BTC 和收益代币的转换 |
| DeepBook 集成 | 通过 DEX 实现 BTC 与收益资产的兑换 |
| 动态字段 | 存储兑换历史、收益记录和风险参数 |

## 实现方案

### 智能合约 (Move)
- `btc_vault` 模块：管理 BTC 存入和提取
- `swap_router` 模块：通过 DeepBook 实现 BTC ↔ 收益资产的最优兑换路径
- `yield_loop` 模块：管理"兑换 → 存入 → 赚取 → 换回"的收益循环
- `risk_guard` 模块：监控滑点和价格偏差，设置安全阈值
- 关键数据结构：`BTCVault { owner: address, btc_deposited: Coin<BTC>, btc_earning: Coin<BTC>, in_yield_position: Coin<T>, created_at: u64 }`

### 前端 / 后端
- 技术栈：React + @mysten/dapp-kit + Sui TypeScript SDK
- 关键页面：Vault Dashboard（BTC 金库总览）、Deposit/Withdraw（存取 BTC）、Yield History（收益历史）、BTC Performance（BTC 计价收益曲线）
- 后端 Keeper 定期触发收益循环和收益 harvesting

## 技术架构

用户存入 BTC 到 BTCVault 对象后，后端 Keeper 定期触发收益循环：通过 DeepBook 将部分 BTC 兑换为高收益稳定币 → 存入 Scallop 或 Cetus 赚取利息 → 定期提取利息收益 → 通过 DeepBook 将收益换回 BTC。整个过程通过 PTB 原子化执行，确保不会因中途失败导致资产丢失。前端以 BTC 为单位展示总收益和年化收益率，让用户清楚看到 BTC 持有量的增长。

## 难度评估

| 维度 | 难度 | 说明 |
|------|------|------|
| Move 合约 | ⭐⭐⭐⭐ | 兑换路径和收益循环需要安全的资金管理 |
| 前端 | ⭐⭐⭐ | 需要清晰的 BTC 计价展示 |
| 集成复杂度 | ⭐⭐⭐⭐ | 需要对接 DEX 和借贷协议实现完整循环 |

## 官方参赛要求检查

- [ ] 项目名称和描述
- [ ] 项目 Logo (1:1)
- [ ] 公开 GitHub 仓库
- [ ] Demo 视频 (≤5min)
- [ ] 测试网部署 + Package ID
