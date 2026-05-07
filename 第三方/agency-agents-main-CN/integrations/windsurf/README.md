# Windsurf 集成

完整的 Agency 名单合并为单个 `.windsurfrules` 文件。规则是**项目范围**的——从项目根目录安装。

## 安装

```bash
# 从项目根目录运行
cd /your/project
/path/to/agency-agents/scripts/install.sh --tool windsurf
```

## 激活智能体

在 Windsurf 中，在提示中通过名称引用智能体：

```
使用 Frontend Developer 智能体构建此组件。
```

## 重新生成

```bash
./scripts/convert.sh --tool windsurf
```
