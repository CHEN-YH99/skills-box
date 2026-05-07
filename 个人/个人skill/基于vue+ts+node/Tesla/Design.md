# Tesla-Inspired Cinematic Landing Page — Skill Design

从 Tesla 品牌落地页项目中蒸馏出的可复用 skill 设计。本 skill 不是对现有三个 skill 的重复，而是将它们在真实项目中的**融合实践、踩坑修正、架构决策**固化为可复用的工程蓝图。

---

## 1. 什么时候必须用这个 Skill

满足以下任一条件时使用：

- 需求是**品牌落地页、产品发布页、营销首页**，且要求电影级滚动叙事
- 需要同时调用 `enterprise-vue-ts-scss-node-mongodb` + `synex-cinematic-scroll` + `responsive-enterprise` 三个 skill
- 用户明确提到 `tesla-cinematic-landing`
- 需求是暗色系高端官网，包含分镜切换、视差、玻璃卡片、对比表、CTA 收束

如果只是普通后台管理页面或简单展示页，不需要本 skill。

---

## 2. 技术栈锁定

| 层级 | 选型 | 版本约束 |
|------|------|----------|
| 框架 | Vue 3 + `<script setup>` | ^3.5 |
| 语言 | TypeScript (strict: true) | ~5.7 |
| 构建 | Vite | ^6.3 |
| 样式 | SCSS + BEM | sass ^1.86 |
| 动画 | GSAP + ScrollTrigger | gsap ^3.12 |
| 包管理 | **pnpm**（强制） | 禁止 npm/yarn |
| Lint | ESLint + @typescript-eslint | - |
| 类型检查 | vue-tsc --noEmit | - |

---

## 3. 项目结构

```text
src/
  main.ts
  App.vue
  features/
    <feature-name>/
      components/
        HeroSection.vue
        FeaturesSection.vue
        PerformanceSection.vue
        SpecsSection.vue
        CtaSection.vue
      composables/
        use-cinematic-scroll.ts
  shared/
    components/
      TheNavigation.vue
    styles/
      _tokens.scss
      _mixins.scss
      _reset.scss
      _buttons.scss
      _entry.scss
      index.scss
```

### 依赖方向（强制）

```
App.vue -> features/* -> shared/*
features/home -> features/home/composables
features/a 禁止直接引用 features/b
shared 禁止反向依赖 features
```

---

## 4. SCSS 架构

### 4.1 文件职责

| 文件 | 职责 |
|------|------|
| `_tokens.scss` | 所有设计令牌：颜色、字号、间距、断点、z-index、动画参数 |
| `_mixins.scss` | 可复用 mixin：respond-to、glass-card、scene-container、scene-layer、text-mask、focus-visible、reduced-motion |
| `_reset.scss` | 全局重置 + 基础排版 |
| `_buttons.scss` | .btn 组件样式（--primary / --outline 变体） |
| `_entry.scss` | `@forward 'tokens'` + `@forward 'mixins'`，供 Vite additionalData 注入 |
| `index.scss` | `@forward` 全部模块，在 main.ts 中引入 |

### 4.2 Vite 配置

```ts
css: {
  preprocessorOptions: {
    scss: {
      additionalData: `@use "@/shared/styles/entry" as *;\n`,
    },
  },
},
```

这样所有组件 SCSS 无需手动 `@use` tokens 和 mixins。

### 4.3 设计令牌体系

**颜色**：暗色系三阶背景 + 三阶文字 + 强调色 + 玻璃态 + 雾层

```scss
$color-bg-primary: #000000;
$color-bg-secondary: #0a0a0a;
$color-bg-tertiary: #1a1a1a;
$color-text-primary: #ffffff;
$color-text-secondary: rgba(255, 255, 255, 0.7);
$color-text-tertiary: rgba(255, 255, 255, 0.4);
$color-accent: #e82127;
$color-glass: rgba(255, 255, 255, 0.05);
$color-glass-border: rgba(255, 255, 255, 0.1);
$color-fog: rgba(255, 255, 255, 0.95);
```

**字号**：全部使用 `clamp()` 实现流式缩放

