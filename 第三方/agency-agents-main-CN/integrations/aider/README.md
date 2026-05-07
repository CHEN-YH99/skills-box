# Aider 集成

完整的 Agency 名单合并为单个 `CONVENTIONS.md` 文件。当该文件存在于项目根目录时，Aider 会自动读取。

## 安装

```bash
# 从项目根目录运行
cd /your/project
/path/to/agency-agents/scripts/install.sh --tool aider
```

## 激活智能体

在 Aider 会话中，通过名称引用智能体：

```
使用 Frontend Developer 智能体重构此组件。
```

```
应用 Reality Checker 智能体验证此功能是否可用于生产。
```

## 手动使用

你也可以直接传递规范文件：

```bash
aider --read CONVENTIONS.md
```

## 重新生成

```bash
./scripts/convert.sh --tool aider
```
