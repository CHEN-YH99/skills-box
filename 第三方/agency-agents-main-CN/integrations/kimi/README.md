# Kimi Code CLI 集成

将所有 Agency 智能体转换为 Kimi Code CLI 智能体规范。每个智能体成为一个包含 `agent.yaml`（智能体规范）和 `system.md`（系统提示）的目录。

## 安装

### 前提条件

- 已安装 [Kimi Code CLI](https://github.com/MoonshotAI/kimi-cli)

### 安装步骤

```bash
# 生成集成文件（全新克隆时必须）
./scripts/convert.sh --tool kimi

# 安装智能体
./scripts/install.sh --tool kimi
```

这会将智能体复制到 `~/.config/kimi/agents/`。

## 使用方法

### 激活智能体

使用 `--agent-file` 标志加载特定智能体：

```bash
kimi --agent-file ~/.config/kimi/agents/frontend-developer/agent.yaml
```

### 在项目中使用

```bash
cd /your/project
kimi --agent-file ~/.config/kimi/agents/frontend-developer/agent.yaml \
     --work-dir /your/project \
     "审查此 React 组件的性能问题"
```

### 列出已安装的智能体

```bash
ls ~/.config/kimi/agents/
```

## 智能体结构

每个智能体目录包含：

```
~/.config/kimi/agents/frontend-developer/
├── agent.yaml    # 智能体规范（工具、子智能体）
└── system.md     # 包含人格和指令的系统提示
```

### agent.yaml 格式

```yaml
version: 1
agent:
  name: frontend-developer
  extend: default  # 继承 Kimi 内置的默认智能体
  system_prompt_path: ./system.md
  tools:
    - "kimi_cli.tools.shell:Shell"
    - "kimi_cli.tools.file:ReadFile"
    # ... 所有默认工具
```

## 重新生成

修改源智能体后：

```bash
./scripts/convert.sh --tool kimi
./scripts/install.sh --tool kimi
```

## 故障排除

### 找不到智能体文件

确保在 `install.sh` 之前运行了 `convert.sh`：

```bash
./scripts/convert.sh --tool kimi
```

### 未检测到 Kimi CLI

确保 `kimi` 在你的 PATH 中：

```bash
which kimi
kimi --version
```

### YAML 无效

验证生成的文件：

```bash
python3 -c "import yaml; yaml.safe_load(open('integrations/kimi/frontend-developer/agent.yaml'))"
```
