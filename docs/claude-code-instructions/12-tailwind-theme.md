# 第十二部分 · Tailwind 主题配置

请在 `tailwind.config.ts` 中添加银发星球的主题色彩。

## 主色系

银发星球品牌色：暖橙（主） + 米白（背景） + 深绿（点缀）。

silver 色阶（暖橙系）：

- silver-50  #FFF8F0  最暖的米白
- silver-100 #FFF0E0
- silver-200 #FFE0C0
- silver-300 #FFD0A0
- silver-400 #FFC080
- silver-500 #FF9933  主色：暖橙
- silver-600 #E68A2E
- silver-700 #CC7A29
- silver-800 #B36B24
- silver-900 #995C1F

sage 色阶（深绿系，辅助色）：

- sage-400 #7D9B76
- sage-500 #5C7A54  辅助色：深绿
- sage-600 #4A6344

## 适老化字号系统

所有老人端页面必须使用 `elderly-*` 字号类名，确保文字够大：

- elderly-sm  18px / 行高 28px
- elderly-base 22px / 行高 32px
- elderly-lg   28px / 行高 38px
- elderly-xl   36px / 行高 44px
- elderly-2xl  48px / 行高 56px

## 动画

- pulse-slow：3s 缓慢呼吸动画（用于"银花在听..."头像）
- voice-wave：1.5s 声音波纹动画（说话时显示）

## 配置代码示例

theme.extend.colors 添加 silver 和 sage 两套色阶。

theme.extend.fontSize 添加 elderly-sm 到 elderly-2xl 五档。

theme.extend.animation 添加 pulse-slow 和 voice-wave。

theme.extend.keyframes 中定义 voiceWave 关键帧：从 scale(1) opacity(0.8) 到 scale(1.2) opacity(0.4) 再回到原点。

## 圆角与阴影

- 老人端按钮统一 rounded-2xl（16px 圆角）
- 卡片使用 rounded-xl + shadow-md
- 紧急按钮使用 rounded-full（圆形）

## 字体加载

使用思源黑体（Noco Sans SC）或苹方作为主字体。在 globals.css 中通过 next/font 加载。
