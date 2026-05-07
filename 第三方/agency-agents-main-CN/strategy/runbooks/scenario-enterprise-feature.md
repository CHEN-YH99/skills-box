# 🏢 运行手册：企业功能开发

> **模式**：NEXUS-Sprint | **持续时间**：6-12 周 | **智能体**：20-30

---

## 场景

你正在为现有的企业产品添加一个重要功能。合规性、安全性和质量门是不可妥协的。多个利益相关者需要对齐。该功能必须与现有系统无缝集成。

## 智能体阵容

### 核心团队
| 智能体 | 角色 |
|-------|------|
| Agents Orchestrator | 管道控制器 |
| Project Shepherd | 跨职能协调 |
| Senior Project Manager | 规格到任务转换 |
| Sprint Prioritizer | 待办列表管理 |
| UX Architect | 技术基础 |
| UX Researcher | 用户验证 |
| UI Designer | 组件设计 |
| Frontend Developer | UI 实现 |
| Backend Architect | API 和系统集成 |
| Senior Developer | 复杂功能实现 |
| DevOps Automator | CI/CD 和部署 |
| Evidence Collector | 视觉 QA |
| API Tester | 端点验证 |
| Reality Checker | 最终质量门 |
| Performance Benchmarker | 负载测试 |

### 合规与治理
| 智能体 | 角色 |
|-------|------|
| Legal Compliance Checker | 监管合规 |
| Brand Guardian | 品牌一致性 |
| Finance Tracker | 预算跟踪 |
| Executive Summary Generator | 利益相关者报告 |

### 质量保证
| 智能体 | 角色 |
|-------|------|
| Test Results Analyzer | 质量指标 |
| Workflow Optimizer | 流程改进 |
| Experiment Tracker | A/B 测试 |

## 执行计划

### 阶段 1：需求与架构（第 1-2 周）

```
第 1 周：利益相关者对齐
├── Project Shepherd → 利益相关者分析 + 沟通计划
├── UX Researcher → 功能需求的用户研究
├── Legal Compliance Checker → 合规需求扫描
├── Senior Project Manager → 规格到任务转换
└── Finance Tracker → 预算框架

第 2 周：技术架构
├── UX Architect → UX 基础 + 组件架构
├── Backend Architect → 系统架构 + 集成计划
├── UI Designer → 组件设计 + 设计系统更新
├── Sprint Prioritizer → RICE 评分待办列表
├── Brand Guardian → 品牌影响评估
└── 质量门：架构评审（Project Shepherd + Reality Checker）
```

### 阶段 2：基础（第 3 周）

```
├── DevOps Automator → 功能分支管道 + 功能开关
├── Frontend Developer → 组件脚手架
├── Backend Architect → API 脚手架 + 数据库迁移
├── Infrastructure Maintainer → Staging 环境设置
└── 质量门：基础验证（Evidence Collector）
```

### 阶段 3：构建（第 4-9 周）

```
冲刺 1-3（第 4-9 周）：
├── Agents Orchestrator → Dev↔QA 循环管理
├── Frontend Developer → UI 实现（逐任务）
├── Backend Architect → API 实现（逐任务）
├── Senior Developer → 复杂/高级功能
├── Evidence Collector → 每个任务 QA（截图）
├── API Tester → 每个 API 任务的端点验证
├── Experiment Tracker → 关键功能的 A/B 测试设置
│
├── 双周：
│   ├── Project Shepherd → 利益相关者状态更新
│   ├── Executive Summary Generator → 高管简报
│   └── Finance Tracker → 预算跟踪
│
└── 冲刺评审与利益相关者演示
```

### 阶段 4：加固（第 10-11 周）

```
第 10 周：证据收集
├── Evidence Collector → 完整截图套件
├── API Tester → 完整回归套件
├── Performance Benchmarker → 10 倍流量负载测试
├── Legal Compliance Checker → 最终合规审计
├── Test Results Analyzer → 质量指标仪表板
└── Infrastructure Maintainer → 生产就绪检查

第 11 周：最终裁决
├── Reality Checker → 集成测试（默认：需要改进）
├── 修复周期（如需要，2-3 天）
├── 重新验证
└── Executive Summary Generator → 通过/不通过建议
```

### 阶段 5：推出（第 12 周）

```
├── DevOps Automator → 金丝雀部署（5% → 25% → 100%）
├── Infrastructure Maintainer → 实时监控
├── Analytics Reporter → 功能采用跟踪
├── Support Responder → 新功能用户支持
├── Feedback Synthesizer → 早期反馈收集
└── Executive Summary Generator → 发布报告
```

## 利益相关者沟通节奏

| 受众 | 频率 | 智能体 | 格式 |
|------|------|--------|------|
| 高管发起人 | 双周 | Executive Summary Generator | SCQA 摘要（≤500 字） |
| 产品团队 | 每周 | Project Shepherd | 状态报告 |
| 工程团队 | 每日 | Agents Orchestrator | 管道状态 |
| 合规团队 | 每月 | Legal Compliance Checker | 合规状态 |
| 财务 | 每月 | Finance Tracker | 预算报告 |

## 质量要求

| 要求 | 阈值 | 验证方 |
|------|------|--------|
| 代码覆盖率 | > 80% | Test Results Analyzer |
| API 响应时间 | P95 < 200ms | Performance Benchmarker |
| 可访问性 | WCAG 2.1 AA | Evidence Collector |
| 安全性 | 零关键漏洞 | Legal Compliance Checker |
| 品牌一致性 | 95%+ 遵守度 | Brand Guardian |
| 规格合规性 | 100% | Reality Checker |
| 负载处理 | 10 倍当前流量 | Performance Benchmarker |

## 风险管理

| 风险 | 概率 | 影响 | 缓解措施 | 负责人 |
|------|------|------|----------|--------|
| 集成复杂性 | 高 | 高 | 早期集成测试，每个冲刺都有 API Tester | Backend Architect |
| 范围蔓延 | 中 | 高 | Sprint Prioritizer 执行 MoSCoW，Project Shepherd 管理变更 | Sprint Prioritizer |
| 合规问题 | 中 | 关键 | Legal Compliance Checker 从第一天就参与 | Legal Compliance Checker |
| 性能回归 | 中 | 高 | Performance Benchmarker 每个冲刺测试 | Performance Benchmarker |
| 利益相关者不一致 | 低 | 高 | 双周高管简报，Project Shepherd 协调 | Project Shepherd |
