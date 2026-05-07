# 🔄 阶段 6 剧本 — 运营与演进

> **持续时间**：持续 | **智能体**：12+（轮换） | **治理**：Studio Producer

---

## 目标

持续运营与持续改进。产品已上线 — 现在让它蓬勃发展。这个阶段没有结束日期；只要产品在市场上，它就会运行。

## 前置条件

- [ ] 阶段 5 质量门通过（稳定发布）
- [ ] 阶段 5 交接包已接收
- [ ] 运营节奏已建立
- [ ] 基线指标已记录

## 运营节奏

### 持续（始终活跃）

| 智能体 | 责任 | SLA |
|-------|------|-----|
| **Infrastructure Maintainer** | 系统正常运行时间、性能、安全 | 99.9% 正常运行时间，< 30 分钟 MTTR |
| **Support Responder** | 客户支持、问题解决 | < 4 小时首次响应 |
| **DevOps Automator** | 部署管道、热修复 | 每天多次部署能力 |

### 每日

| 智能体 | 活动 | 输出 |
|-------|------|------|
| **Analytics Reporter** | KPI 仪表板更新 | 每日指标快照 |
| **Support Responder** | 问题分类和解决 | 支持工单摘要 |
| **Infrastructure Maintainer** | 系统健康检查 | 健康状态报告 |

### 每周

| 智能体 | 活动 | 输出 |
|-------|------|------|
| **Analytics Reporter** | 每周性能分析 | 每周分析报告 |
| **Feedback Synthesizer** | 用户反馈综合 | 每周反馈摘要 |
| **Sprint Prioritizer** | 待办梳理 + 冲刺规划 | 冲刺计划 |
| **Growth Hacker** | 增长渠道优化 | 增长指标报告 |
| **Project Shepherd** | 跨团队协调 | 每周状态更新 |

### 双周

| 智能体 | 活动 | 输出 |
|-------|------|------|
| **Feedback Synthesizer** | 深度反馈分析 | 双周洞察报告 |
| **Experiment Tracker** | A/B 测试分析 | 实验结果摘要 |
| **Content Creator** | 内容日历执行 | 已发布内容报告 |

### 每月

| 智能体 | 活动 | 输出 |
|-------|------|------|
| **Executive Summary Generator** | C 层级报告 | 月度执行摘要 |
| **Finance Tracker** | 财务表现审查 | 月度财务报告 |
| **Legal Compliance Checker** | 监管监控 | 合规状态报告 |
| **Trend Researcher** | 市场情报更新 | 月度市场简报 |
| **Brand Guardian** | 品牌一致性审计 | 品牌健康报告 |

### 每季度

| 智能体 | 活动 | 输出 |
|-------|------|------|
| **Studio Producer** | 战略组合审查 | 季度战略审查 |
| **Workflow Optimizer** | 流程效率审计 | 优化报告 |
| **Performance Benchmarker** | 性能回归测试 | 季度性能报告 |
| **Tool Evaluator** | 技术栈审查 | 技术债评估 |

## 持续改进循环

```
测量（Analytics Reporter）
    │
    ▼
分析（Feedback Synthesizer + Analytics Reporter）
    │
    ▼
规划（Sprint Prioritizer + Studio Producer）
    │
    ▼
构建（阶段 3 Dev↔QA 循环 — 迷你周期）
    │
    ▼
验证（Evidence Collector + Reality Checker）
    │
    ▼
部署（DevOps Automator）
    │
    ▼
测量（回到起点）
```

### 阶段 6 中的功能开发

新功能遵循压缩的 NEXUS 周期：

```
1. Sprint Prioritizer 从待办列表选择功能
2. 适当的开发者智能体实现
3. Evidence Collector 验证（Dev↔QA 循环）
4. DevOps Automator 部署（功能标志或直接）
5. Experiment Tracker 监控（如适用 A/B 测试）
6. Analytics Reporter 测量影响
7. Feedback Synthesizer 收集用户反馈
```

## 事件响应协议

### 严重程度级别

| 级别 | 定义 | 响应时间 | 决策权威 |
|------|------|----------|----------|
| **P0 — 关键** | 服务中断、数据丢失、安全漏洞 | 立即 | Studio Producer |
| **P1 — 高** | 主要功能损坏、严重性能下降 | < 1 小时 | Project Shepherd |
| **P2 — 中** | 次要功能问题、有解决方法 | < 4 小时 | Agents Orchestrator |
| **P3 — 低** | 外观问题、轻微不便 | 下一个冲刺 | Sprint Prioritizer |

### 事件响应序列

```
检测（Infrastructure Maintainer 或 Support Responder）
    │
    ▼
分类（Agents Orchestrator）
    ├── 分类严重程度（P0-P3）
    ├── 分配响应团队
    └── 通知利益相关者
    │
    ▼
响应
    ├── P0：Infrastructure Maintainer + DevOps Automator + Backend Architect
    ├── P1：相关开发者智能体 + DevOps Automator
    ├── P2：相关开发者智能体
    └── P3：加入冲刺待办列表
    │
    ▼
解决
    ├── 修复已实施并部署
    ├── Evidence Collector 验证修复
    └── Infrastructure Maintainer 确认稳定性
    │
    ▼
事后分析
    ├── Workflow Optimizer 主持回顾
    ├── 根因分析已文档化
    ├── 预防措施已识别
    └── 流程改进已实施
```

