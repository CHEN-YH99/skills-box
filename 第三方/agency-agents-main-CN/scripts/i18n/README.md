# 🇨🇳 中文（zh-CN）本地化

将智能体 YAML 前置元数据中的 `name` 和 `description` 字段本地化为简体中文。这使得中文用户在 Copilot Chat 的智能体选择器中能够阅读智能体名称。

## 文件

| 文件 | 描述 |
|------|------|
| `agent-names-zh.json` | 英文智能体名称 → 中文翻译的映射（130+ 条目） |
| `localize-agents-zh.ps1` | 读取 JSON 并更新已安装智能体文件的 PowerShell 脚本 |

## 用法

使用 `install.sh --tool copilot` 安装智能体后：

```powershell
# 将智能体名称本地化为中文
powershell -ExecutionPolicy Bypass -File scripts/i18n/localize-agents-zh.ps1
```

默认情况下，脚本处理：
- `%USERPROFILE%\.github\agents\`
- `%USERPROFILE%\.copilot\agents\`

如需自定义路径：

```powershell
powershell -File scripts/i18n/localize-agents-zh.ps1 -TargetDirs @("C:\custom\path\agents")
```

## 工作原理

1. 读取 `agent-names-zh.json`（UTF-8 编码）获取翻译映射
2. 对于目标目录中的每个 `.md` 文件：
   - 提取 YAML 前置元数据中的 `name:` 字段
   - 查找中文翻译
   - 替换 `name:` 和 `description:` 字段
   - 以 UTF-8 写回

## 结果

翻译前：
```yaml
---
name: Security Engineer
description: Threat modeling, secure code review, security architecture
---
```

翻译后：
```yaml
---
name: 安全工程师
description: 威胁建模、安全代码审查与应用安全架构专家
---
```

## 注意事项

- 仅修改**已安装的副本**（在 `~/.github/agents/` 中），不修改源文件
- 每次运行 `install.sh` 更新后需重新执行（更新会用英文原文覆盖）
- JSON 文件是翻译的单一事实来源——新智能体请添加到该文件中
- 脚本为纯 ASCII（避免 PowerShell 编码问题）；所有中文文本存放在 JSON 中
