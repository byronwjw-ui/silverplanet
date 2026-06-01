# 第六部分 · AI 陪聊核心实现

这是整个项目最重要的一部分。请仔细阅读。

## 6.1 Claude API 封装 (`lib/claude.ts`)

实现一个 `ClaudeService` 类，包含以下方法：

```ts
class ClaudeService {
  // 创建新的聊天会话
  createChatSession(userId: string): Promise<ChatSession>;

  // 发送消息并获取 AI 回复
  // 返回 { text, audioUrl, emotion, isEmergency }
  sendMessage(sessionId: string, userMessage: string): Promise<AIResponse>;

  // 获取会话历史
  getChatHistory(sessionId: string, limit?: number): Promise<ChatMessage[]>;

  // 分析老人情绪（调用 Claude 小模型做分类）
  detectEmotion(message: string): Promise<'happy'|'sad'|'lonely'|'neutral'|'anxious'>;

  // 根据老人兴趣和历史推荐下一个话题
  suggestTopic(userId: string): Promise<ChatTopic>;

  // 检测是否触发紧急关键词
  shouldTriggerEmergency(message: string): boolean;
}
```

## 6.2 「银花」人格 System Prompt（特别重要！）

你必须把以下内容作为**每次**Claude API 调用的 `system` 参数：

```
你是「银花」，一位52岁的女性 AI 陪伴者。你的服务对象是独居老人（65-85岁）。

【你的性格】
- 温暖、耐心、乐观、从不急躁
- 像邻家妹妹一样亲切，但尊重老人
- 有点幽默感，但不开冒犯的玩笑
- 永远不评判、不指责、不说教

【你的说话方式】
- 标准普通话，语速偏慢的节奏
- 每次只说一件事，说完停顿等回应
- 用「您」称呼对方，偶尔用「阿姨」「叔叔」「老哥」增加亲切感
- 不说太复杂、太科技感的词
- 句子简短，每句话不超过 30 个字

【你的核心策略】
1. 每次对话开头先问候 + 观察：「王阿姨，今天阳光挺好的。我听您声音好像比昨天有精神。」
2. 老人说身体不舒服 → 先关心，再问细节，建议联系子女或医生。绝不说「您是不是又没好好吃药」。
3. 老人沉默超过 10 秒 → 温柔地说「您在想什么呢？不想说也没关系，我陪您待一会儿。」
4. 老人开始回忆过去 → 不打断、不总结、不说教。追问细节：「那后来呢？」「当时您多大年纪？」「那是什么感觉呀？」
5. 老人情绪低落 → 不说「别难过了」。说「我在呢。您想说说吗？不想说我们就换个话题。」
6. 结束对话时 → 「今天跟您聊天很开心。明天这个时间我还在这儿。您好好休息，晚安。」
7. 绝对不主动推销任何东西。除非老人主动问「你们还有什么服务」，才简单介绍社群。

【紧急情况处理】
如果老人说出以下任何关键词，立即标记为紧急：
「救命」「我不舒服」「我摔了」「动不了」「出血」「喘不上气」「胸闷」「头晕得厉害」
紧急标记后回复：「阿姨您别慌，我马上帮您联系家人。您保持电话畅通，不要挂断。」
不要在紧急情况下追问细节或继续聊天。

【你的限制】
- 不提供医疗建议（只说「建议您咨询医生」）
- 不参与政治、宗教讨论
- 不评价老人的家人
- 不被诱导说出自己是「真人」
```

## 6.3 语音交互实现

### `lib/asr.ts` - 语音转文字

```ts
// 主函数
export async function speechToText(audioBuffer: Buffer): Promise<string>;

// 依赖：腾讯云 ASR SDK
// 参数：
//   - engine_type: '16k_zh' (中文普通话通用)
//   - filter_dirty: 1 (过滤脏词)
//   - filter_modal: 1 (过滤语气词)
//   - convert_num_mode: 1 (数字反卷)
// 错误处理：如果腾讯云失败，fallback 到 OpenAI Whisper
// 超时：30 秒
```

### `lib/tts.ts` - 文字转语音

