# 第四部分 · 数据库设计（Prisma Schema）

请按以下 Prisma Schema 创建数据库模型。

**重要原则：**
1. 所有模型必须支持软删除（`deletedAt` 字段，可选）
2. 所有时间字段使用 `DateTime` 类型
3. 所有主键使用 `cuid()`
4. 关键字段必须建索引（手机号、openId、email）

## 完整 schema.prisma

```prisma
// prisma/schema.prisma

generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

// ============ 用户系统 ============

model User {
  id               String    @id @default(cuid())
  name             String?
  phone            String?   @unique
  wechatOpenId     String?   @unique
  email            String?   @unique
  role             Role      @default(ELDERLY)
  avatarUrl        String?
  birthDate        DateTime?
  address          String?
  emergencyContact String?   // 紧急联系人电话
  emergencyName    String?   // 紧急联系人姓名

  // 老人端特有字段
  hearingLevel     Int?      // 听力水平 1-5, 5=极差
  fontSize         String?   @default("large") // small/medium/large/xlarge
  interests        String[]  // 兴趣标签: ["养花","广场舞","下棋"]

  // 关系
  familyMembers    FamilyMember[]
  subscriptions    Subscription[]
  chatSessions     ChatSession[]
  communityPosts   CommunityPost[]
  communityComments CommunityComment[]
  orders           Order[]
  volunteerProfile VolunteerProfile?
  narrativeOrders  NarrativeOrder[]
  elderlyMatches   CompanionMatch[] @relation("ElderlyMatches")
  volunteerMatches CompanionMatch[] @relation("VolunteerMatches")

  createdAt        DateTime  @default(now())
  updatedAt        DateTime  @updatedAt
  deletedAt        DateTime?

  @@index([phone])
  @@index([wechatOpenId])
  @@index([role])
}

enum Role {
  ELDERLY      // 老人用户
  FAMILY       // 子女/家属
  VOLUNTEER    // 志愿者
  HELPER       // 银发助教（社群里的活跃老人）
  ADMIN        // 管理员（我）
  SUPERADMIN   // 超级管理员
}

model FamilyMember {
  id                 String   @id @default(cuid())
  userId             String
  user               User     @relation(fields: [userId], references: [id])
  name               String
  phone              String
  relation           String   // 关系: "儿子","女儿","儿媳"等
  isEmergencyContact Boolean  @default(false)
  createdAt          DateTime @default(now())
  updatedAt          DateTime @updatedAt

  @@index([userId])
}

model VolunteerProfile {
  id            String          @id @default(cuid())
  userId        String          @unique
  user          User            @relation(fields: [userId], references: [id])
  status        VolunteerStatus @default(PENDING)
  serviceTypes  String[]        // 能提供的服务
  availableTime String?         // 空闲时间描述
  servedCount   Int             @default(0)
  rating        Float?          // 评分
  verifiedAt    DateTime?
  createdAt     DateTime        @default(now())
  updatedAt     DateTime        @updatedAt
}

enum VolunteerStatus {
  PENDING     // 待审核
  ACTIVE      // 活跃
  INACTIVE    // 暂停
  REJECTED    // 拒绝
}

// ============ AI 陪聊系统 ============

model ChatSession {
  id            String        @id @default(cuid())
  userId        String
  user          User          @relation(fields: [userId], references: [id])
  title         String?
  status        ChatStatus    @default(ACTIVE)
  totalMessages Int           @default(0)
  duration      Int?          // 总时长（秒）
  startedAt     DateTime      @default(now())
  endedAt       DateTime?
  messages      ChatMessage[]
  createdAt     DateTime      @default(now())
  updatedAt     DateTime      @updatedAt

  @@index([userId, status])
}

enum ChatStatus {
  ACTIVE
  ENDED
  ARCHIVED
}

model ChatMessage {
  id        String      @id @default(cuid())
  sessionId String
  session   ChatSession @relation(fields: [sessionId], references: [id])
  role      MessageRole
  content   String      @db.Text
  audioUrl  String?
  duration  Int?
  emotion   String?     // "happy","sad","lonely","neutral"
  topicTag  String?     // "天气","健康","家庭","回忆"
  isEmergency Boolean   @default(false)
  createdAt DateTime    @default(now())

  @@index([sessionId])
}

enum MessageRole {
  USER
  ASSISTANT
  SYSTEM
}

// AI 陪聊的话题库
model ChatTopic {
  id          String   @id @default(cuid())
  title       String
  description String?
  category    String   // "daily","memory","health","entertainment"
  openingLine String   @db.Text
  followUps   String[]
  tags        String[]
  isActive    Boolean  @default(true)
  priority    Int      @default(0)
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt

  @@index([category, isActive])
}

// ============ 社群系统 ============

model CommunityPost {
  id          String             @id @default(cuid())
  authorId    String
  author      User               @relation(fields: [authorId], references: [id])
  title       String
  content     String             @db.Text
  contentType ContentType        @default(TEXT)
  mediaUrls   String[]
  tags        String[]
  category    String             // "ai-learning","daily-life","health","hobby"
  isPinned    Boolean            @default(false)
  isFeatured  Boolean            @default(false)
  viewCount   Int                @default(0)
  likeCount   Int                @default(0)
  comments    CommunityComment[]
  createdAt   DateTime           @default(now())
  updatedAt   DateTime           @updatedAt
  deletedAt   DateTime?

  @@index([category, createdAt])
  @@index([authorId])
}

enum ContentType {
  TEXT
  IMAGE
  VIDEO
  AI_GENERATED
}

model CommunityComment {
  id        String             @id @default(cuid())
  postId    String
  post      CommunityPost      @relation(fields: [postId], references: [id])
  authorId  String
  author    User               @relation(fields: [authorId], references: [id])
  content   String             @db.Text
  parentId  String?
  parent    CommunityComment?  @relation("CommentReplies", fields: [parentId], references: [id])
  replies   CommunityComment[] @relation("CommentReplies")
  createdAt DateTime           @default(now())
  deletedAt DateTime?

  @@index([postId])
}

// 社群学习内容
model LearningContent {
  id          String    @id @default(cuid())
  title       String
  description String    @db.Text
  contentType String    // "video","article","challenge"
  mediaUrl    String?
  bodyText    String?   @db.Text
  difficulty  String    @default("beginner")
  category    String
  authorId    String
  isPublished Boolean   @default(false)
  publishedAt DateTime?
  viewCount   Int       @default(0)
  createdAt   DateTime  @default(now())
  updatedAt   DateTime  @updatedAt

  @@index([category, isPublished])
}

// ============ 订单与订阅系统 ============

model Subscription {
  id        String    @id @default(cuid())
  userId    String
  user      User      @relation(fields: [userId], references: [id])
  planType  PlanType
  status    SubStatus @default(ACTIVE)
  startedAt DateTime  @default(now())
  expiresAt DateTime
  autoRenew Boolean   @default(false)
  amount    Float
  createdAt DateTime  @default(now())
  updatedAt DateTime  @updatedAt

  @@index([userId, status])
  @@index([expiresAt])
}

enum PlanType {
  COMMUNITY_FREE      // 社群免费体验（14天）
  COMMUNITY_MONTHLY   // 社群月费 19.9元
  COMMUNITY_QUARTERLY // 社群季费 49元
  AI_CHAT_FREE        // AI陪聊免费版
  AI_CHAT_PREMIUM     // AI陪聊升级版 9.9-29.9元/月
}

enum SubStatus {
  ACTIVE
  EXPIRED
  CANCELLED
  TRIAL
}

model Order {
  id          String      @id @default(cuid())
  userId      String
  user        User        @relation(fields: [userId], references: [id])
  type        OrderType
  amount      Float
  status      OrderStatus @default(PENDING)
  description String?
  paidAt      DateTime?
  completedAt DateTime?
  createdAt   DateTime    @default(now())
  updatedAt   DateTime    @updatedAt

  // 线下陪伴订单特有字段
  volunteerId String?
  serviceType String?
  serviceDate DateTime?
  feedback    String?     @db.Text

  @@index([userId, status])
  @@index([type, status])
}

enum OrderType {
  SUBSCRIPTION       // 订阅
  NARRATIVE_PERSONAL // 人生叙事个人版
  NARRATIVE_FAMILY   // 人生叙事家庭版
  COMPANION_WALK     // 陪伴散步
  COMPANION_SHOPPING // 陪伴买菜
  COMPANION_HOSPITAL // 陪诊
  COMPANION_OTHER    // 其他陪伴
}

enum OrderStatus {
  PENDING     // 待支付
  PAID        // 已支付
  PROCESSING  // 处理中
  MATCHING    // 匹配志愿者中
  IN_PROGRESS // 服务进行中
  COMPLETED   // 已完成
  CANCELLED   // 已取消
  REFUNDED    // 已退款
}

// 人生回忆叙事订单
model NarrativeOrder {
  id              String              @id @default(cuid())
  userId          String
  user            User                @relation(fields: [userId], references: [id])
  orderId         String?             @unique
  status          NarrativeStatus     @default(MATERIAL_COLLECTING)
  materials       NarrativeMaterial[]
  deliveredPdfUrl String?
  deliveredAt     DateTime?
  feedback        String?             @db.Text
  rating          Int?
  createdAt       DateTime            @default(now())
  updatedAt       DateTime            @updatedAt

  @@index([userId, status])
}

enum NarrativeStatus {
  MATERIAL_COLLECTING  // 收集素材中
  MATERIAL_READY       // 素材就绪
  WRITING              // 朋友创作中
  DELIVERED            // 已交付
  REVISING             // 修改中
  COMPLETED            // 完成
}

model NarrativeMaterial {
  id          String         @id @default(cuid())
  orderId     String
  order       NarrativeOrder @relation(fields: [orderId], references: [id])
  type        String         // "voice_recording","text","photo","question_answer"
  content     String?        @db.Text
  fileUrl     String?
  description String?
  createdAt   DateTime       @default(now())

  @@index([orderId])
}

// ============ 线下互助系统 ============

model CompanionMatch {
  id                String      @id @default(cuid())
  orderId           String      @unique
  elderlyId         String
  elderly           User        @relation("ElderlyMatches", fields: [elderlyId], references: [id])
  volunteerId       String
  volunteer         User        @relation("VolunteerMatches", fields: [volunteerId], references: [id])
  serviceType       String
  serviceDate       DateTime
  status            MatchStatus @default(UPCOMING)
  elderlyFeedback   String?     @db.Text
  volunteerFeedback String?     @db.Text
  elderlyRating     Int?
  volunteerRating   Int?
  createdAt         DateTime    @default(now())
  updatedAt         DateTime    @updatedAt

  @@index([elderlyId])
  @@index([volunteerId])
  @@index([serviceDate])
}

enum MatchStatus {
  UPCOMING
  IN_PROGRESS
  COMPLETED
  CANCELLED
  DISPUTED
}

// ============ 公益板块 ============

model CharityProject {
  id            String        @id @default(cuid())
  title         String
  description   String        @db.Text
  category      String
  targetAmount  Float?
  raisedAmount  Float         @default(0)
  status        CharityStatus @default(ACTIVE)
  coverImage    String?
  partnerName   String?       // 合作伙伴（如侄女平台名称）
  createdAt     DateTime      @default(now())
  updatedAt     DateTime      @updatedAt

  @@index([category, status])
}

enum CharityStatus {
  DRAFT
  ACTIVE
  PAUSED
  COMPLETED
}
```

## 初始化命令

```bash
npx prisma migrate dev --name init
npx prisma generate
npx prisma db seed   # 可选，运行 seed 脚本
```

## 索引设计要点

- `User.phone`、`User.wechatOpenId`、`User.email` 已是 unique，自动建索引
- `ChatSession(userId, status)` 复合索引，用于查询用户的活跃会话
- `ChatMessage.sessionId` 索引，用于查询会话历史
- `Subscription.expiresAt` 索引，用于 cron 扫描即将过期的订阅
- `Order(userId, status)`、`Order(type, status)` 复合索引
- `CommunityPost(category, createdAt)` 复合索引，用于按分类时间倒序展示
