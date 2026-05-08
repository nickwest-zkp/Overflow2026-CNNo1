# 开发者提交指南

想参与 Sui Overflow 中文区抽奖 / 领周边？按下面步骤在本仓库登记你的项目信息即可。

> **注意：本仓库仅用于中文区项目登记和社区活动（抽奖/周边）。正式参赛请务必在 [Sui Overflow 官网](https://overflow.sui.io/) 完成提交，以官方提交为准。**

---

## 前提条件

- 有一个 GitHub 账号
- 有一个正在开发或已完成的项目（基于 Sui）

## 提交步骤

### 1. Fork 本仓库

打开本仓库页面，点击右上角 **Fork** 按钮，将仓库 fork 到自己账号下。

### 2. 复制提交模板

在你的 fork 里，把 `submissions/_template/` 整个目录复制一份，**重命名为你的 GitHub 用户名**：

```bash
git clone https://github.com/<你的用户名>/Overflow2026-CNNo1.git
cd Overflow2026-CNNo1
cp -r submissions/_template submissions/<你的GitHub用户名>
```

### 3. 填写项目信息

打开 `submissions/<你的GitHub用户名>/README.md`，按模板填写：

- **项目名称** — 替换掉 `<Project Name>`
- **赛道** — 在对应赛道前把 `[ ]` 改成 `[x]`，只选一个
- **项目简介** — 一段话描述你做了什么
- **链接** — 填上你的 GitHub 仓库地址和 Demo 视频链接
- **团队成员** — 写上所有成员的 `@github-id`
- **部署信息** — 填上部署环境（Testnet/Mainnet）和 Package ID

填完大概长这样：

```markdown
# SuiPay

## Track / 赛道

- [x] DeFi & Payments

## Description / 项目简介

基于 Sui 的可编程支付协议，支持多币种自动结算和链上对账。

## Links / 链接

- GitHub: https://github.com/myname/sui-pay
- Demo: https://www.youtube.com/watch?v=xxx
- Website: https://suipay.xyz

## Team / 团队成员

- @myname
- @partner-dev

## Deployment / 部署信息

- Env: Testnet
- Package ID: 0x1234...abcd
```

### 4. 提交 PR

```bash
git add submissions/<你的GitHub用户名>/
git commit -m "Add project: <项目名>"
git push origin main
```

然后回到 GitHub 页面，点击 **Create Pull Request**，提交到本仓库的 `main` 分支。

### 5. 等待合并

PR 合并后你就自动进入抽奖名单了。

---

## 目录命名规范

```
submissions/
├── _template/              # 模板，别动
├── zhangsan/               # 正确
├── li-dev/                 # 正确
├── 我的超酷项目/             # 错误！用 GitHub 用户名
├── My Cool Project/        # 错误！用 GitHub 用户名
```

**规则：**
- 目录名 = 你的 **GitHub 用户名**
- 只用小写字母、数字、连字符
- 一个 GitHub ID 一个目录

---

## 官方参赛要求（提醒）

正式参赛需在 [Sui Overflow 官网](https://overflow.sui.io/) 提交以下材料：

| 字段 | 要求 |
|------|------|
| 项目名称 | 简洁明了 |
| 项目描述 | 做什么，为什么重要 |
| 项目 Logo | 1:1 比例 (JPG/PNG) |
| GitHub 仓库 | 评审期间必须公开 |
| Demo 视频 | 必须，YouTube 优先，≤ 5 分钟 |
| 网站 | 可选，强烈建议 |
| 部署环境 | Testnet 或 Mainnet |
| Package ID | 如已部署链上 |

### 评审标准

- **产品体验** — 质量、可用性、打磨程度和整体用户体验
- **解决问题** — 有意义的问题、市场相关性和长期价值
- **技术质量** — 可靠性、与 Sui 的深度整合
- **表达与愿景** — 表达清晰、叙事和长期愿景

### 学生奖

- **10 个名额，每人 $2,500 USD**
- 团队至少 **50%** 成员为在校学生

### 奖金发放

- 获奖公布时发放 **50%**
- 成功部署主网后发放剩余 **50%**
- 截止时已部署主网的项目 → **100% 一次性发放**

---

## 常见问题

### 我的项目还没写完可以提交吗？

可以。只要填上项目名称、赛道和简介就行，链接可以先放 GitHub 仓库地址，后续通过新的 PR 更新。

### 一个人可以提交多个项目吗？

一个目录对应一个项目。如果想提交多个项目，可以在同一个 README 里追加，或者联系管理员。

### 团队项目怎么算？

由一名成员提交，在 README 的 Team 里列出所有成员的 GitHub ID，团队成员都有抽奖资格。

### 不在中文区可以提交吗？

本仓库面向中文社区，但你如果在中文社区活跃（微信群、Discord 中文频道等），欢迎提交。

### 抽奖怎么抽？

PR 合并后，你的 GitHub 用户名会自动进入参与者列表。抽奖时从列表中随机抽取。

---

## 一句话总结

**Fork → 复制模板 → 填信息 → 提 PR → 等合并 → 自动进抽奖池**