```scss
$font-size-hero: clamp(3rem, 8vw, 7rem);
$font-size-h1: clamp(2.5rem, 6vw, 5rem);
$font-size-h2: clamp(2rem, 4vw, 3.5rem);
$font-size-h3: clamp(1.5rem, 3vw, 2rem);
$font-size-body: clamp(1rem, 1.5vw, 1.125rem);
```

**间距**：同样 `clamp()` 流式

```scss
$spacing-xs: clamp(0.25rem, 0.5vw, 0.5rem);
$spacing-sm: clamp(0.5rem, 1vw, 1rem);
$spacing-md: clamp(1rem, 2vw, 2rem);
$spacing-lg: clamp(2rem, 4vw, 4rem);
$spacing-xl: clamp(3rem, 6vw, 6rem);
$spacing-2xl: clamp(4rem, 8vw, 10rem);
```

**z-index 层级**：严格分层，禁止随意赋值

```scss
$z-index-nav: 1000;
$z-index-fog: 900;
$z-index-content: 15;
$z-index-fg: 10;
$z-index-mid: 5;
$z-index-bg: 1;
```

**视差速度系数**：

```scss
$parallax-bg: 0.45;
$parallax-mid: 1.00;
$parallax-fg: 1.35;
$perspective-scene: 2000px;
```

### 4.4 核心 Mixin

```scss
@mixin scene-container {
  position: relative;
  width: 100%;
  min-height: 100vh;
  overflow: hidden;
  perspective: $perspective-scene;
}

@mixin scene-layer {
  position: absolute;
  inset: 0;
  will-change: transform, opacity;
}

@mixin glass-card {
  background: $color-glass;
  border: 1px solid $color-glass-border;
  border-radius: $border-radius-lg;
  backdrop-filter: blur(20px);
  -webkit-backdrop-filter: blur(20px);
}

@mixin text-mask {
  overflow: hidden;
  display: inline-block;
  span { display: inline-block; }
}

@mixin reduced-motion {
  @media (prefers-reduced-motion: reduce) { @content; }
}

@mixin respond-to($breakpoint) {
  @if $breakpoint == sm { @media (min-width: $breakpoint-sm) { @content; } }
  @else if $breakpoint == md { @media (min-width: $breakpoint-md) { @content; } }
  @else if $breakpoint == lg { @media (min-width: $breakpoint-lg) { @content; } }
  @else if $breakpoint == xl { @media (min-width: $breakpoint-xl) { @content; } }
}
```

---

## 5. 分镜架构

### 5.1 场景容器结构（每个 section 必须遵守）

```html
<section id="xxx" ref="sectionRef" class="scene scene-xxx">
  <div class="scene-xxx__bg scene__layer" />
  <div class="scene-xxx__mid scene__layer">...</div>
  <div class="scene-xxx__fg scene__layer">...</div>
  <div class="scene-xxx__content scene__content">...</div>
</section>
```

### 5.2 分镜节奏（6 段式）

| 序号 | 名称 | 叙事目标 | 关键元素 |
|------|------|----------|----------|
| 1 | Hero | 建立世界观 | 全屏标题 + Text Masking + CTA + 滚动指示器 |
| 2 | Features | 解释能力 | 三列玻璃卡片 + 数据指标 |
| 3 | Performance | 核心卖点 | 焦点仪表盘 + 规格参数网格 |
| 4 | Specs | 价值对比 | 双产品对比表 + 高亮行 |
| 5 | CTA | 收束转化 | 行动号召 + 底部链接 |
| — | Navigation | 全局锚点 | 固定顶部 + 滚动高亮 + 移动端汉堡菜单 |

### 5.3 每个场景组件的内部结构

```vue
<script setup lang="ts">
import { ref, onMounted } from 'vue'
import { gsap } from 'gsap'
import { ScrollTrigger } from 'gsap/ScrollTrigger'

gsap.registerPlugin(ScrollTrigger)

interface DataType {
  // ...
}

const DATA_ITEMS: DataType[] = [/* ... */]

const sectionRef = ref<HTMLElement | null>(null)
const headingLine = ref<HTMLElement | null>(null)

onMounted(() => {
  const ctx = gsap.context(() => {
    gsap.from(headingLine.value!, {
      yPercent: 110,
      opacity: 0,
      duration: 0.72,
      ease: 'power3.out',
      scrollTrigger: {
        trigger: sectionRef.value!,
        start: 'top 80%',
        toggleActions: 'play none none reverse',
      },
    })
  }, sectionRef.value!)

  return () => ctx.revert()
})
</script>
```

