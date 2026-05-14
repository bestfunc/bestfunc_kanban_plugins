# BestFunc Kanban Plugins

BestFunc Kanban 任务管理系统的 AI 客户端插件包。

## 安装

### Claude Code

```bash
# 方式一：从 GitHub 安装
/plugin marketplace add bestfunc/bestfunc_kanban_plugins
/plugin install kanban@kanban-plugins

# 方式二：本地安装
/plugin install /path/to/bestfunc_kanban_plugins/plugins/kanban
```

### 手动配置

在 `~/.claude/settings.json` 中添加 MCP 服务器：

```json
{
  "mcpServers": {
    "kanban": {
      "type": "http",
      "url": "https://task.bestfunc.com/api/mcp"
    },
    "kanban-files": {
      "command": "npx",
      "args": ["-y", "@bestfunc-com/kanban-file-mcp@latest"],
      "env": {
        "KANBAN_ENDPOINT": "https://task.bestfunc.com"
      }
    }
  }
}
```

## 包含内容

### MCP Servers

| 名称 | 类型 | 说明 |
|------|------|------|
| kanban | HTTP (远程) | 85+ 项目管理工具，OAuth 2.1 自动认证 |
| kanban-files | stdio (本地) | 文件上传/下载，避免 base64 编码开销 |

### Skills

| 名称 | 说明 |
|------|------|
| sync-skills | 从 Kanban 系统同步 AI Skills 到本地 |
| kanban-guide | MCP 工具使用指南和最佳实践 |

## 认证

首次使用时会自动弹出浏览器进行 OAuth 授权：

1. 浏览器打开 Kanban 登录页
2. 登录后点击"允许"授权
3. Token 自动保存到 `~/.kanban/` 目录
4. 后续使用自动复用，30 天有效

也可以使用管理员生成的 API Key（`mak-` 前缀）进行认证。
