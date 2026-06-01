# 第二部分 · 技术栈选择

## 总览

| 层级 | 技术 | 备注 |
|------|------|------|
| 前端框架 | **Next.js 14** + TypeScript | App Router |
| 样式 | **Tailwind CSS** | 含适老化字号自定义 |
| 后端 | **Next.js API Routes** + Prisma ORM | 全栈一体 |
| 数据库 | **PostgreSQL** | 推荐 Supabase 免费层或 Neon |
| 认证 | **NextAuth.js** | 支持微信登录 + 手机号登录 |
| AI 对话 | **Claude API** (anthropic-sdk) | 使用「银花」System Prompt |
| 语音合成 (TTS) | 腾讯云 TTS / 微软 Azure TTS | 温暖中年女声 |
| 语音识别 (ASR) | 腾讯云 ASR / OpenAI Whisper | 支持中老年口音 |
| 文件存储 | Supabase Storage / AWS S3 | 老照片、语音录音、PDF |
| 支付 | 微信支付 JSAPI | 先做手动确认，后续接入 |
| 部署 | **Vercel** | 自定义域名 |
| 实时通信 | WebSocket / SSE | AI 陪聊实时对话 |
| 缓存 | Redis (Vercel KV 或 Upstash) | 对话 session 管理 |
| 短信 | 腾讯云 SMS | 紧急联络通知 |
| 监控 | Sentry (可选) | 错误追踪 |

## 选型理由

### 为什么选 Next.js 14 + App Router
- 全栈一体，前后端在同一个仓库，节省运维成本
- 内置 SSR、ISR，落地页 SEO 友好
- 与 Vercel 一键部署
- App Router 支持 Server Components，减少前端 bundle

### 为什么选 PostgreSQL + Prisma
- 关系型数据库适合「用户—家庭—订阅—订单」这类强关系数据
- Prisma 提供类型安全的 ORM，配合 TypeScript 减少 bug
- Supabase / Neon 都有慷慨的免费层，0 成本起步

### 为什么选 Claude API 作为对话引擎
- Claude 在中文长对话、情感共情场景下表现优于 GPT-4
- 支持长 system prompt，适合定义「银花」复杂人格
- API 稳定，价格透明

### 为什么选腾讯云 TTS/ASR（首选）
- 中文普通话识别准确率高
- 「温暖女声」音色适合老人陪伴场景
- 国内访问稳定，延迟低
- 价格便宜（按量计费，月预算 200-500 元覆盖 50-100 老人）

### 为什么先做 PWA 而不是原生 App
- 老人「添加到桌面」即可像 App 一样使用
- 无需上架审核，迭代快
- 一套代码覆盖 iOS / Android
- MVP 阶段够用，后续再开发原生 App

## 月度运营成本预估（服务 50-100 位老人）

| 项目 | 月成本 |
|------|--------|
| Vercel Pro（可选，免费层够用） | $0 - $20 |
| Supabase (免费层) | $0 |
| 腾讯云 ASR | ¥50 - ¥100 |
| Claude API | ¥200 - ¥500 |
| 腾讯云 TTS | ¥50 - ¥100 |
| 腾讯云 SMS | ¥20 - ¥50 |
| Upstash Redis (免费层) | $0 |
| **合计** | **约 ¥300 - ¥700 / 月** |