---

## 6. 动画系统

### 6.1 双层动画架构

| 层级 | 实现位置 | 职责 |
|------|----------|------|
| 全局视差 + 场景转场 | `use-cinematic-scroll.ts` composable | 在 App.vue 中调用一次，管理所有 scene 的滚动驱动动画 |
| 局部入场动画 | 各 Section 组件内部 | 每个组件管理自己的标题、卡片、列表入场 |

### 6.2 全局 Composable：use-cinematic-scroll

三个核心函数：

1. **initSceneParallax**：为每个 scene 的 bg/mid/fg 层绑定不同速度的 scrub 视差
2. **initSceneTransitions**：为每个 scene 的 content 绑定入场/离场 blur + opacity + scale + yPercent
3. **initFogTransitions**：在非首屏 scene 顶部动态插入雾层 overlay，scrub 控制透明度

GSAP 参数预设：

```ts
const EASE_ENTER = 'power3.out'
const EASE_EXIT = 'power2.inOut'
const D_ENTER = 0.72
const D_EXIT = 0.52
```

转场语言（硬规范）：

| 方向 | opacity | blur | scale | yPercent |
|------|---------|------|-------|----------|
| 入场 | 0 → 1 | 16px → 0px | 1.02 → 1 | 10 → 0 |
| 离场 | 1 → 0 | 0px → 14px | 1 → 0.985 | 0 → -8 |

### 6.3 局部入场动画模式

**标题 Text Masking**（所有 section 统一）：

```ts
gsap.from(headingLine.value!, {
  yPercent: 110,
  opacity: 0,
  duration: 0.72,
  ease: 'power3.out',
  scrollTrigger: {
    trigger: sectionRef.value!,
    start: 'top 80%',
    toggleActions: 'play none none reverse',
  },
})
```

**卡片/列表入场**（按场景变体）：

```ts
cards.value.forEach((card, i) => {
  gsap.from(card, {
    opacity: 0,
    y: 60,
    filter: 'blur(10px)',
    duration: 0.72,
    delay: i * 0.15,
    ease: 'power3.out',
    scrollTrigger: { /* ... */ },
  })
})
```

### 6.4 无障碍降级

```ts
const isReducedMotionPreferred = typeof window !== 'undefined'
  ? window.matchMedia('(prefers-reduced-motion: reduce)').matches
  : false

onMounted(() => {
  if (isReducedMotionPreferred) return
  // ... 所有动画逻辑
})
```

SCSS 侧同步降级：

```scss
@include reduced-motion {
  &__scroll-line { animation: none; }
}
```

---

## 7. 组件设计模式

### 7.1 数据声明模式

所有静态数据提取为 `UPPER_SNAKE_CASE` 常量 + 显式 interface：

```ts
interface Feature {
  icon: string
  title: string
  description: string
  stat: string
  statLabel: string
}

const FEATURES: Feature[] = [/* ... */]
```

### 7.2 事件处理模式

```ts
const handleScrollToSection = (id: string) => {
  const el = document.getElementById(id)
  el?.scrollIntoView({ behavior: 'smooth' })
}
```

### 7.3 导航组件模式

- 固定定位 + 滚动时背景模糊
- IntersectionObserver 或 scroll 事件追踪 active section
- 移动端汉堡菜单 + Vue Transition 动画
- 导航项配置化：`NavItem[]` 常量驱动

### 7.4 对比表模式

- CSS Grid 三列布局
- 高亮行通过 `isHighlight` 布尔属性 + BEM modifier class
- 横向滚动兜底（`overflow-x: auto`）

---

## 8. 命名规范（从 naming-style-guide.md 蒸馏 + 项目实践修正）

