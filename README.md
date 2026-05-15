# BestFunc Kanban Plugins

BestFunc Kanban 任务管理系统的 AI 客户端插件包，支持 Claude Code / Qwen Code 等 CLI。

一条命令接入 **85+ MCP 工具 + 文件传输 + AI Skills**，覆盖任务管理、项目报告、排期编排、会议纪要、邮件通知等场景。MCP 认证走 OAuth 2.1，首次使用自动弹出浏览器授权页，无需手动配 token。

## 安装

### Claude Code

```bash
# 从 GitHub 安装
/plugin marketplace add bestfunc/bestfunc_kanban_plugins
/plugin install kanban@kanban-plugins

# 查看 MCP 连接状态
/mcp

# 首次触发 OAuth 授权
/mcp auth kanban
```

### Qwen Code（自动转换格式）

```bash
# 安装扩展
qwen extensions install bestfunc/bestfunc_kanban_plugins:kanban

# 重启 Qwen Code 让 MCP 配置生效
qwen

# 查看 MCP 连接状态
/mcp

# 触发 OAuth 授权
/mcp auth kanban
```

Qwen Code 会自动把 Claude plugin 格式转成 Qwen extensions 格式并写入 `~/.qwen/extensions/<name>/qwen-extension.json`。

### 其他支持 MCP 的客户端（Cursor / Zed / Cline 等）

本仓库 skill 是纯 Markdown，按客户端各自的规范复制到对应目录即可；MCP connector 单独按客户端 UI 手动配：

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
