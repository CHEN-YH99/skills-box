# Antigravity 集成

将完整的 Agency 名单安装为 Antigravity 技能。每个智能体以 `agency-` 为前缀，以避免与现有技能冲突。

## 安装

```bash
./scripts/install.sh --tool antigravity
```

此命令将 `integrations/antigravity/` 中的文件复制到 `~/.gemini/antigravity/skills/`。

## 激活技能

在 Antigravity 中，通过 slug 激活智能体：

```
使用 agency-frontend-developer 技能审查此组件。
```

可用的 slug 遵循 `agency-<agent-name>` 模式，例如：
- `agency-frontend-developer`
- `agency-backend-architect`
- `agency-reality-checker`
- `agency-growth-hacker`

## 重新生成

修改智能体后，重新生成技能文件：

```bash
./scripts/convert.sh --tool antigravity
```

## 文件格式

每个技能是一个 `SKILL.md` 文件，包含 Antigravity 兼容的前置元数据：

```yaml
---
name: agency-frontend-developer
description: Expert frontend developer specializing in...
risk: low
source: community
date_added: '2026-03-08'
---
```