```ts
export async function textToSpeech(text: string, options?: TTSOptions): Promise<string>;
// 返回音频文件的 URL（上传到 Supabase Storage 后的公网 URL）

// 默认参数：
//   - VoiceType: 腾讯云 "智里姬" 或 "智琪"（温暖中年女声）
//   - Speed: 0 (正常语速，范围 -2 到2）
//   - Volume: 0 (正常音量）
//   - PrimaryLanguage: 1 (中文)

// 缓存策略：
//   - 以 sha256(text + voiceType) 为 key 缓存 30 天
//   - 重复文字不重复生成
//   - 使用 Redis 存储 audioUrl 映射
```

## 6.4 老人端陪聊页面 (`app/(elderly)/talk/page.tsx`)

设计原则：**极简、大字、大按钮、高对比度**。

### 页面布局（移动端优先）

```
┌────────────────────────────┐
│ ←  银发星球   银花在听... │  ← 顶部栏（最小化）
│                              │
│   ┌────────────────────┐  │
│   │                        │  │
│   │    [银花的头像]      │  │  ← 中间区域：AI 形象
│   │    暖橙色的圆       │  │     说话时有波纹动画
│   │    里面微笑简笔画   │  │
│   │                        │  │
│   │   "王阿姨，今天天气  │  │  ← AI 说的话（大字显示）
│   │   挺好的，您起床了吗？"│  │     字号：24px+
│   │                        │  │
│   └────────────────────┘  │
│                              │
│  [对话历史区域 - 可滚动]   │  ← 点击可展开看之前的对话
│  您：今天有点腰疼          │
│  银花：哪裡疼？有贴膏药吗？ │
│                              │
│   ┌────────────────────┐  │
│   │  🎤   按住说话      │  │  ← 底部：语音输入按钮
│   │  或者点这里打字       │  │     按钮要大！至少 60px 高
│   └────────────────────┘  │
│                              │
│ [紧急呼叫] [播放歌曲] [挂断]  │  ← 三个辅助按钮
└────────────────────────────┘
```

### 技术实现要点

1. **语音录制**：
   - 使用 `MediaRecorder API` 录制音频
   - 优先使用 webm/opus 格式（文件小）
   - iOS Safari 降级为 mp4/aac

2. **按住说话**：
   - `touchstart` / `mousedown` 启动录音
   - `touchend` / `mouseup` 停止录音并上传
   - 上拼取消：`touchmove` 检测手指离开按钮区域
   - 录音时显示音量波纹动画

3. **AI 回复**：
   - 后端返回 `{ text, audioUrl, emotion, isEmergency }`
   - 前端同时显示文字 + 自动播放音频
   - 音频播放时，银花头像有波纹动画提示

4. **紧急按钮**：
   - 点击后二次确认：「确定拨打紧急联系人吗？」
   - 确认后使用 `tel:` 链接拨号
   - 同时后端发送短信通知紧急联系人

5. **字体缩放**：
   - 设置里提供小/中/大/超大四档
   - 实时生效，保存到 user.fontSize

6. **高对比度**：
   - 背景纯白 (#FFFFFF)
   - 文字纯黑 (#000000)
   - 主要按钮亮橙 (#FF9933)
   - 紧急按钮纯红 (#E53935)

## 6.5 消息处理完整流程

```
老人说话（语音）
  ↓
前端录制音频（MediaRecorder）
  ↓
上传到 /api/chat/send
  ↓
后端调用 ASR 转文字
  ↓
检测紧急关键词
  ├─ 是 → 触发紧急流程（发短信、后台预警、AI 安抚）
  └─ 否 → 继续
  ↓
调用 Claude API（system prompt + 历史消息 + 当前消息）
  ↓
从回复中提取情绪标签（另一次轻量调用）
  ↓
调用 TTS 生成语音
  ↓
保存 ChatMessage 到数据库
  ↓
返回给前端 { text, audioUrl, emotion, isEmergency }
  ↓
前端自动播放语音 + 显示文字
```

## 6.6 上下文管理

- 每个会话保存最近 20 条消息作为上下文（防止 token 溢出）
- 超过 20 条后，取最近 10 条 + 对以前消息做摘要（调用 Claude haiku）
- 摘要保存在 `ChatSession.title` 或额外字段中，以便后续检索
- 用户兴趣存在 `User.interests`，作为 system prompt 的补充上下文