| 对象 | 规则 | 示例 | 踩坑修正 |
|------|------|------|----------|
| Vue 组件文件 | PascalCase.vue | HeroSection.vue | — |
| TS 普通文件 | kebab-case.ts | use-cinematic-scroll.ts | — |
| 目录名 | kebab-case | features/home | — |
| 变量/函数 | camelCase | handleScrollTo | — |
| 布尔变量 | is/has/can/should 前缀 | isScrolled | 曾用 prefersReducedMotion，修正为 isReducedMotionPreferred |
| 常量 | UPPER_SNAKE_CASE | SPEC_ITEMS | — |
| 类型/接口 | PascalCase | SpecRow | — |
| 事件处理 | handleXxx | handleMobileMenuToggle | 曾用 toggleMobileMenu，修正为 handle 前缀 |
| CSS BEM | block__element--modifier | scene-hero__title-mask | — |
| CSS keyframes | kebab-case | scroll-pulse | 曾用 scrollPulse，修正为 kebab-case |
| section id | kebab-case | #hero, #features | — |

---

## 9. 包管理器强制策略

### 9.1 项目规则文件

`.trae/rules/project_rules.md` 中声明：

- 所有命令必须使用 pnpm
- 禁止 npm install / npm run / yarn
- 初始化项目使用 pnpm create

### 9.2 preinstall 硬拦截

```json
{
  "scripts": {
    "preinstall": "node -e \"if(process.env.npm_config_user_agent && !process.env.npm_config_user_agent.includes('pnpm')){console.error('Use pnpm instead of npm/yarn.');process.exit(1)}\""
  }
}
```

### 9.3 .npmrc

```ini
strict-peer-dependencies=true
shamefully-hoist=true
```

---

## 10. 实施步骤

每次使用本 skill 构建新项目时，按此顺序执行：

1. **读取 skills 规范**：先读完所有相关 skill 的 references 文件，再决定工具链
2. **初始化项目**：`pnpm create vite@latest . -- --template vue-ts`
3. **安装依赖**：`pnpm add gsap` + `pnpm add -D sass`
4. **搭建 SCSS 架构**：tokens → mixins → reset → buttons → entry → index
5. **配置 Vite**：路径别名 + SCSS additionalData
6. **搭建目录结构**：features/home/components + composables + shared/components + shared/styles
7. **实现导航**：固定导航 + 滚动追踪 + 移动端菜单
8. **逐个实现场景**：Hero → Features → Performance → Specs → CTA
9. **接入全局动画**：use-cinematic-scroll composable
10. **响应式验证**：375 / 768 / 1024 / 1440 四档
11. **无障碍验证**：prefers-reduced-motion + focus-visible + aria
12. **质量门禁**：pnpm lint → pnpm typecheck → pnpm build

---

## 11. 验收清单

全部满足才算完成：

- [ ] pnpm 作为唯一包管理器，npm/yarn 被拦截
- [ ] 所有文件/函数/变量命名符合 naming-style-guide.md
- [ ] SCSS 全部使用 tokens，无硬编码颜色/间距
- [ ] 每个 section 至少两层视差位移
- [ ] 标题入场带 Text Masking，非整块淡入
- [ ] 场景转场使用 blur + opacity + scale + yPercent
- [ ] 导航固定可见，滚动高亮正确
- [ ] 375 / 768 / 1024 / 1440 无横向溢出
- [ ] prefers-reduced-motion 下关闭强位移和强模糊
- [ ] pnpm typecheck 通过
- [ ] pnpm build 通过
- [ ] 所有组件数据提取为常量 + interface，无内联魔法值

---

## 12. 常见失败模式

| 失败 | 原因 | 修正 |
|------|------|------|
| 用 npm 初始化项目 | 未先读 skill 规范 | 先读 quality-gates.md，确认 pnpm |
| 布尔变量无 is/has 前缀 | 习惯性命名 | prefersReducedMotion → isReducedMotionPreferred |
| 事件处理无 handle 前缀 | toggleXxx / scrollToXxx | toggleMobileMenu → handleMobileMenuToggle |
| CSS keyframes 用 camelCase | scrollPulse | scroll-pulse（kebab-case） |
| SCSS mixin undefined | 组件内未引入 mixins | 通过 _entry.scss + Vite additionalData 全局注入 |
| v-for 使用未使用的 index 变量 | `v-for="(item, index) in list"` | 移除未使用的 index：`v-for="item in list"` |
| 模板中直接使用 document | `@click="document.getElementById('x')"` | 提取为 handleXxx 函数 |
| 内联数据散落在 template 中 | 直接在 template 写死值 | 提取为常量 + interface |
