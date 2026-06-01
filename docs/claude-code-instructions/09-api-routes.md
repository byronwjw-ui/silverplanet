# 第九部分 · API 路由设计

所有 API 使用 Next.js App Router 的 Route Handlers（`app/api/.../route.ts`）。

## 认证相关

| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/api/auth/register` | 注册（手机号+验证码） |
| POST | `/api/auth/login` | 登录 |
| POST | `/api/auth/wechat` | 微信登录 |
| POST | `/api/auth/send-code` | 发送验证码 |
| GET  | `/api/auth/session` | 获取当前会话 |
| POST | `/api/auth/logout` | 退出登录 |

## AI 陪聊

| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/api/chat/session` | 创建新会话 |
| POST | `/api/chat/send` | 发送消息（文字或语音） |
| GET  | `/api/chat/session/[id]` | 获取会话详情+历史消息 |
| GET  | `/api/chat/sessions` | 获取用户的所有会话 |
| POST | `/api/chat/session/[id]/end` | 结束会话 |
| POST | `/api/chat/emergency` | 触发紧急联络 |
| GET  | `/api/chat/topics` | 获取话题推荐 |

## 社群

| 方法 | 路径 | 说明 |
|------|------|------|
| GET  | `/api/community/posts` | 帖子列表（分页、分类筛选） |
| POST | `/api/community/posts` | 发帖 |
| GET  | `/api/community/posts/[id]` | 帖子详情 |
| POST | `/api/community/posts/[id]/comments` | 评论 |
| POST | `/api/community/posts/[id]/like` | 点赞 |
| DELETE | `/api/community/posts/[id]` | 删除帖子（管理员） |

## 学习内容

| 方法 | 路径 | 说明 |
|------|------|------|
| GET  | `/api/learning` | 学习内容列表 |
| POST | `/api/learning` | 发布学习内容（管理员） |
| PUT  | `/api/learning/[id]` | 更新 |
| DELETE | `/api/learning/[id]` | 删除 |

## 用户

| 方法 | 路径 | 说明 |
|------|------|------|
| GET  | `/api/users` | 用户列表（管理员） |
| GET  | `/api/users/[id]` | 用户详情 |
| PUT  | `/api/users/[id]` | 更新用户信息 |
| GET  | `/api/users/[id]/stats` | 用户统计 |
| GET  | `/api/users/me` | 获取当前用户 |
| PUT  | `/api/users/me/font-size` | 修改字体大小 |

## 订阅与订单

| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/api/subscriptions` | 创建订阅 |
| GET  | `/api/subscriptions/current` | 当前订阅状态 |
| POST | `/api/orders` | 创建订单 |
| GET  | `/api/orders` | 订单列表 |
| PUT  | `/api/orders/[id]` | 更新订单状态 |
| POST | `/api/orders/[id]/pay` | 支付确认 |

## 志愿者

| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/api/volunteers/apply` | 申请成为志愿者 |
| GET  | `/api/volunteers` | 志愿者列表（管理员） |
| PUT  | `/api/volunteers/[id]` | 审核志愿者 |
| POST | `/api/volunteers/match` | 匹配志愿者到订单 |

## 人生叙事

| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/api/narrative/orders` | 创建叙事订单 |
| GET  | `/api/narrative/orders` | 叙事订单列表 |
| PUT  | `/api/narrative/orders/[id]` | 更新订单状态 |
| POST | `/api/narrative/orders/[id]/materials` | 上传素材 |
| POST | `/api/narrative/orders/[id]/deliver` | 交付成品（朋友上传） |

## 公益

| 方法 | 路径 | 说明 |
|------|------|------|
| GET  | `/api/charity/projects` | 公益项目列表 |
| GET  | `/api/charity/projects/[id]` | 公益项目详情 |

## 管理后台统计

| 方法 | 路径 | 说明 |
|------|------|------|
| GET  | `/api/admin/stats` | 核心数据 |
| GET  | `/api/admin/chat-stats` | AI 陪聊统计 |
| GET  | `/api/admin/revenue` | 收入统计 |
| GET  | `/api/admin/emotion-alerts` | 情绪预警列表 |

## Webhook

| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/api/webhook/wechat-pay` | 微信支付回调 |
| POST | `/api/webhook/wechat-event` | 微信事件推送 |

## 统一响应格式

```ts
// 成功
{ success: true, data: T }

// 失败
{ success: false, error: { code: string, message: string, details?: any } }
```

## 状态码规范

- 200 成功
- 201 创建成功
- 400 参数错误
- 401 未认证
- 403 无权限
- 404 资源不存在
- 429 请求过频
- 500 服务器错误

## Rate Limiting

- 语音上传：每分钟 30 次
- AI 对话：每分钟 20 次
- 验证码：每手机号每小时 5 次
