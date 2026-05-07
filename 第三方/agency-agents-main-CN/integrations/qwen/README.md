# Qwen Code 集成

Qwen Code 使用 `.qwen/agents/` 中项目范围的 `.md` SubAgent 文件。

生成的文件来自 `scripts/convert.sh --tool qwen`，它会将每个 Agency 智能体写入一个 SubAgent Markdown 文件到 `integrations/qwen/agents/`。

## 生成

从仓库根目录：

```bash
./scripts/convert.sh --tool qwen
```

## 安装

从目标项目根目录运行安装程序：

```bash
cd /your/project && /path/to/agency-agents/scripts/install.sh --tool qwen
```

这会将生成的 SubAgent 文件复制到：

```text
.qwen/agents/
```

## 在 Qwen Code 中刷新

安装后：

- 在 Qwen Code 中运行 `/agents manage` 刷新智能体列表，或
- 重启当前 Qwen Code 会话

## 注意事项

- Qwen Code 是项目范围的，不是用户范围的
- 生成的 Qwen 文件使用最小前置元数据：`name`、`description` 和可选的 `tools`
- 如果你在此仓库中更新了智能体，重新安装前需重新生成 Qwen 输出
