# Cursor 集成

将完整的 Agency 名单转换为 Cursor `.mdc` 规则文件。规则是**项目范围**的——从项目根目录安装。

## 安装

```bash
# 从项目根目录运行
cd /your/project
/path/to/agency-agents/scripts/install.sh --tool cursor
```

这会在项目中创建 `.cursor/rules/<agent-slug>.mdc` 文件。

## 激活规则

在 Cursor 中，在提示中引用智能体：

```
@frontend-developer 审查此 React 组件的性能问题。
```

或通过编辑前置元数据将规则启用为始终生效：

```yaml
---
description: Expert frontend developer...
globs: "**/*.tsx,**/*.ts"
alwaysApply: true
---
```

## 重新生成

```bash
./scripts/convert.sh --tool cursor
```
