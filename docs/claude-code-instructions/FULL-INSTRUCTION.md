# 银发星球 · 完整开发指令（一次性版本）

> 这是给 Claude Code 4.7 的完整开发指令。复制全文粘贴到 CC，然后说"请从阶段一开始"。

---

## 角色扮演

你是一位资深全栈工程师，正在帮我从零开发「银发星球」(Silver Planet) 项目。

这是一个面向中老年人的 AI 情感陪伴 + 社群服务平台，独立品牌，独立运营。

请：
1. 完整读完下面所有 19 个模块，建立全局上下文
2. 从「实现顺序」的阶段一开始，按顺序逐步实现
3. 每完成一个阶段，停下来告诉我进度，我测试通过后再继续
4. 写完整、可运行的代码，禁止用 TODO / 此处省略 / 占位实现
5. 老人端 UI 必须用大字、大按钮、高对比度（适老化）
6. 严格遵守「银花」人格 System Prompt（见模块 15）

---

## 模块导航

完整内容请参考同目录下的各个分模块文件：

- [01-project-overview.md](./01-project-overview.md) — 项目总纲
- [02-tech-stack.md](./02-tech-stack.md) — 技术栈
- [03-file-structure.md](./03-file-structure.md) — 文件结构
- [04-database-schema.md](./04-database-schema.md) — 数据库 Schema
- [05-mvp-scope.md](./05-mvp-scope.md) — MVP 范围
- [06-ai-chat-core.md](./06-ai-chat-core.md) — AI 陪聊核心
- [07-admin-backend.md](./07-admin-backend.md) — 管理后台
- [08-landing-page.md](./08-landing-page.md) — 落地页
- [09-api-routes.md](./09-api-routes.md) — API 路由
- [10-business-logic.md](./10-business-logic.md) — 业务逻辑
- [11-env-config.md](./11-env-config.md) — 环境变量
- [12-tailwind-theme.md](./12-tailwind-theme.md) — Tailwind 主题
- [13-implementation-phases.md](./13-implementation-phases.md) — 分阶段实现
- [14-requirements.md](./14-requirements.md) — 具体要求
- [15-yinhua-persona.md](./15-yinhua-persona.md) — 银花人格
- [16-narrative-product.md](./16-narrative-product.md) — 人生叙事
- [17-charity-integration.md](./17-charity-integration.md) — 公益集成
- [18-operation-playbook.md](./18-operation-playbook.md) — 第一个月运营
- [19-elderly-ux-checklist.md](./19-elderly-ux-checklist.md) — 适老化清单

---

## 第一部分 · 项目总纲

- 项目名称：银发星球 (Silver Planet)
- Slogan：有人陪，有人懂
- 一句话：为老年人提供 AI 语音情感陪伴 + 社群服务的独立平台

目标用户：
- 独居老人（65-85 岁）：用 AI 语音陪聊
- 刚退休人群（50-65 岁）：在社群里学 AI、交朋友
- 老人的子女（30-50 岁）：代父母购买服务

三层服务体系：
- 第一层：AI 语音情感陪伴（免费 + 付费升级 9.9-29.9 元/月）
- 第二层：银发 AI 社群（19.9 元/月）
- 第三层：人生回忆叙事（980 元/家庭版 1980 元）
- 第四层：线下互助网络（撮合抽成 5-15 元/单）

品牌定位：
- 色调：暖橙 + 米白 + 深绿
- 风格：极简、大字、大按钮、高对比度（适老化）
- 独立品牌，不挂靠任何平台

---

## 第二部分 · 技术栈

- 前端框架：Next.js 14 + TypeScript + Tailwind CSS
- 后端：Next.js API Routes + Prisma ORM
- 数据库：PostgreSQL (Supabase 免费层)
- 认证：NextAuth.js (手机号 + 微信)
- AI 对话：Claude API (anthropic-sdk)
- 语音合成：腾讯云 TTS / Azure TTS
- 语音识别：腾讯云 ASR / OpenAI Whisper
- 文件存储：Supabase Storage
- 支付：微信支付 JSAPI (先手动确认)
- 部署：Vercel
- 缓存：Redis (Vercel KV / Upstash)

---

## 第三部分 · 文件结构

详见 [03-file-structure.md](./03-file-structure.md)

核心路径：
- `app/(elderly)/talk/page.tsx` — 老人端 AI 陪聊
- `app/(admin)/*` — 管理后台
- `app/api/chat/send/route.ts` — AI 对话核心 API
- `lib/claude.ts` — Claude API 封装
- `lib/prompts/yinhua-system-prompt.ts` — 银花人格
- `prisma/schema.prisma` — 数据库模型

---

## 第四部分 · 数据库 Schema

完整 schema 见 [04-database-schema.md](./04-database-schema.md)。

核心模型：
- User（含 ELDERLY/FAMILY/VOLUNTEER/HELPER/ADMIN 五种角色）
- ChatSession + ChatMessage（AI 陪聊）
- ChatTopic（话题库）
- CommunityPost + CommunityComment（社群）
- LearningContent（学习内容）
- Subscription + Order（订阅与订单）
- NarrativeOrder + NarrativeMaterial（人生叙事）
- CompanionMatch（线下匹配）
- VolunteerProfile（志愿者）
- CharityProject（公益）

---

## 第五部分 · MVP 范围

P0（第一周）：落地页 + 老人端 AI 陪聊 + 银花人格 + 基础登录 + 管理后台

P1（第二周）：社群 + 学习内容 + 订阅 + 支付 + 社群浏览

