# 银发星球 (Silver Planet)

> 一个专门为老年人设计的 AI 情感陪伴 + 社群服务平台
>
> **Slogan：有人陪，有人懂**

---

## 项目核心逻辑

用 AI 语音给独居老人做情感陪伴（轻量层），用社群给 50+ 人群做 AI 学习和社交（主战场），用「人生回忆叙事」给老人留下生命遗产（深度层）。

**三层服务，一个品牌，独立运营。**

## 服务人群

| 人群 | 痛点 | 银发星球怎么解决 |
|------|------|------------------|
| 独居老人（65-85 岁） | 孤独、没人说话、怕出事没人知道 | AI 语音陪聊 + 社区互助网络 |
| 刚退休人群（50-65 岁） | 突然闲下来、觉得自己没用了 | 银发 AI 学习社群 |
| 老人的子女（30-50 岁） | 担心父母，想尽孝但没时间 | 代父母购买 AI 陪聊 + 人生回忆叙事礼物 |

## 仓库内容

本仓库托管 **「银发星球」给 Claude Code 4.7 的完整开发指令文档**，按模块拆分，方便逐段复制使用。

文档目录见 [`docs/claude-code-instructions/`](./docs/claude-code-instructions/)。

## 文档结构

```
docs/claude-code-instructions/
├── 00-index.md                # 总索引（一键索引到各模块）
├── 01-project-overview.md     # 项目总纲 & 品牌定位
├── 02-tech-stack.md           # 技术栈选择
├── 03-file-structure.md       # 项目文件结构
├── 04-database-schema.md      # Prisma 数据库模型
├── 05-mvp-scope.md            # MVP 阶段实现范围
├── 06-ai-chat-core.md         # AI 陪聊核心实现（银花人格）
├── 07-admin-backend.md        # 管理后台详细需求
├── 08-landing-page.md         # 落地页设计
├── 09-api-routes.md           # API 路由设计
├── 10-business-logic.md       # 关键业务逻辑
├── 11-env-config.md           # 环境变量配置
├── 12-tailwind-theme.md       # Tailwind 主题配置
├── 13-implementation-phases.md# 分阶段实现顺序
├── 14-requirements.md         # 具体要求 & 验收标准
└── FULL-INSTRUCTION.md        # 一次性完整版（合并全部模块）
```

## 如何使用

1. 打开 `docs/claude-code-instructions/FULL-INSTRUCTION.md`，复制全文粘贴到 Claude Code，让它一次性读完所有上下文。
2. 然后让 Claude Code **从「阶段一」开始** 按 `13-implementation-phases.md` 中的顺序逐步实现。
3. 每完成一个阶段就测试，确认后再开下一个阶段。

## 三个角色

- **星主（项目主理人）**：运营社群、设计内容策略、管理志愿者
- **人生回忆叙事师（朋友）**：把老人的故事写成 3000–5000 字的深度叙事信
- **技术支持方（侄女）**：AI 语音相关技术咨询

## License

私有项目，保留所有权利。
