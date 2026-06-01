# 银发星球 · Claude Code 开发指令 · 总索引

本目录包含「银发星球」项目交给 Claude Code 4.7 的**完整、模块化、可直接复制粘贴**的开发指令。

## 使用方式（两种任选其一）

### 方式 A：一次性灌入完整上下文（推荐）

打开 [FULL-INSTRUCTION.md](./FULL-INSTRUCTION.md)，全选复制，粘贴给 Claude Code。它会读完全部模块的导航与总览。

然后说一句：

> 请从「第十三部分：分阶段实现」的**阶段一**开始，按顺序实现。每完成一个阶段告诉我，我测试通过后再继续下一个阶段。

### 方式 B：分模块投喂（适合调试某一个具体模块）

按需把以下任意一个文件单独丢给 Claude Code：

| 编号 | 文件 | 内容 |
|------|------|------|
| 01 | [project-overview](./01-project-overview.md) | 项目总纲、用户、角色、品牌 |
| 02 | [tech-stack](./02-tech-stack.md) | 技术栈选择 |
| 03 | [file-structure](./03-file-structure.md) | 项目文件结构 |
| 04 | [database-schema](./04-database-schema.md) | Prisma 数据库模型（完整 schema） |
| 05 | [mvp-scope](./05-mvp-scope.md) | MVP 阶段实现范围 |
| 06 | [ai-chat-core](./06-ai-chat-core.md) | AI 陪聊核心实现 |
| 07 | [admin-backend](./07-admin-backend.md) | 管理后台详细需求 |
| 08 | [landing-page](./08-landing-page.md) | 落地页设计 |
| 09 | [api-routes](./09-api-routes.md) | 全部 API 路由设计 |
| 10 | [business-logic](./10-business-logic.md) | 关键业务逻辑流程 |
| 11 | [env-config](./11-env-config.md) | 环境变量配置 |
| 12 | [tailwind-theme](./12-tailwind-theme.md) | Tailwind 主题配置 |
| 13 | [implementation-phases](./13-implementation-phases.md) | 分阶段实现顺序 |
| 14 | [requirements](./14-requirements.md) | 具体要求 & 验收标准 |
| 15 | [yinhua-persona](./15-yinhua-persona.md) | 「银花」AI 人格 System Prompt（最重要！） |
| 16 | [narrative-product](./16-narrative-product.md) | 人生回忆叙事产品规范 |
| 17 | [charity-integration](./17-charity-integration.md) | 公益板块集成 |
| 18 | [operation-playbook](./18-operation-playbook.md) | 第一个月运营执行手册 |
| 19 | [elderly-ux-checklist](./19-elderly-ux-checklist.md) | 适老化 UX 验收清单 |

## 给 Claude Code 的开场白模板

```
你是一位资深全栈工程师，正在帮我从零开发「银发星球」(Silver Planet) 项目。

这是一个面向中老年人的 AI 情感陪伴 + 社群服务平台，独立品牌，独立运营。

我会先把完整的项目指令贴给你（FULL-INSTRUCTION.md）。请你：
1. 完整读完所有 19 个模块，建立全局上下文。
2. 从「实现顺序」的阶段一开始，按顺序逐步实现。
3. 每完成一个阶段，停下来告诉我进度，我测试通过后再继续。
4. 写完整、可运行的代码，禁止用 TODO / 此处省略 / 占位实现。
5. 老人端 UI 必须用大字、大按钮、高对比度（适老化）。
6. 严格遵守「银花」人格 System Prompt（见模块 15）。
```

---

准备好了吗？开始读吧。
