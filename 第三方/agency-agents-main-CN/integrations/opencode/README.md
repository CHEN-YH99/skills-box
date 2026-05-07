# OpenCode 集成

OpenCode 智能体是存储在 `.opencode/agents/` 中的带 YAML 前置元数据的 `.md` 文件。转换器将命名颜色映射为十六进制代码，并添加 `mode: subagent`，使智能体通过 `@agent-name` 按需调用，而非在主智能体选择器中造成混乱。

## 安装

```bash
# 从项目根目录运行
cd /your/project
/path/to/agency-agents/scripts/install.sh --tool opencode
```

这会在项目目录中创建 `.opencode/agents/<slug>.md` 文件。

## 激活智能体

在 OpenCode 中，使用 `@` 前缀调用子智能体：

```
@frontend-developer 帮我构建此组件。
```

```
@reality-checker 审查此 PR。
```

你也可以从 OpenCode UI 的智能体选择器中选择智能体。

## 智能体格式

每个生成的智能体文件包含：

```yaml
---
name: Frontend Developer
description: Expert frontend developer specializing in modern web technologies...
mode: subagent
color: "#00FFFF"
---
```

- **mode: subagent** — 智能体按需可用，不显示在主 Tab 循环列表中
- **color** — 十六进制代码（源文件中的命名颜色会自动转换）

## 项目 vs 全局

`.opencode/agents/` 中的智能体是**项目范围**的。要使它们在所有项目中全局可用，先生成智能体文件，然后使用 `--path` 安装：

```bash
./scripts/convert.sh --tool opencode
./scripts/install.sh --tool opencode --path ~/.config/opencode/agents
```

## 重新生成

```bash
./scripts/convert.sh --tool opencode
```