P2（第三-四周）：志愿者 + 陪伴匹配 + 人生叙事 + 数据看板 + 公益

不做：智能音箱硬件、400 电话、实时音视频、原生 App、多语言

---

## 第六部分 · AI 陪聊核心

详见 [06-ai-chat-core.md](./06-ai-chat-core.md)。

ClaudeService 类方法：
- createChatSession
- sendMessage
- getChatHistory
- detectEmotion
- suggestTopic
- shouldTriggerEmergency

完整消息流程：录音 → ASR → 紧急检测 → Claude API → 情绪标记 → TTS → 存数据库 → 返回给前端 → 自动播放。

---

## 第七部分 · 管理后台

详见 [07-admin-backend.md](./07-admin-backend.md)。

核心页面：
- /admin/dashboard — 数据看板
- /admin/users — 用户管理
- /admin/ai-config — AI 陪聊配置（话题库 + 对话监控 + 银花人格调整）
- /admin/content — 社群内容
- /admin/learning — 学习内容
- /admin/orders — 订单
- /admin/volunteers — 志愿者
- /admin/narrative — 人生叙事
- /admin/charity — 公益

---

## 第八部分 · 落地页

详见 [08-landing-page.md](./08-landing-page.md)。

单页滚动六屏：Hero / 功能介绍 / 如何使用 / 用户感言 / 定价 / 底部。

移动端优先，字体比一般网站大一号。

---

## 第九部分 · API 路由

详见 [09-api-routes.md](./09-api-routes.md)。

核心：
- /api/chat/* — AI 陪聊
- /api/community/* — 社群
- /api/orders/* — 订单
- /api/subscriptions/* — 订阅
- /api/admin/* — 管理后台数据
- /api/webhook/* — 支付与微信回调

统一响应格式：`{ success, data | error }`。

---

## 第十部分 · 业务逻辑

详见 [10-business-logic.md](./10-business-logic.md)。

关键流程：
- AI 陪聊消息处理
- 紧急联络流程
- 订阅过期 cron
- 线下陪伴匹配
- 人生叙事订单流转
- 计费逻辑
- 推荐佣金
- 限流与防滥用

---

## 第十一部分 · 环境变量

详见 [11-env-config.md](./11-env-config.md)。

主要分类：
- 数据库 / NextAuth / 微信登录
- Claude API
- 腾讯云 (ASR/TTS/SMS)
- Azure TTS（备选）
- Supabase Storage
- Redis
- 微信支付
- 站点配置 / 监控 / 特性开关

---

## 第十二部分 · Tailwind 主题

详见 [12-tailwind-theme.md](./12-tailwind-theme.md)。

silver 色阶（暖橙）：50 - 900
sage 色阶（深绿辅助）：400 - 600
elderly-* 字号：18 / 22 / 28 / 36 / 48 px
动画：pulse-slow, voice-wave

---

## 第十三部分 · 实现顺序（重要）

详见 [13-implementation-phases.md](./13-implementation-phases.md)。

六个阶段：
1. 项目初始化
2. AI 陪聊核心
3. 用户系统 + 管理后台
4. 社群 + 订阅
5. 线下互助 + 人生叙事
6. 完善与部署

**请从阶段一开始，每完成一个阶段停下来汇报。**

---

## 第十四部分 · 具体要求

详见 [14-requirements.md](./14-requirements.md)。

核心要求：
- 代码完整、可运行、有类型
- 老人端大字大按钮高对比度
- 管理后台 sidebar + 内容区
- 移动端优先
- 安全（XSS / SQL 注入防护 / bcrypt / JWT）
- 性能（FCP < 1.5s）
- 软删除
- 降级策略
- E2E 测试覆盖核心场景

---

## 第十五部分 · 银花人格

详见 [15-yinhua-persona.md](./15-yinhua-persona.md)。

核心是把银花 System Prompt 作为常量保存在 `lib/prompts/yinhua-system-prompt.ts`，每次 Claude API 调用作为 system 参数传入。

紧急关键词清单也在该文件中。

第一周 7 个话题库初始数据要写到 prisma seed。

---

## 第十六部分 · 人生回忆叙事

详见 [16-narrative-product.md](./16-narrative-product.md)。

定价 980 / 1980 元。

朋友（HELPER 角色）通过管理后台：
1. 接收订单和素材包
2. 系统外完成 PDF 创作
3. 上传 PDF 交付
4. 客户反馈 + 一次免费修改
5. 自动计算分成

---

## 第十七部分 · 公益板块

详见 [17-charity-integration.md](./17-charity-integration.md)。

第一阶段：落地页 footer 互相导流 + UTM 追踪。

第二阶段：嵌入式集成（iframe 或子域名 + SSO）。

CharityProject 表支持企业 CSR 接口。

---

## 第十八部分 · 第一个月运营手册

详见 [18-operation-playbook.md](./18-operation-playbook.md)。

四周计划 + 系统需要支持的运营功能清单 + 收入目标（3000-6000 元/月）。

---

## 第十九部分 · 适老化 UX 清单

详见 [19-elderly-ux-checklist.md](./19-elderly-ux-checklist.md)。

完整验收清单，包括：视觉 / 交互 / 语音 / 文案 / 内容 / 容错 / 辅助功能 / 测试。

---

# 开始执行

请确认你已读完所有 19 个模块，然后从阶段一开始实现。

每完成一个阶段，给出：
1. 已实现功能清单
2. 如何手动测试
3. 已知问题
4. 下一阶段计划

我测试通过后再继续。
