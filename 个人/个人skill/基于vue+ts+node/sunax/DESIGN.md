# Synex Cinematic Scroll — 系统设计文档

> 版本：1.0.0 · 最后更新：2026-04-18

---

## 目录

1. [项目概述](#1-项目概述)
2. [架构设计](#2-架构设计)
3. [技术选型依据](#3-技术选型依据)
4. [核心功能模块说明](#4-核心功能模块说明)
5. [数据流程设计](#5-数据流程设计)
6. [接口定义规范](#6-接口定义规范)
7. [关键算法实现原理](#7-关键算法实现原理)
8. [UI/UX 设计理念](#8-uiux-设计理念)
9. [性能优化策略](#9-性能优化策略)
10. [安全设计考量](#10-安全设计考量)
11. [未来扩展规划](#11-未来扩展规划)

---

## 1. 项目概述

### 1.1 产品定位

Synex Cinematic Scroll 是一个**企业级电影感滚动叙事官网**，面向高端品牌展示场景。核心体验是通过 GSAP + ScrollTrigger 驱动的分镜式整屏切换，实现 3D 视差、Text Masking、玻璃卡片层叠、白场/模糊过渡等电影级视觉效果。

### 1.2 设计目标

| 目标 | 说明 |
|------|------|
| 电影感叙事 | 每个 section 为独立分镜，具备 bg/mid/fg 三层视差 + 入场/停驻/离场完整生命周期 |
| 企业级工程 | 严格分层架构、命名规范、质量门禁、类型安全 |
| 响应式优先 | 移动优先，clamp() 流式排版，5 级视口全覆盖 |
| 可维护性 | Design Token 驱动、composable 复用、模块边界清晰 |
| 可扩展性 | Monorepo 架构、后端模块化、场景组件可插拔 |

### 1.3 技术全景

```
┌─────────────────────────────────────────────────────────┐
│                     用户浏览器                           │
│  Vue 3 + TypeScript + SCSS + GSAP + ScrollTrigger       │
│  ┌─────────┐ ┌──────────┐ ┌──────────┐ ┌────────────┐  │
│  │  AppNav  │ │ 7 Scenes │ │Composables│ │Design Tokens│  │
│  └─────────┘ └──────────┘ └──────────┘ └────────────┘  │
└────────────────────────┬────────────────────────────────┘
                         │ HTTP / REST API
┌────────────────────────▼────────────────────────────────┐
│                   Node.js + Express 5                    │
│  ┌────────────┐ ┌────────────┐ ┌─────────────────────┐ │
│  │ Controller  │ │  Service   │ │    Repository        │ │
│  └────────────┘ └────────────┘ └─────────────────────┘ │
└────────────────────────┬────────────────────────────────┘
                         │ Mongoose ODM
┌────────────────────────▼────────────────────────────────┐
│                      MongoDB                             │
│            Content Collection (7 sections)               │
└─────────────────────────────────────────────────────────┘
```

---

## 2. 架构设计

### 2.1 Monorepo 结构

```
synex-cinematic-scroll/
├── web/                    # 前端工作区
│   ├── src/
│   │   ├── app/            # 应用层（路由、布局、Provider）
│   │   ├── features/       # 业务功能域（按场景划分）
│   │   ├── shared/         # 共享层（组件、composables、样式、类型）
│   │   └── assets/         # 静态资源
│   └── public/             # 公共资源（sitemap、robots、favicon）
├── server/                 # 后端工作区
│   └── src/
│       ├── config/         # 环境配置
│       └── modules/        # 业务模块（按领域划分）
├── skills/                 # 开发规范文档
└── package.json            # Monorepo 根配置
```

### 2.2 前端分层架构

```
┌─────────────────────────────────────────┐
│              Pages（页面组装层）          │  仅做组件编排，不承载业务逻辑
├─────────────────────────────────────────┤
│            Features（业务功能域）         │  按场景划分：hero / features /
│                                          │  focus / network / compare / cta
├─────────────────────────────────────────┤
│             Shared（共享层）             │  components / composables /
│                                          │  constants / types / styles
└─────────────────────────────────────────┘
```

**依赖方向（强制）**：

```
Pages → Features/* → Shared/*
```

**禁止**：

- `Shared → Features`（共享层不得反向依赖业务层）
- `Features/A → Features/B` 内部文件（跨 feature 通过 shared 交互）
- 页面组件直接访问数据层

### 2.3 后端分层架构

```
┌─────────────────────────────────────────┐
│          Routes（路由定义层）             │  HTTP 方法 + 路径映射
├─────────────────────────────────────────┤
│        Controllers（协议层）             │  req/res 处理，不写业务判断
├─────────────────────────────────────────┤
│         Services（业务层）               │  业务流程编排，不关心 HTTP 细节
├─────────────────────────────────────────┤
│       Repositories（数据访问层）          │  数据库查询封装，字段投影
├─────────────────────────────────────────┤
│          Models（数据模型层）             │  Schema 定义，索引声明
└─────────────────────────────────────────┘
```

**依赖方向（强制）**：

```
Routes → Controllers → Services → Repositories → Models
```

**禁止**：

- `Controllers → Repositories`（必须通过 Service）
- `Services` 直接操作 HTTP req/res
- 跨模块直接引用对方 Repository

### 2.4 场景组件架构

每个分镜场景遵循统一的四层容器结构：

```html
<section class="scene scene-{name}">
  <div class="scene__bg">       <!-- 背景层：渐变/位图/氛围 -->
  <div class="scene__mid">      <!-- 主体层：核心视觉锚点 -->
  <div class="scene__fg">       <!-- 前景层：遮挡/穿越/雾层 -->
  <div class="scene__content">  <!-- 内容层：标题/正文/按钮/数据 -->
</section>
```

各层职责与动画分工：

| 层级 | 内容职责 | 动画职责 | 视差速度系数 |
|------|----------|----------|-------------|
| `bg` | 环境、空间、天空、远景氛围 | 慢速大位移、轻微缩放、模糊离场 | 0.45 |
| `mid` | 主体建筑、核心装置、主卡片 | 主叙事锚点、主体入场、中速视差 | 1.00 |
| `fg` | 岩石、玻璃、雾层、边缘遮挡物 | 遮挡、穿越感、镜头擦过 | 1.35 |
| `content` | 标题、正文、按钮、数据 UI | 入场/离场转场、文字遮罩 | — |

---

## 3. 技术选型依据

### 3.1 前端技术栈

| 技术 | 版本 | 选型理由 |
|------|------|----------|
| **Vue 3** | 3.5+ | Composition API + `<script setup>` 提供更优的逻辑复用和类型推导；响应式系统适合动画密集场景 |
| **TypeScript** | 5.7+ | `strict: true` 模式确保类型安全；IDE 支持优秀；减少运行时错误 |
| **SCSS** | — | BEM 命名 + 嵌套 + 变量 + mixin 适合 Design Token 体系；`@use` 模块化避免全局污染 |
| **Vite** | 6+ | ESM 原生支持、HMR 极速、构建配置简洁；路由级懒加载天然支持 |
| **GSAP** | 3.12+ | 业界最强 Web 动画引擎；ScrollTrigger 提供精确的滚动驱动控制；SplitText 支持文字遮罩；商业级性能 |
| **Vue Router** | 4.5+ | Vue 官方路由；HTML5 History 模式；懒加载支持 |

### 3.2 后端技术栈

| 技术 | 版本 | 选型理由 |
|------|------|----------|
| **Express** | 5.1+ | Node.js 最成熟的 Web 框架；5.x 支持 async 中间件；生态丰富 |
| **Mongoose** | 8.13+ | MongoDB ODM；Schema 驱动 + 类型安全；中间件钩子；索引声明 |
| **Helmet** | 8+ | 自动设置安全 HTTP 头；零配置生产级安全基线 |
| **Compression** | 1.8+ | gzip 响应压缩；减少传输体积 |

### 3.3 工程化工具链

| 工具 | 用途 | 选型理由 |
|------|------|----------|
| **pnpm** | 包管理 | Monorepo workspace 原生支持；硬链接节省磁盘；严格依赖隔离 |
| **ESLint 9** | 代码检查 | Flat config 新范式；TypeScript + Vue 统一配置 |
| **Prettier** | 代码格式化 | 与 ESLint 集成；团队格式统一 |
| **Stylelint** | 样式检查 | SCSS 规范 + 属性排序 |
| **Vitest** | 单元测试 | Vite 原生测试框架；ESM 支持；jsdom 环境 |

### 3.4 未选型说明

| 技术 | 未选理由 |
|------|----------|
| **Pinia** | 项目为纯展示页，无复杂跨组件状态共享需求；composables + props 足够 |
| **Nuxt** | 当前为单页品牌展示，SSR 收益有限；如需 SEO 强化可后续迁移 |
| **Tailwind CSS** | 项目需要高度定制化的动画样式和 BEM 语义类，Utility-first 不适合 |
| **Zod / Joi** | 后端 DTO 仅有类型定义，运行时校验为后续增强项 |

---

## 4. 核心功能模块说明

### 4.1 场景模块（7 段分镜叙事）

#### S01 — Hero 首屏

- **主叙事目标**：建立世界观，传达品牌核心价值
- **视觉结构**：深蓝渐变背景 → 蓝色径向光晕 → 底部雾化过渡 → 居中内容
- **内容**：Now Available 徽章 + 大标题 + 副标题 + 双 CTA 按钮
- **动画**：三层视差滚动 + 内容入场/离场 + 呼吸灯徽章
- **文件**：[HeroScene.vue](web/src/features/hero/components/HeroScene.vue)

#### S02 — 三列能力卡

- **主叙事目标**：解释核心能力
- **视觉结构**：渐变背景 → 三列玻璃态卡片
- **内容**：Real-time Sync / Enterprise Security / Smart Analytics
- **动画**：卡片从下方 + blur 逐个入场，hover 上浮 + 边框高亮
- **文件**：[FeaturesScene.vue](web/src/features/features-section/components/FeaturesScene.vue)

#### S03 — 中央焦点卡

- **主叙事目标**：展示核心卖点
- **视觉结构**：左右分栏 — 左侧文案 + 统计数字，右侧玻璃卡片
- **内容**：统一数据引擎 + 99.9% Uptime / 50ms Latency / 10M+ Events/Sec
- **动画**：卡片 scale + blur 入场，曲线背景 scaleY 生长
- **文件**：[FocusScene.vue](web/src/features/focus/components/FocusScene.vue)

#### S04 — 节点网络图

- **主叙事目标**：展示体系连接
- **视觉结构**：SVG 连线 + 7 个胶囊节点（Core 居中 + 6 服务环绕）
- **动画**：线条 strokeDashoffset 逐条绘制，节点弹性入场
- **文件**：[NetworkScene.vue](web/src/features/network/components/NetworkScene.vue)

#### S05 — Integrations 生态环

- **主叙事目标**：展示生态连接
- **视觉结构**：flex-wrap 胶囊标签矩阵
- **内容**：12 个集成（Snowflake / PostgreSQL / Redis / Kafka 等）
- **动画**：从中心向外的弹性入场
- **文件**：[IntegrationsScene.vue](web/src/features/network/components/IntegrationsScene.vue)

#### S06 — 暗色对比表

- **主叙事目标**：建立价值对比
- **视觉结构**：玻璃态表格，Synex 列高亮
- **内容**：6 行特性对比（勾/叉标记）
- **动画**：整表 blur + yPercent 入场
- **文件**：[CompareScene.vue](web/src/features/compare/components/CompareScene.vue)

#### S07 — CTA + Footer

- **主叙事目标**：收束转化
- **视觉结构**：径向光晕 + CTA 区域 + 三列 Footer
- **动画**：内容 blur + yPercent 入场
- **文件**：[CtaScene.vue](web/src/features/cta/components/CtaScene.vue)

### 4.2 导航模块

- **固定导航栏**：`position: fixed`，z-index 全局最高（1000）
- **桌面端**：Logo + 4 个锚点链接 + Sign In / Get Started 按钮
- **移动端**：汉堡菜单（三线 → X 动画），全屏覆盖式导航
- **毛玻璃效果**：`backdrop-filter: blur(12px)` + 半透明深色背景
- **文件**：[AppNav.vue](web/src/shared/components/AppNav.vue)

### 4.3 动画系统模块

- **核心 composable**：[use-cinematic-scene.ts](web/src/shared/composables/use-cinematic-scene.ts)
  - `setupParallax`：三层视差（bg=0.45 / mid=1.0 / fg=1.35）
  - `setupTextMasking`：文字遮罩入场（yPercent: 110 → 0）
  - `setupContentEnter`：内容入场（blur + opacity + scale + yPercent）
  - `setupSceneExit`：场景离场（反向转场）
- **参数预设**：[animation.ts](web/src/shared/constants/animation.ts)
  - 缓动：`power3.out`（入场）/ `power2.inOut`（离场）
  - 时长：0.72s（入场）/ 0.52s（离场）
  - 文字 stagger：0.06s
- **无障碍降级**：`ScrollTrigger.matchMedia` + 全局 CSS `prefers-reduced-motion`

### 4.4 设计系统模块

- **Design Tokens**：4 个 SCSS token 文件
  - [_colors.scss](web/src/shared/styles/tokens/_colors.scss)：11 级灰阶 + 主色 + 语义色
  - [_spacing.scss](web/src/shared/styles/tokens/_spacing.scss)：16 级间距 + 流式 section padding
  - [_typography.scss](web/src/shared/styles/tokens/_typography.scss)：流式字号 clamp() + 4 级字重
  - [_layout.scss](web/src/shared/styles/tokens/_layout.scss)：断点 + z-index + 圆角 + 阴影 + 透视
- **全局注入**：Vite `additionalData` 自动注入 `@use "@/shared/styles/tokens" as *`
- **基础组件**：[BaseButton.vue](web/src/shared/components/BaseButton.vue)（3 变体 × 3 尺寸）

### 4.5 后端内容管理模块

- **CRUD API**：`/api/content` 路由，5 个端点
- **分页**：`findAll` 支持 page/limit 参数，并行查询 count
- **字段投影**：列表查询仅返回必要字段，避免全字段透传
- **数据模型**：title / slug / body / section(枚举7值) / metadata / isActive
- **索引**：`{ section: 1, isActive: 1 }` 复合索引 + `{ slug: 1 }` 唯一索引

---

## 5. 数据流程设计

### 5.1 页面加载流程

```
用户访问 /
  │
  ├── Vite Dev Server 返回 index.html
  │
  ├── 加载 main.ts
  │     ├── 创建 Vue App
  │     ├── 创建 Vue Router（HTML5 History）
  │     ├── 导入 global.scss（CSS Reset + Token 注入）
  │     └── 挂载 #app
  │
  ├── 路由匹配 /
  │     ├── 加载 CinematicLayout（含 AppNav）
  │     └── 懒加载 HomePage
  │
  └── HomePage 组装 7 个场景组件
        ├── HeroScene → useCinematicScene（视差 + 转场）
        ├── FeaturesScene → GSAP 直接动画
        ├── FocusScene → GSAP 直接动画
        ├── NetworkScene → GSAP 直接动画
        ├── IntegrationsScene → GSAP 直接动画
        ├── CompareScene → GSAP 直接动画
        └── CtaScene → GSAP 直接动画
```

### 5.2 滚动动画流程

```
用户滚动页面
  │
  ├── ScrollTrigger 检测 section 进入视口
  │
  ├── 入场阶段（section 顶部到达视口 80% 位置）
  │     ├── bg 层：慢速视差（速度 0.45）
  │     ├── mid 层：中速视差（速度 1.0）
  │     ├── fg 层：快速视差（速度 1.35）
  │     └── content 层：
  │           ├── opacity: 0 → 1
  │           ├── filter: blur(16px) → blur(0px)
  │           ├── scale: 1.02 → 1
  │           └── yPercent: 10 → 0
  │
  ├── 停驻阶段（section 在视口中央）
  │     └── 所有层保持当前状态
  │
  └── 离场阶段（section 底部离开视口 20% 位置）
        └── content 层：
              ├── opacity: 1 → 0
              ├── filter: blur(0px) → blur(14px)
              ├── scale: 1 → 0.985
              └── yPercent: 0 → -8
```

### 5.3 后端数据流程

```
HTTP 请求
  │
  ├── Express 中间件链
  │     ├── helmet()          → 安全头
  │     ├── cors()            → 跨域
  │     ├── compression()     → gzip 压缩
  │     └── json({limit:10kb}) → JSON 解析
  │
  ├── Controller（协议层）
  │     ├── 解析 req.params / req.query / req.body
  │     ├── 调用 Service 方法
  │     └── 返回 res.json() / res.status()
  │
  ├── Service（业务层）
  │     ├── 编排业务流程
  │     ├── 计算分页参数
  │     └── 调用 Repository
  │
  ├── Repository（数据访问层）
  │     ├── 构建 Mongoose 查询
  │     ├── 字段投影 + 排序 + 分页
  │     └── 返回 .lean() 纯对象
  │
  └── Model（数据模型层）
        ├── Schema 校验
        ├── 索引约束
        └── timestamps 自动管理
```

---

## 6. 接口定义规范

### 6.1 RESTful API 规范

| 方法 | 路径 | 说明 | 请求体 | 响应 |
|------|------|------|--------|------|
| `GET` | `/api/content` | 分页查询 | — | `{ items, total, page, limit, totalPages }` |
| `GET` | `/api/content/:id` | 单条查询 | — | Content 对象 |
| `POST` | `/api/content` | 创建 | CreateContentDto | 201 + Content 对象 |
| `PATCH` | `/api/content/:id` | 更新 | UpdateContentDto | Content 对象 |
| `DELETE` | `/api/content/:id` | 删除 | — | 204 |
| `GET` | `/api/health` | 健康检查 | — | `{ status, timestamp }` |

### 6.2 查询参数

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `page` | number | 1 | 页码（从 1 开始） |
| `limit` | number | 10 | 每页条数 |

### 6.3 DTO 定义

**CreateContentDto**：

```typescript
{
  title: string              // 必填，内容标题
  slug: string               // 必填，URL 友好标识，唯一
  body?: string              // 可选，正文内容
  section: 'hero' | 'features' | 'focus' | 'network' | 'integrations' | 'compare' | 'cta'
  metadata?: Record<string, unknown>  // 可选，扩展元数据
  isActive?: boolean         // 可选，默认 true
}
```

**UpdateContentDto**：所有字段可选。

### 6.4 响应格式

**分页响应**：

```json
{
  "items": [...],
  "total": 42,
  "page": 1,
  "limit": 10,
  "totalPages": 5
}
```

**错误响应**：

```json
{
  "error": "Content not found"
}
```

### 6.5 命名规范

| 对象 | 规则 | 示例 |
|------|------|------|
| API 路径 | `kebab-case`，复数名词 | `/api/content` |
| 请求/响应字段 | `camelCase` | `isActive`, `createdAt` |
| DTO 类型 | `PascalCase` + `Dto` 后缀 | `CreateContentDto` |
| Controller 方法 | 箭头函数属性 | `getAll`, `getById` |
| Service 方法 | `camelCase` | `findAll`, `findById` |
| Repository 方法 | `camelCase` | `findAll`, `count`, `create` |

---

## 7. 关键算法实现原理

### 7.1 三层视差算法

视差效果的核心原理是**不同层以不同速度响应滚动进度**，创造空间深度感。

```typescript
// ScrollTrigger scrub 模式：将滚动进度映射为层位移
ScrollTrigger.create({
  trigger: sceneRef,
  start: 'top bottom',     // section 顶部到达视口底部时开始
  end: 'bottom top',       // section 底部离开视口顶部时结束
  scrub: true,             // 动画与滚动进度绑定
  onUpdate: (self) => {
    // self.progress: 0 → 1，表示 section 从进入视口到离开视口的进度
    // 速度系数越大，层移动越快，产生"更近"的视觉效果
    gsap.set(layerRef, {
      yPercent: self.progress * -15 * speedCoefficient
    })
  }
})
```

**速度系数设计**：

| 层级 | 系数 | 效果 | 原理 |
|------|------|------|------|
| bg | 0.45 | 慢速移动 | 模拟远景，视差小 |
| mid | 1.00 | 中速移动 | 主体层，标准速度 |
| fg | 1.35 | 快速移动 | 模拟近景，视差大 |

**透视设置**：父容器 `perspective: 2000px`，为 3D 变换提供深度参考。

### 7.2 Text Masking 算法

文字遮罩入场的核心原理是**将文字从遮罩区域外滑入可视区域**，配合透明度渐变。

```typescript
// 每行文字从下方 110% 位置滑入（完全在遮罩外）
gsap.from(textLines, {
  yPercent: 110,      // 初始位置：向下偏移自身高度的 110%
  opacity: 0,         // 初始透明
  duration: 0.72,     // 入场时长
  stagger: 0.06,      // 每行延迟 60ms
  ease: 'power3.out', // 先快后慢的缓动
})
```

**关键约束**：
- 仅对标题和关键副标题使用，正文段落不使用（避免信息密度过高时阅读体验差）
- 父容器需设置 `overflow: hidden` 作为遮罩裁切区域

### 7.3 转场系统算法

转场遵循**"先去清晰，再进清晰"**的原则，通过 blur + opacity + scale + yPercent 四维联动：

**入场序列**：

```
初始状态                    →    最终状态
opacity: 0                  →    opacity: 1
filter: blur(16px)          →    filter: blur(0px)
scale: 1.02                 →    scale: 1
yPercent: 10                →    yPercent: 0
缓动: power3.out (先快后慢)
时长: 0.72s
```

**离场序列**：

```
当前状态                    →    离场状态
opacity: 1                  →    opacity: 0
filter: blur(0px)           →    filter: blur(14px)
scale: 1                    →    scale: 0.985
yPercent: 0                 →    yPercent: -8
缓动: power2.inOut (先慢后快再慢)
时长: 0.52s
```

**白雾过渡**：场景间通过底部雾层（`linear-gradient(to top, $neutral-950, transparent)`）实现焦点重建，模拟电影中的白场过渡。

### 7.4 SVG 连线绘制算法

网络图的连线通过 `stroke-dashoffset` 动画实现"逐步绘制"效果：

```typescript
// 初始状态：dashoffset 等于线段总长度，线条不可见
gsap.from(svgPaths, {
  strokeDashoffset: 200,   // 从偏移状态开始
  opacity: 0,
  duration: 1,
  stagger: 0.1,            // 每条线延迟 100ms
  ease: 'power2.out',
})
```

节点使用 `back.out(1.7)` 弹性缓动，模拟"弹出"效果：

```typescript
gsap.from(nodeElements, {
  scale: 0,
  opacity: 0,
  duration: 0.6,
  stagger: 0.08,
  ease: 'back.out(1.7)',   // 超出目标后回弹
})
```

### 7.5 分页查询算法

后端分页采用并行查询优化：

```typescript
async findAll(params: PaginationParams) {
  const { page, limit } = params
  const skip = (page - 1) * limit

  // 并行执行数据查询和计数查询，避免串行等待
  const [items, total] = await Promise.all([
    this.repository.findAll(skip, limit),
    this.repository.count(),
  ])

  return {
    items,
    total,
    page,
    limit,
    totalPages: Math.ceil(total / limit),
  }
}
```

---

## 8. UI/UX 设计理念

### 8.1 设计哲学

本项目遵循 **"电影感叙事"** 设计哲学，核心原则：

1. **永远多平面**：背景层、内容层、浮层至少三层，避免单层平移
2. **一镜一主对象**：每段只保留一个主视觉锚点，不堆砌焦点
3. **快入慢停**：进入快、停驻稳、离场干净
4. **导航不参与戏剧化**：导航固定、稳定、低运动，始终作为交互锚点
5. **转场先去清晰再进清晰**：blur + opacity + scale 比单纯 fade 更高级

### 8.2 色彩体系

```
深色主题（主基调）
├── 背景：#0d1117 (neutral-950) — 深空黑
├── 表面：rgba(255,255,255,0.08) — 玻璃态
├── 边框：rgba(255,255,255,0.12) — 微光边框
├── 主色：#166ee1 — 科技蓝
├── 文字主：#212529 → 反转为 #f8f9fa (neutral-0)
├── 文字辅：#868e96 (neutral-600) → #adb5bd (neutral-500)
└── 强调：#7c3aed (accent) / #10b981 (success) / #ef4444 (error)
```

**色彩使用原则**：
- 深色背景为主，营造沉浸感
- 主色（蓝）仅用于关键交互元素和焦点高亮
- 玻璃态（`backdrop-filter: blur`）用于卡片和导航，创造层次感
- 半透明边框（`rgba(255,255,255,0.12)`）替代实线边框

### 8.3 排版体系

**流式排版**：所有字号使用 `clamp()` 实现，无断点跳变：

```scss
$font-size-5xl: clamp(2.75rem, 1.75rem + 5vw, 4.5rem);  // Hero 标题
$font-size-4xl: clamp(2.25rem, 1.6rem + 3.25vw, 3.5rem); // Section 标题
$font-size-base: clamp(0.875rem, 0.825rem + 0.25vw, 1rem); // 正文
```

**层级**：

| 层级 | 字号 | 字重 | 用途 |
|------|------|------|------|
| 5xl | clamp(2.75-4.5rem) | Bold(700) | Hero 标题 |
| 4xl | clamp(2.25-3.5rem) | Bold(700) | Section 标题 |
| lg | clamp(1-1.125rem) | Normal(400) | 副标题 |
| base | clamp(0.875-1rem) | Normal(400) | 正文 |
| sm | clamp(0.8125-0.875rem) | Medium(500) | 辅助文字 |
| xs | clamp(0.75-0.8125rem) | Medium(500) | 标签/徽标 |

### 8.4 响应式策略

**移动优先，内容驱动断点**：

| 视口范围 | 设备 | 策略 |
|----------|------|------|
| 0-479px | 紧凑手机 | 单列布局，CTA 纵向，字号缩小 |
| 480-767px | 大屏手机 | 单列布局，卡片全宽 |
| 768-1023px | 平板 | 双列布局，统计数字纵向 |
| 1024-1439px | 笔记本 | 双列/网格布局，完整导航 |
| 1440px+ | 大桌面 | 最大宽度 1440px 居中，精调间距 |

**核心手段**：
- `clamp()`：流式字号和间距
- `minmax()` + `auto-fit`：自适应网格
- `flex-wrap`：自然换行
- `width: min(100% - padding, max-width)`：容器约束

### 8.5 无障碍设计

| 维度 | 实现 |
|------|------|
| 动画降级 | `prefers-reduced-motion: reduce` → 全局 CSS 时长 0.01ms + GSAP `matchMedia` 跳过动画 |
| 键盘导航 | 所有交互元素可 Tab 聚焦，导航链接可 Enter 激活 |
| 屏幕阅读器 | `.sr-only` 类提供隐藏文本，ARIA 标签（`aria-label`、`aria-expanded`、`role`） |
| 触摸目标 | 移动端按钮/链接最小 44×44px |
| 缩放容忍 | 200% 缩放后内容仍可用 |

---

## 9. 性能优化策略

### 9.1 前端性能

| 策略 | 实现 | 目标 |
|------|------|------|
| **路由懒加载** | `() => import('./HomePage.vue')` | 减少首屏 JS 体积 |
| **手动 Chunk 分离** | `vendor-vue` + `vendor-gsap` | 利用浏览器缓存 |
| **SCSS Token 全局注入** | `additionalData` 避免重复编译 | 减少构建时间 |
| **流式排版** | `clamp()` 替代断点跳变 | 减少 CLS |
| **图片规范** | WebP 格式 + `max-width: 100%` + 延迟加载 | 减少 LCP |
| **CSS 隔离** | `<style scoped>` | 避免样式冲突和冗余 |

**性能预算**：

| 指标 | 目标值 |
|------|--------|
| LCP (Largest Contentful Paint) | < 2.5s |
| INP (Interaction to Next Paint) | < 200ms |
| CLS (Cumulative Layout Shift) | < 0.1 |

### 9.2 动画性能

| 策略 | 实现 |
|------|------|
| **GPU 加速属性** | 仅动画 `transform` + `opacity` + `filter`，避免 layout/paint |
| **will-change 提示** | 视差层隐式触发 GPU 合成 |
| **scrub 模式** | ScrollTrigger `scrub: true` 避免异步动画帧 |
| **.lean() 查询** | 后端返回纯 JS 对象，减少 Mongoose 文档开销 |

### 9.3 后端性能

| 策略 | 实现 |
|------|------|
| **分页查询** | 拒绝无上限列表，默认 limit=10 |
| **字段投影** | 列表查询 `.select()` 仅返回必要字段 |
| **并行查询** | `Promise.all([find, count])` 避免串行等待 |
| **gzip 压缩** | `compression()` 中间件 |
| **Body 限制** | `json({ limit: '10kb' })` 防止大负载攻击 |

### 9.4 数据库性能

| 策略 | 实现 |
|------|------|
| **复合索引** | `{ section: 1, isActive: 1 }` 覆盖高频筛选 |
| **唯一索引** | `{ slug: 1 }` 保证 slug 唯一性 |
| **.lean()** | 跳过 Mongoose 文档包装，返回纯对象 |
| **timestamps** | 自动管理 `createdAt` / `updatedAt`，支持排序 |

---

## 10. 安全设计考量

### 10.1 传输安全

| 措施 | 实现 |
|------|------|
| **HTTP 安全头** | Helmet 自动设置 X-Content-Type-Options、X-Frame-Options、Strict-Transport-Security 等 |
| **CORS** | 限定 `corsOrigin` 为前端开发地址，生产环境需配置实际域名 |
| **Body 大小限制** | JSON 请求体限制 10kb，防止大负载 DoS |
| **Gzip 压缩** | Compression 中间件，减少传输体积 |

### 10.2 数据安全

| 措施 | 实现 |
|------|------|
| **环境变量** | 敏感配置（MONGO_URI、CORS_ORIGIN）通过 `.env` 管理，不硬编码 |
| **字段投影** | 列表查询不返回全字段，减少数据暴露 |
| **Slug 唯一** | 唯一索引防止重复内容 |
| **isActive 软删除** | 查询默认过滤 `isActive: true`，支持软删除 |

### 10.3 前端安全

| 措施 | 实现 |
|------|------|
| **无密钥前端** | 前端不包含任何 API Key、Secret 或数据库连接串 |
| **路由守卫** | Vue Router 可扩展导航守卫 |
| **XSS 防护** | Vue 模板自动转义，不使用 `v-html` |

### 10.4 安全待增强项

| 项目 | 当前状态 | 建议 |
|------|----------|------|
| 运行时输入校验 | 仅 TypeScript 类型 | 引入 Zod/Joi 中间件 |
| 速率限制 | 未实现 | 引入 `express-rate-limit` |
| 认证/授权 | 未实现 | 引入 JWT + 中间件 |
| HTTPS | 依赖部署环境 | 生产环境强制 HTTPS |
| CSP | Helmet 默认 | 根据需要自定义 CSP 策略 |

---

## 11. 未来扩展规划

### 11.1 短期优化（1-2 周）

| 优先级 | 项目 | 说明 |
|--------|------|------|
| P0 | **统一动画系统** | 所有 7 个场景统一使用 `useCinematicScene` composable，消除 GSAP 直接调用 |
| P0 | **运行时输入校验** | 后端引入 Zod schema 验证，替代纯 TypeScript DTO |
| P0 | **全局错误处理** | 后端添加 Express error handler 中间件 |
| P0 | **Mongoose 连接** | app.ts 中添加 `mongoose.connect()` 调用 |
| P1 | **Husky + lint-staged** | Git hook 自动执行 lint + typecheck |
| P1 | **类型去重** | 消除组件内局部类型与 `shared/types` 的重复定义 |
| P1 | **导航数据统一** | AppNav 使用 `NAV_ITEMS` 常量替代硬编码 |

### 11.2 中期增强（1-2 月）

| 项目 | 说明 |
|------|------|
| **SSR/SSG 迁移** | 迁移至 Nuxt 3，解决 SPA SEO 局限；营销页面 SSG，后台 SPA |
| **素材系统** | 实现完整的 bg/mid/fg 三层素材管理，当前仅使用 CSS 渐变模拟 |
| **SplitText 集成** | 引入 GSAP SplitText 插件，实现逐行/逐词文字遮罩 |
| **Lenis 平滑滚动** | 引入 Lenis 替代原生滚动，实现惯性滚动效果 |
| **Content API 对接** | 前端通过 API 获取场景内容，替代硬编码文案 |
| **i18n 国际化** | 引入 vue-i18n，支持中英文切换 |
| **暗色/亮色主题** | 基于现有 Token 体系扩展亮色主题变量 |

### 11.3 长期规划（3-6 月）

| 项目 | 说明 |
|------|------|
| **CMS 后台** | 基于 Content API 构建可视化管理后台 |
| **A/B 测试** | 场景内容/动画参数 A/B 测试框架 |
| **性能监控** | 集成 Sentry / DataDog，追踪 Core Web Vitals 和 API 性能 |
| **CDN 部署** | 静态资源 CDN 分发，图片格式自动转换（WebP/AVIF） |
| **微前端** | 如需扩展为多页面应用，可引入 qiankun / Module Federation |
| **自动化测试** | Playwright E2E 测试 + 视觉回归测试 |

### 11.4 架构演进路线

```
当前（SPA + 纯展示）
  │
  ├── Phase 1: SSR/SSG 迁移（Nuxt 3）
  │     └── 解决 SEO + 首屏性能
  │
  ├── Phase 2: CMS 集成
  │     └── 内容可配置 + 可视化编辑
  │
  ├── Phase 3: 多页面扩展
  │     └── Blog / Docs / Pricing 等页面
  │
  └── Phase 4: 全栈平台化
        └── 用户系统 + 数据分析 + A/B 测试
```

---

## 附录 A：文件索引

### 前端核心文件

| 文件 | 职责 |
|------|------|
| [main.ts](web/src/main.ts) | 应用入口，创建 Vue App + Router |
| [App.vue](web/src/App.vue) | 根组件，仅 `<router-view />` |
| [router/index.ts](web/src/app/router/index.ts) | 路由定义，布局路由模式 |
| [CinematicLayout.vue](web/src/app/layouts/CinematicLayout.vue) | 电影感布局，AppNav + router-view |
| [HomePage.vue](web/src/features/home/pages/HomePage.vue) | 首页，组装 7 个场景 |
| [use-cinematic-scene.ts](web/src/shared/composables/use-cinematic-scene.ts) | 核心动画 composable |
| [use-seo.ts](web/src/shared/composables/use-seo.ts) | SEO meta 管理 |
| [animation.ts](web/src/shared/constants/animation.ts) | GSAP 参数预设 |
| [_colors.scss](web/src/shared/styles/tokens/_colors.scss) | 色彩 Token |
| [_spacing.scss](web/src/shared/styles/tokens/_spacing.scss) | 间距 Token |
| [_typography.scss](web/src/shared/styles/tokens/_typography.scss) | 排版 Token |
| [_layout.scss](web/src/shared/styles/tokens/_layout.scss) | 布局 Token |
| [global.scss](web/src/shared/styles/global.scss) | 全局样式 + CSS Reset |

### 后端核心文件

| 文件 | 职责 |
|------|------|
| [app.ts](server/src/app.ts) | Express 应用入口 |
| [config/index.ts](server/src/config/index.ts) | 环境配置 |
| [content-controller.ts](server/src/modules/content/controllers/content-controller.ts) | HTTP 协议层 |
| [content-service.ts](server/src/modules/content/services/content-service.ts) | 业务逻辑层 |
| [content-repository.ts](server/src/modules/content/repositories/content-repository.ts) | 数据访问层 |
| [content-model.ts](server/src/modules/content/models/content-model.ts) | Mongoose Schema |
| [content-dto.ts](server/src/modules/content/dto/content-dto.ts) | 数据传输对象 |
| [routes/index.ts](server/src/modules/content/routes/index.ts) | 路由定义 |

## 附录 B：Design Token 速查

### 色彩

| Token | 值 | 用途 |
|-------|-----|------|
| `$color-primary` | `#166ee1` | 主色/CTA |
| `$color-neutral-950` | `#0d1117` | 深色背景 |
| `$color-neutral-0` | `#ffffff` | 反色文字 |
| `$color-bg-glass` | `rgba(255,255,255,0.08)` | 玻璃态背景 |
| `$color-border-dark` | `rgba(255,255,255,0.12)` | 深色边框 |

### 间距

| Token | 值 | 用途 |
|-------|-----|------|
| `$section-padding-y` | `clamp(4rem, 5vw, 8rem)` | Section 纵向内边距 |
| `$section-padding-x` | `clamp(1rem, 3vw, 4rem)` | Section 横向内边距 |
| `$container-max-width` | `1440px` | 内容最大宽度 |

### 动画

| Token | 值 | 用途 |
|-------|-----|------|
| `$perspective-scene` | `2000px` | 3D 透视距离 |
| `$blur-enter` | `16px` | 入场模糊量 |
| `$blur-exit` | `14px` | 离场模糊量 |
| `$transition-fast` | `150ms ease` | 快速过渡 |
| `$transition-normal` | `250ms ease` | 标准过渡 |

## 附录 C：验收清单

### 功能验收

- [ ] 导航始终可见可点击
- [ ] 每个分镜至少能看出两层以上不同速度位移
- [ ] 标题入场带遮罩效果，不是整块淡入
- [ ] 主转场存在"白雾过渡 + 焦点重建"
- [ ] 所有 CTA 按钮可点击

### 响应式验收

- [ ] 375×812 下无横向溢出
- [ ] 768×1024 下布局合理
- [ ] 1280×800 下完整展示
- [ ] 1440×900 下精调到位

### 无障碍验收

- [ ] `prefers-reduced-motion` 下关闭强位移和强模糊
- [ ] 键盘可 Tab 到所有交互元素
- [ ] 200% 缩放后内容可用

### 性能验收

- [ ] LCP < 2.5s
- [ ] INP < 200ms
- [ ] CLS < 0.1

### 质量门禁

- [ ] `pnpm lint` 无错误
- [ ] `pnpm typecheck` 通过
- [ ] `pnpm test` 通过
- [ ] `pnpm build` 成功
