# Cross-Framework Memory / 跨框架记忆共享

> 不同 AI 框架的 Agent 通过 Walrus 共享记忆和上下文，打破框架间的数据孤岛

## 解决什么问题

不同 AI 框架（LangChain、AutoGen、CrewAI、Dify）的 Agent 各自维护独立的记忆系统，无法互相理解和共享上下文。用户在 LangChain Agent 中告诉 AI 自己的偏好，换成 AutoGen Agent 后又要重新说一遍。本方案通过 Walrus 建立跨框架的统一记忆层，让任何框架的 Agent 都能访问共享记忆。

## 核心功能

- 统一记忆格式：定义跨框架的标准记忆 JSON Schema，所有框架输出相同格式
- 记忆读写网关：提供 REST API 让任意框架的 Agent 读写共享记忆
- 上下文翻译：自动将一个框架的上下文格式转换为另一个框架可理解的格式
- 权限隔离：不同框架的 Agent 只能访问被授权的记忆分区

## 使用的 Walrus / Sui 能力

| 能力 | 用途 |
|------|------|
| Walrus Blob 存储 | 存储跨框架共享记忆数据和上下文 |
| Sui 对象模型 | 链上记录框架注册信息、权限策略、记忆分区 |
| MemWal 记忆层 | 提供统一的记忆读写抽象层 |
| Seal 加密 | 按分区加密记忆数据，实现细粒度访问控制 |

## 实现方案

### Agent / AI 部分
- 格式转换 Agent 将各框架的记忆格式转为统一中间表示（IR）
- 上下文适配 Agent 根据目标框架的要求裁剪和转换上下文
- 权限检查 Agent 在每次记忆访问时验证请求框架的权限
- 冲突协调 Agent 处理多框架同时写入同一记忆分区的冲突
- 提供各框架的 SDK 插件（LangChain Memory、AutoGen Memory 等）

### Walrus 集成
- 共享记忆以 `shared/{user_id}/memory/{category}.json` 统一格式存储
- 框架特定视图以 `shared/{user_id}/views/{framework}/` 存储转换后的数据
- 权限策略以 Sui 链上对象存储，定义每个框架可访问的分区
- 访问日志以 `shared/{user_id}/access_log.json` 记录所有读写操作

### 前端 / 后端
- 前端：React 管理面板，展示已连接框架和权限配置
- 后端：Python 网关服务 + 各框架 SDK 包
- 关键页面：框架管理页、权限配置页、记忆浏览页、访问日志页

## 技术架构

框架 Agent 调用 SDK 写入记忆 → SDK 序列化为统一 JSON → 通过网关 API 发送 → 权限检查 Agent 验证 → 格式转换 Agent 检查是否需要转换 → 记忆数据写入 Walrus Blob → Sui 链上更新索引 → 另一框架 Agent 调用 SDK 读取 → 网关从 Walrus 获取数据 → 格式转换为目标框架格式 → 返回给 Agent。

## 难度评估

| 维度 | 难度 | 说明 |
|------|------|------|
| Walrus 集成 | ⭐⭐⭐ | 统一存储格式和跨框架索引管理 |
| AI/Agent | ⭐⭐⭐ | 格式转换和上下文适配 |
| 前端 | ⭐⭐ | 管理面板和权限配置 |
| 集成复杂度 | ⭐⭐⭐⭐⭐ | 多框架适配、权限系统、实时同步 |

## 官方参赛要求检查

- [ ] 项目名称和描述
- [ ] 项目 Logo (1:1)
- [ ] 公开 GitHub 仓库
- [ ] Demo 视频 (≤5min)
- [ ] 测试网部署 + Package ID
