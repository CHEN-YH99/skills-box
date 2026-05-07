# 🏗️ 阶段 1 剧本 — 策略与架构

> **持续时间**：5-10 天 | **智能体**：8 | **门守**：Studio Producer + Reality Checker

---

## 目标

定义我们要构建什么、如何构建以及成功是什么样的 — 在编写一行代码之前。每个架构决策都被记录。每个功能都被优先排序。每一分钱都被说明。

## 前置条件

- [ ] 阶段 0 质量门通过（GO 决策）
- [ ] 阶段 0 交接包已接收
- [ ] 利益相关者对项目范围已对齐

## 智能体激活序列

### 步骤 1：战略框架（第 1-3 天，并行）

#### 🎬 Studio Producer — 战略组合对齐
```
激活 Studio Producer 进行 [项目] 的战略组合对齐。

输入：阶段 0 执行摘要 + 市场分析报告
所需交付物：
1. 带项目定位的战略组合计划
2. 愿景、目标和 ROI 目标
3. 资源分配策略
4. 风险/回报评估
5. 成功标准和里程碑定义

与以下对齐：组织战略目标
格式：战略组合计划模板
时间线：3 天
```

#### 🎭 Brand Guardian — 品牌标识系统
```
激活 Brand Guardian 进行 [项目] 的品牌标识开发。

输入：阶段 0 UX 研究（用户画像、旅程图）
所需交付物：
1. 品牌基础（宗旨、愿景、使命、价值观、个性）
2. 视觉标识系统（颜色、排版、间距作为 CSS 变量）
3. 品牌声音和信息架构
4. Logo 系统规格（如果是新品牌）
5. 品牌使用指南

格式：品牌标识系统文档
时间线：3 天
```

#### 💰 Finance Tracker — 预算与资源规划
```
激活 Finance Tracker 进行 [项目] 的财务规划。

输入：Studio Producer 战略计划 + 阶段 0 技术栈评估
所需交付物：
1. 带分类明细的综合项目预算
2. 资源成本预测（智能体、基础设施、工具）
3. ROI 模型及盈亏平衡分析
4. 现金流时间线
5. 带应急储备的财务风险评估

格式：带 ROI 预测的财务计划
时间线：2 天
```

### 步骤 2：技术架构（第 3-7 天，并行，步骤 1 输出可用后）

#### 🏛️ UX Architect — 技术架构 + UX 基础
```
激活 UX Architect 进行 [项目] 的技术架构。

输入：Brand Guardian 视觉标识 + 阶段 0 UX 研究
所需交付物：
1. CSS 设计系统（变量、令牌、比例）
2. 布局框架（Grid/Flexbox 模式、响应式断点）
3. 组件架构（命名约定、层级）
4. 信息架构（页面流程、内容层级）
5. 主题系统（亮色/暗色/系统切换）
6. 无障碍基础（WCAG 2.1 AA 基线）

要创建的文件：
- css/design-system.css
- css/layout.css
- css/components.css
- docs/ux-architecture.md

格式：开发者就绪的基础包
时间线：4 天
```

#### 🏗️ Backend Architect — 系统架构
```
激活 Backend Architect 进行 [项目] 的系统架构。

输入：阶段 0 技术栈评估 + 合规要求
所需交付物：
1. 系统架构规格
   - 架构模式（微服务/单体/无服务器/混合）
   - 通信模式（REST/GraphQL/gRPC/事件驱动）
   - 数据模式（CQRS/事件溯源/CRUD）
2. 带索引策略的数据库 Schema 设计
3. 带版本管理的 API 设计规格
4. 认证和授权架构
5. 安全架构（纵深防御）
6. 可扩展性计划（水平扩展策略）

格式：系统架构规格
时间线：4 天
```

#### 🤖 AI Engineer — ML 架构（如适用）
```
激活 AI Engineer 进行 [项目] 的 ML 系统架构。

输入：Backend Architect 系统架构 + 阶段 0 数据审计
所需交付物：
1. ML 系统设计
   - 模型选择和训练策略
   - 数据管道架构
   - 推理策略（实时/批量/边缘）
2. AI 伦理和安全框架
3. 模型监控和重训练计划
4. 与主应用的集成点
5. ML 基础设施成本预测

条件：仅在项目包含 AI/ML 功能时激活
格式：ML 系统设计文档
时间线：3 天
```