## 增长运营

### 月度增长审查（Growth Hacker 主导）

```
1. 渠道表现分析
   - 按渠道的获客（自然、付费、推荐、社交）
   - 按渠道的 CAC
   - 按漏斗阶段的转化率
   - LTV:CAC 比率趋势

2. 实验结果
   - 已完成的 A/B 测试和结果
   - 统计显著性验证
   - 获胜方案实施状态
   - 新实验管道

3. 留存分析
   - 队列留存曲线
   - 流失风险识别
   - 重新参与活动结果
   - 功能采用指标

4. 增长路线图更新
   - 下月的增长实验
   - 渠道预算重新分配
   - 新渠道探索
   - 病毒系数优化
```

### 内容运营（Content Creator + Social Media Strategist）

```
每周：
- 内容日历执行
- 社交媒体参与
- 社区管理
- 表现跟踪

每月：
- 内容表现审查
- 编辑日历规划
- 平台算法更新
- 内容策略优化

平台特定：
- Twitter Engager → 每日参与，每周推文串
- Instagram Curator → 每周 3-5 篇帖子，每日故事
- TikTok Strategist → 每周 3-5 个视频
- Reddit Community Builder → 每日真实参与
```

## 财务运营

### 月度财务审查（Finance Tracker）

```
1. 收入分析
   - MRR/ARR 跟踪
   - 按细分/计划的收入
   - 扩展收入
   - 流失收入影响

2. 成本分析
   - 基础设施成本
   - 按渠道的营销支出
   - 团队/资源成本
   - 工具和服务成本

3. 单位经济
   - CAC 趋势
   - LTV 趋势
   - LTV:CAC 比率
   - 回收期

4. 预测
   - 收入预测（3 个月滚动）
   - 成本预测
   - 现金流预测
   - 预算差异分析
```

## 合规运营

### 月度合规检查（Legal Compliance Checker）

```
1. 监管监控
   - 影响产品的新法规
   - 现有法规变更
   - 行业执法行动
   - 合规截止日期跟踪

2. 隐私合规
   - 数据主体请求处理
   - 同意管理有效性
   - 数据保留政策遵守
   - 跨境传输合规

3. 安全合规
   - 漏洞扫描结果
   - 补丁管理状态
   - 访问控制审查
   - 事件日志审查

4. 审计就绪
   - 文档时效性
   - 证据收集状态
   - 培训完成率
   - 政策确认跟踪
```

## 战略演进

### 季度战略审查（Studio Producer）

```
1. 市场定位评估
   - 竞争格局变化（Trend Researcher 输入）
   - 市场份额演变
   - 品牌感知（Brand Guardian 输入）
   - 客户满意度趋势（Feedback Synthesizer 输入）

2. 产品战略
   - 功能路线图审查
   - 技术债评估（Tool Evaluator 输入）
   - 平台扩展机会
   - 合作伙伴评估

3. 增长战略
   - 渠道有效性审查
   - 新市场机会
   - 定价策略评估
   - 扩展规划

4. 组织健康
   - 流程效率（Workflow Optimizer 输入）
   - 团队绩效指标
   - 资源分配优化
   - 能力发展需求

输出：季度战略审查 → 更新路线图和优先级
```

## 阶段 6 成功指标

| 类别 | 指标 | 目标 | 负责人 |
|------|------|------|--------|
| **可靠性** | 系统正常运行时间 | > 99.9% | Infrastructure Maintainer |
| **可靠性** | MTTR | < 30 分钟 | Infrastructure Maintainer |
| **增长** | 月度用户增长 | > 20% | Growth Hacker |
| **增长** | 激活率 | > 60% | Analytics Reporter |
| **留存** | 第 7 天留存 | > 40% | Analytics Reporter |
| **留存** | 第 30 天留存 | > 20% | Analytics Reporter |
| **财务** | LTV:CAC 比率 | > 3:1 | Finance Tracker |
| **财务** | 组合 ROI | > 25% | Studio Producer |
| **质量** | NPS 评分 | > 50 | Feedback Synthesizer |
| **质量** | 支持解决时间 | < 4 小时 | Support Responder |
| **合规** | 监管遵守率 | > 98% | Legal Compliance Checker |
| **效率** | 部署频率 | 多次/天 | DevOps Automator |
| **效率** | 流程改进 | 20%/季度 | Workflow Optimizer |

---

*阶段 6 没有结束日期。只要产品在市场上就持续运行，持续改进循环推动产品前进。NEXUS 管道可以重新激活（NEXUS-Sprint 或 NEXUS-Micro）用于重大新功能或转型。*
