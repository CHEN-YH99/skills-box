# 🔍 阶段 0 剧本 — 情报与发现

> **持续时间**：3-7 天 | **智能体**：6 | **门守**：Executive Summary Generator

---

## 目标

在投入资源前验证机会。在理解问题、市场和监管环境之前不进行构建。

## 前置条件

- [ ] 项目简报或初始概念已存在
- [ ] 已确定利益相关者发起人
- [ ] 发现阶段预算已批准

## 智能体激活序列

### 第一波：并行启动（第 1 天）

#### 🔍 Trend Researcher — 市场情报负责人
```
激活 Trend Researcher 进行 [项目领域] 的市场情报。

所需交付物：
1. 竞争格局分析（直接 + 间接竞争对手）
2. 市场规模：TAM、SAM、SOM 及方法论
3. 趋势生命周期映射：这个市场在采用曲线中的位置？
4. 3-6 个月趋势预测及置信区间
5. 该领域的投资和融资趋势

来源：至少 15 个独特的、已验证的来源
格式：带执行摘要的战略报告
时间线：3 天
```

#### 💬 Feedback Synthesizer — 用户需求分析
```
激活 Feedback Synthesizer 进行 [项目领域] 的用户需求分析。

所需交付物：
1. 多渠道反馈收集计划（调查、访谈、评论、社交）
2. 现有用户接触点的情感分析
3. 痛点识别和优先级排序（RICE 评分）
4. 功能请求分析与业务价值估计
5. 来自反馈模式的流失风险指标

格式：带优先级矩阵的综合反馈报告
时间线：3 天
```

#### 🔍 UX Researcher — 用户行为分析
```
激活 UX Researcher 进行 [项目领域] 的用户行为分析。

所需交付物：
1. 用户访谈计划（5-10 个目标用户）
2. 用户画像开发（3-5 个主要画像）
3. 主要用户流程的旅程映射
4. 竞争产品的可用性启发式评估
5. 带统计验证的行为洞察

格式：带用户画像和旅程图的研究发现报告
时间线：5 天
```

### 第二波：并行启动（第 1 天，与第一波独立）

#### 📊 Analytics Reporter — 数据环境评估
```
激活 Analytics Reporter 进行 [项目领域] 的数据环境评估。

所需交付物：
1. 现有数据源审计（有哪些数据可用？）
2. 信号识别（我们可以测量什么？）
3. 基线指标建立
4. 带完整性评分的数据质量评估
5. 分析基础设施建议

格式：带信号图的数据审计报告
时间线：2 天
```

#### ⚖️ Legal Compliance Checker — 监管扫描
```
激活 Legal Compliance Checker 进行 [项目领域] 的监管扫描。

所需交付物：
1. 适用的监管框架（GDPR、CCPA、HIPAA 等）
2. 数据处理要求和约束
3. 目标市场的管辖区映射
4. 带严重性评级的合规风险评估
5. 阻塞性 vs 可管理的合规问题

格式：合规要求矩阵
时间线：3 天
```

#### 🛠️ Tool Evaluator — 技术环境
```
激活 Tool Evaluator 进行 [项目领域] 的技术环境评估。

所需交付物：
1. 问题领域的技术栈评估
2. 关键组件的构建 vs 购买分析
3. 与现有系统的集成可行性
4. 开源 vs 商业评估
5. 技术风险评估

格式：带推荐矩阵的技术栈评估
时间线：2 天
```

## 汇聚点（第 5-7 天）

所有六个智能体交付报告。Executive Summary Generator 综合如下：

```
激活 Executive Summary Generator 综合阶段 0 发现。

输入文档：
1. Trend Researcher → 市场分析报告
2. Feedback Synthesizer → 综合反馈报告
3. UX Researcher → 研究发现报告
4. Analytics Reporter → 数据审计报告
5. Legal Compliance Checker → 合规要求矩阵
6. Tool Evaluator → 技术栈评估

输出：执行摘要（≤500 字，SCQA 格式）
所需决策：GO / NO-GO / PIVOT
包含：量化的市场机会、已验证的用户需求、监管路径、技术可行性
```

## 质量门检查清单

| # | 标准 | 证据来源 | 状态 |
|---|------|----------|------|
| 1 | 市场机会已验证，TAM > 最低可行阈值 | Trend Researcher 报告 | ☐ |
| 2 | ≥3 个已验证的用户痛点及支持数据 | Feedback Synthesizer + UX Researcher | ☐ |
| 3 | 未发现阻塞性合规问题 | Legal Compliance Checker 矩阵 | ☐ |
| 4 | 关键指标和数据源已识别 | Analytics Reporter 审计 | ☐ |
| 5 | 技术栈可行且已评估 | Tool Evaluator 评估 | ☐ |
| 6 | 执行摘要已交付，含 GO/NO-GO 建议 | Executive Summary Generator | ☐ |

## 门决策

- **GO**：进入阶段 1 — 策略与架构
- **NO-GO**：归档发现结果，记录经验教训，重新分配资源
- **PIVOT**：根据发现修改范围/方向，重新运行针对性发现

## 交接至阶段 1

```markdown
## 阶段 0 → 阶段 1 交接包

### 需携带的文档：
1. 市场分析报告（Trend Researcher）
2. 综合反馈报告（Feedback Synthesizer）
3. 用户画像和旅程图（UX Researcher）
4. 数据审计报告（Analytics Reporter）
5. 合规要求矩阵（Legal Compliance Checker）
6. 技术栈评估（Tool Evaluator）
7. 带 GO 决策的执行摘要（Executive Summary Generator）

### 已识别的关键约束：
- [来自 Legal Compliance Checker 的监管约束]
- [来自 Tool Evaluator 的技术约束]
- [来自 Trend Researcher 的市场时机约束]

### 优先用户需求（供 Sprint Prioritizer 使用）：
1. [痛点 1 — 来自 Feedback Synthesizer]
2. [痛点 2 — 来自 UX Researcher]
3. [痛点 3 — 来自 Feedback Synthesizer]
```

---

*阶段 0 在 Executive Summary Generator 交付 GO 决策并附有所有六个发现智能体的支持证据时完成。*
