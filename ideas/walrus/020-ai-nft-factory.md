# AI NFT Factory / AI 生成 NFT 工厂

> AI 自动生成艺术作品，图片存储在 Walrus，元数据上链，完整创作过程可追溯可验证

## 解决什么问题

AI 生成艺术的版权归属和创作过程透明度是当前的核心争议。现有平台通常只存储最终图片，缺乏对创作过程、提示词演变、参数调整的完整记录。本方案将 AI 生成的每一幅作品及其完整创作过程存储在 Walrus，通过 Sui 链上 NFT 确权，让创作过程本身也成为可验证的数字资产。

## 核心功能

- Prompt 驱动生成：用户输入文字描述，AI 自动生成多风格候选图片
- 创作过程记录：记录从初始 prompt 到最终作品的每一步调整（提示词修改、参数变化、风格选择）
- 一键铸造 NFT：选定作品后自动将图片存入 Walrus、元数据上链铸造 Sui NFT
- 创作历史画廊：展示所有生成作品及其完整创作时间线

## 使用的 Walrus / Sui 能力

| 能力 | 用途 |
|------|------|
| Walrus Blob 存储 | 存储生成的图片（PNG/WebP）、创作过程 JSON、中间产物 |
| Sui NFT 标准 | 铸造 NFT 对象，元数据指向 Walrus Blob |
| Sui 对象模型 | 链上记录创作参数、生成时间、作者信息 |
| Walrus 读取 API | 前端从 Walrus 加载图片和创作过程数据 |

## 实现方案

### Agent / AI 部分
- 创作 Agent 使用 Stable Diffusion XL / DALL-E 3 根据用户 prompt 生成候选图
- 风格增强 Agent 根据用户反馈迭代调整提示词和参数
- 质量评估 Agent 对生成图片评分，筛选高质量候选
- 使用 ControlNet 和 LoRA 支持用户上传参考图控制生成风格

### Walrus 集成
- 最终作品以 `artworks/{id}/final.png` 存储
- 中间候选以 `artworks/{id}/candidates/{N}.png` 存储
- 创作过程以 `artworks/{id}/process.json` 存储，包含完整 prompt 链和参数变化
- NFT 元数据中 image_url 指向 Walrus Blob 读取地址

### 前端 / 后端
- 前端：Next.js + Canvas API，展示图片画廊和创作过程回放
- 后端：Python FastAPI，调用 AI 模型和 Walrus 存储 API
- 关键页面：创作工坊页、候选选择页、NFT 铸造页、创作画廊页

## 技术架构

用户输入创作 prompt → 创作 Agent 生成多个候选图 → 候选图和过程数据存入 Walrus → 用户选择并确认 → 图片存入 Walrus 获取 Blob ID → 调用 Sui NFT 合约铸造 NFT → NFT 元数据包含 Walrus 图片链接 → 前端展示 NFT 和创作过程回放。

## 难度评估

| 维度 | 难度 | 说明 |
|------|------|------|
| Walrus 集成 | ⭐⭐ | 图片大文件存储和 NFT 元数据关联 |
| AI/Agent | ⭐⭐⭐ | 图像生成质量控制和迭代优化 |
| 前端 | ⭐⭐⭐ | 图片画廊和创作过程可视化回放 |
| 集成复杂度 | ⭐⭐⭐ | AI 生成 + Walrus 存储 + NFT 铸造全链路 |

## 官方参赛要求检查

- [ ] 项目名称和描述
- [ ] 项目 Logo (1:1)
- [ ] 公开 GitHub 仓库
- [ ] Demo 视频 (≤5min)
- [ ] 测试网部署 + Package ID
