# 第三部分 · 项目文件结构

```
silver-planet/
├── app/                       # Next.js App Router
│   ├── layout.tsx             # 根布局（字体、主题、Provider）
│   ├── page.tsx               # 首页 / 落地页
│   ├── globals.css            # 全局样式
│   │
│   ├── (elderly)/             # 老人端（极简界面，路由组）
│   │   ├── layout.tsx         # 大字模式 layout
│   │   ├── talk/              # AI 陪聊
│   │   │   └── page.tsx
│   │   ├── community/         # 社群浏览
│   │   │   └── page.tsx
│   │   └── profile/           # 个人页
│   │       └── page.tsx
│   │
│   ├── (admin)/               # 管理后台（我用）
│   │   ├── layout.tsx         # sidebar + 内容区
│   │   ├── dashboard/         # 数据看板
│   │   │   └── page.tsx
│   │   ├── users/             # 用户管理
│   │   │   └── page.tsx
│   │   ├── content/           # 社群内容管理
│   │   │   └── page.tsx
│   │   ├── orders/            # 订单管理
│   │   │   └── page.tsx
│   │   ├── volunteers/        # 志愿者管理
│   │   │   └── page.tsx
│   │   └── ai-config/         # AI 陪聊配置
│   │       └── page.tsx
│   │
│   └── api/                   # API Routes
│       ├── auth/              # 认证相关
│       ├── chat/              # AI 陪聊
│       ├── community/         # 社群
│       ├── orders/            # 订单
│       ├── users/             # 用户
│       ├── volunteers/        # 志愿者
│       └── webhook/           # 支付回调
│
├── components/
│   ├── elderly/               # 老人端专用组件（大字大按钮）
│   │   ├── BigButton.tsx
│   │   ├── VoiceInput.tsx     # 按住说话
│   │   ├── VoiceOutput.tsx    # 语音播放
│   │   ├── ChatBubble.tsx
│   │   ├── EmergencyButton.tsx
│   │   └── ElderlyNav.tsx
│   │
│   ├── admin/                 # 管理后台组件
│   │   ├── Sidebar.tsx
│   │   ├── DataCard.tsx
│   │   ├── UserTable.tsx
│   │   └── ContentEditor.tsx
│   │
│   ├── shared/                # 共享组件
│   │   ├── LoadingSpinner.tsx
│   │   ├── EmptyState.tsx
│   │   └── Toast.tsx
│   │
│   └── landing/               # 落地页组件
│       ├── Hero.tsx
│       ├── Features.tsx
│       ├── Pricing.tsx
│       └── Testimonials.tsx
│
├── lib/
│   ├── db.ts                  # Prisma 客户端单例
│   ├── auth.ts                # NextAuth 配置
│   ├── claude.ts              # Claude API 封装
│   ├── tts.ts                 # 文字转语音
│   ├── asr.ts                 # 语音转文字
│   ├── redis.ts               # Redis 客户端
│   ├── sms.ts                 # 短信发送
│   ├── wechat-pay.ts          # 微信支付
│   └── utils.ts               # 工具函数
│
├── prisma/
│   └── schema.prisma          # 数据库模型（见模块 04）
│
├── hooks/
│   ├── useVoiceChat.ts        # 语音聊天 hook
│   ├── useElderlyMode.ts      # 老人模式检测
│   ├── useAdminAuth.ts        # 管理员权限
│   └── useEmergencyDetect.ts  # 紧急关键词检测
│
├── types/
│   └── index.ts               # TypeScript 类型定义
│
├── public/
│   ├── images/                # 静态图片
│   ├── icons/                 # 图标
│   ├── audio/                 # 提示音
│   └── manifest.json          # PWA manifest
│
├── scripts/
│   ├── seed.ts                # 数据库初始化
│   └── cron-subscription.ts   # 订阅过期定时任务
│
├── .env.example
├── .env.local                 # 本地环境变量（gitignore）
├── tailwind.config.ts
├── next.config.js
├── tsconfig.json
├── package.json
└── README.md
```

## 命名规范

| 类型 | 规则 | 示例 |
|------|------|------|
| 组件文件 | PascalCase | `BigButton.tsx` |
| Hook | camelCase, `use` 开头 | `useVoiceChat.ts` |
| 工具函数 | camelCase | `formatDate.ts` |
| 页面 | 小写连字符 | `app/(elderly)/talk/page.tsx` |
| 类型 | PascalCase, `I` 不加前缀 | `User`, `ChatSession` |
| 常量 | 全大写下划线 | `MAX_MESSAGE_LENGTH` |

## 路由组说明

- `(elderly)`：老人端路由，启用大字模式 + 极简 UI
- `(admin)`：管理后台，需要 `ADMIN` 或 `SUPERADMIN` 权限
- 落地页 + 公共页面在根路径