#### 👔 Senior Project Manager — 规格到任务转换
```
激活 Senior Project Manager 进行 [项目] 的任务列表创建。

输入：所有阶段 0 文档 + 架构规格（可用时）
所需交付物：
1. 综合任务列表
   - 引用规格中的确切需求（不添加豪华功能）
   - 每个任务有明确的验收标准
   - 任务间的依赖关系映射
   - 工作量估算（故事点或小时数）
2. 工作分解结构
3. 关键路径识别
4. 实施风险登记册

规则：
- 不要添加规格中没有的功能
- 引用需求中的确切文本
- 对工作量估算保持现实

格式：带验收标准的任务列表
时间线：3 天
```

### 步骤 3：优先级排序（第 7-10 天，顺序执行，步骤 2 完成后）

#### 🎯 Sprint Prioritizer — 功能优先级排序
```
激活 Sprint Prioritizer 进行 [项目] 的待办列表优先级排序。

输入：
- Senior Project Manager → 任务列表
- Backend Architect → 系统架构
- UX Architect → UX 架构
- Finance Tracker → 预算框架
- Studio Producer → 战略计划

所需交付物：
1. RICE 评分的待办列表（覆盖范围、影响力、信心度、工作量）
2. 基于速度估算的冲刺分配
3. 带关键路径的依赖图
4. MoSCoW 分类（必须有/应该有/可以有/不会有）
5. 带里程碑映射的发布计划

验证：Studio Producer 确认战略一致性
格式：优先级排序的冲刺计划
时间线：2 天
```

## 质量门检查清单

| # | 标准 | 证据来源 | 状态 |
|---|------|----------|------|
| 1 | 架构覆盖 100% 的规格需求 | Senior PM 任务列表与架构交叉引用 | ☐ |
| 2 | 品牌系统完整（Logo、颜色、排版、声音） | Brand Guardian 交付物 | ☐ |
| 3 | 所有技术组件都有实施路径 | Backend Architect + UX Architect 规格 | ☐ |
| 4 | 预算已批准且在约束范围内 | Finance Tracker 计划 | ☐ |
| 5 | 冲刺计划基于速度且现实 | Sprint Prioritizer 待办列表 | ☐ |
| 6 | 安全架构已定义 | Backend Architect 安全规格 | ☐ |
| 7 | 合规要求已集成到架构中 | 法律要求映射到技术决策 | ☐ |

## 门决策

**需要双重签署**：Studio Producer（战略）+ Reality Checker（技术）

- **批准**：携带完整架构包进入阶段 2
- **修订**：特定项目需要返工（返回相关步骤）
- **重组**：根本性架构问题（重启阶段 1）

## 交接至阶段 2

```markdown
## 阶段 1 → 阶段 2 交接包

### 架构包：
1. 战略组合计划（Studio Producer）
2. 品牌标识系统（Brand Guardian）
3. 财务计划（Finance Tracker）
4. CSS 设计系统 + UX 架构（UX Architect）
5. 系统架构规格（Backend Architect）
6. ML 系统设计（AI Engineer — 如适用）
7. 综合任务列表（Senior Project Manager）
8. 优先级排序的冲刺计划（Sprint Prioritizer）

### 给 DevOps Automator：
- 来自 Backend Architect 的部署架构
- 来自系统架构的环境需求
- 来自基础设施需求的监控需求

### 给 Frontend Developer：
- 来自 UX Architect 的 CSS 设计系统
- 来自 Brand Guardian 的品牌标识
- 来自 UX Architect 的组件架构
- 来自 Backend Architect 的 API 规格

### 给 Backend Architect（继续）：
- 数据库 Schema 准备部署
- API 脚手架准备实施
- 认证系统架构已定义
```

---

*阶段 1 在 Studio Producer 和 Reality Checker 都签署批准架构包时完成。*
