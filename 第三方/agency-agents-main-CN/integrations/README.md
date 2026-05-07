# 🔌 集成

本目录包含 The Agency 的集成和已转换的格式，用于支持的智能体编码工具。

## 支持的工具

- **[Claude Code](#claude-code)** — `.md` 智能体，直接使用仓库
- **[GitHub Copilot](#github-copilot)** — `.md` 智能体，直接使用仓库
- **[Antigravity](#antigravity)** — `antigravity/` 中每个智能体一个 `SKILL.md`
- **[Gemini CLI](#gemini-cli)** — 扩展 + `gemini-cli/` 中的 `SKILL.md` 文件
- **[OpenCode](#opencode)** — `opencode/` 中的 `.md` 智能体文件
- **[OpenClaw](#openclaw)** — `SOUL.md` + `AGENTS.md` + `IDENTITY.md` 工作区
- **[Cursor](#cursor)** — `cursor/` 中的 `.mdc` 规则文件
- **[Aider](#aider)** — `aider/` 中的 `CONVENTIONS.md`
- **[Windsurf](#windsurf)** — `windsurf/` 中的 `.windsurfrules`
- **[Kimi Code](#kimi-code)** — `kimi/` 中的 YAML 智能体规范
- **[Qwen Code](#qwen-code)** — `.qwen/agents/` 中项目范围的 `.md` SubAgent

## 快速安装

```bash
# 自动安装所有检测到的工具
./scripts/install.sh

# 安装特定的用户范围工具
./scripts/install.sh --tool antigravity
./scripts/install.sh --tool copilot
./scripts/install.sh --tool openclaw
./scripts/install.sh --tool claude-code

# Gemini CLI 在全新克隆时需要生成集成文件
./scripts/convert.sh --tool gemini-cli
./scripts/install.sh --tool gemini-cli

# Qwen Code 在全新克隆时也需要生成 SubAgent 文件
./scripts/convert.sh --tool qwen
./scripts/install.sh --tool qwen
```

如果安装了 OpenClaw 且网关已在运行，安装后请重启：

```bash
openclaw gateway restart
```

对于项目范围的工具，如 OpenCode、Cursor、Aider、Windsurf 和 Qwen Code，请从目标项目根目录运行安装程序，如下方各工具特定部分所示。

## 重新生成集成文件

如果你添加或修改了智能体，重新生成所有集成文件：

```bash
./scripts/convert.sh
```

---

## Claude Code

The Agency 最初是为 Claude Code 设计的。智能体原生工作，无需转换。

```bash
cp -r <category>/*.md ~/.claude/agents/
# 或一次性安装所有：
./scripts/install.sh --tool claude-code
```

详见 [claude-code/README.md](claude-code/README.md)。

---

## GitHub Copilot

The Agency 也原生支持 GitHub Copilot。智能体可以直接复制到 `~/.github/agents/` 和 `~/.copilot/agents/`，无需转换。

```bash
./scripts/install.sh --tool copilot
```

详见 [github-copilot/README.md](github-copilot/README.md)。

---

## Antigravity

技能安装到 `~/.gemini/antigravity/skills/`。每个智能体成为以 `agency-` 为前缀的独立技能，以避免命名冲突。

```bash
./scripts/install.sh --tool antigravity
```

详见 [antigravity/README.md](antigravity/README.md)。

---

## Gemini CLI

智能体被打包为 Gemini CLI 扩展，包含独立的技能文件。扩展安装到 `~/.gemini/extensions/agency-agents/`。由于 Gemini 清单和技能文件夹是生成的产物，从全新克隆安装前需运行 `./scripts/convert.sh --tool gemini-cli`。

```bash
./scripts/convert.sh --tool gemini-cli
./scripts/install.sh --tool gemini-cli
```

详见 [gemini-cli/README.md](gemini-cli/README.md)。

---

## OpenCode

每个智能体成为 `.opencode/agents/` 中的项目范围 `.md` 文件。

```bash
cd /your/project && /path/to/agency-agents/scripts/install.sh --tool opencode
```

详见 [opencode/README.md](opencode/README.md)。

---

## OpenClaw

每个智能体成为包含 `SOUL.md`、`AGENTS.md` 和 `IDENTITY.md` 的 OpenClaw 工作区。

安装前，先生成 OpenClaw 工作区：

```bash
./scripts/convert.sh --tool openclaw
```

然后安装：

```bash
./scripts/install.sh --tool openclaw
```

详见 [openclaw/README.md](openclaw/README.md)。

---

## Cursor

每个智能体成为 `.mdc` 规则文件。规则是项目范围的——从项目根目录运行安装程序。

```bash
cd /your/project && /path/to/agency-agents/scripts/install.sh --tool cursor
```

详见 [cursor/README.md](cursor/README.md)。

---

## Aider

所有智能体合并为单个 `CONVENTIONS.md` 文件，当该文件存在于项目根目录时 Aider 会自动读取。

```bash
cd /your/project && /path/to/agency-agents/scripts/install.sh --tool aider
```

详见 [aider/README.md](aider/README.md)。

---

## Windsurf

所有智能体合并为项目根目录的单个 `.windsurfrules` 文件。

```bash
cd /your/project && /path/to/agency-agents/scripts/install.sh --tool windsurf
```

详见 [windsurf/README.md](windsurf/README.md)。

---

## Kimi Code

每个智能体转换为 Kimi Code CLI 智能体规范（YAML 格式，带独立的系统提示文件）。智能体安装到 `~/.config/kimi/agents/`。

由于 Kimi 智能体文件是从源 Markdown 生成的，从全新克隆安装前需运行 `./scripts/convert.sh --tool kimi`。

```bash
./scripts/convert.sh --tool kimi
./scripts/install.sh --tool kimi
```

### 使用方法

安装后，使用 `--agent-file` 标志调用智能体：

```bash
kimi --agent-file ~/.config/kimi/agents/frontend-developer/agent.yaml
```

或在特定项目中：

```bash
cd /your/project
kimi --agent-file ~/.config/kimi/agents/frontend-developer/agent.yaml \
     --work-dir /your/project
```

详见 [kimi/README.md](kimi/README.md)。

---

## Qwen Code

每个智能体成为 `.qwen/agents/` 中的项目范围 `.md` SubAgent 文件。

从全新克隆，先生成 Qwen 文件：

```bash
./scripts/convert.sh --tool qwen
```

然后从项目根目录安装：

```bash
cd /your/project && /path/to/agency-agents/scripts/install.sh --tool qwen
```

详见 [qwen/README.md](qwen/README.md)。
