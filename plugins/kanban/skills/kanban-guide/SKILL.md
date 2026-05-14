---
name: kanban-guide
display_name: Kanban 使用指南
description: BestFunc Kanban MCP 工具使用指南，了解可用工具和最佳实践
user-invocable: true
allowed-tools: mcp__kanban__*
---

# BestFunc Kanban MCP 使用指南

## 概述

BestFunc Kanban 提供 85+ MCP 工具，覆盖项目管理全流程。通过两个 MCP server 接入：

- **kanban**（远程 HTTP）：项目、任务、里程碑、成员等所有管理操作
- **kanban-files**（本地 stdio）：大文件上传/下载，避免 base64 编码开销

## 常用操作

### 查看项目
```
mcp__kanban__list_all_projects          → 列出所有项目
mcp__kanban__get_project_overview       → 项目概览 + 统计
mcp__kanban__get_project_members        → 项目成员列表
```

### 任务管理
```
mcp__kanban__get_project_tasks          → 查询任务（支持筛选）
mcp__kanban__create_task                → 创建任务
mcp__kanban__update_task                → 更新任务字段
mcp__kanban__update_task_status         → 快速更新状态
mcp__kanban__add_task_comment           → 添加评论
```

### 里程碑
```
mcp__kanban__get_project_milestones     → 里程碑列表
mcp__kanban__create_milestone           → 创建里程碑
```

### 文件操作
```
mcp__kanban__get_task_attachments       → 查看附件
mcp__kanban_files__upload_file          → 上传文件（本地 MCP）
mcp__kanban_files__download_file        → 下载文件（本地 MCP）
```

## 工具分类

| 类别 | 工具数 | 说明 |
|------|--------|------|
| 任务 | 11 | CRUD + 关联 + 状态 |
| 项目 | 8 | 概览 + 统计 + 活动 |
| 里程碑 | 9 | 定义 + 实例 |
| 成员 | 5 | 项目成员管理 |
| 角色组 | 7 | 团队分组 |
| 评论 | 2 | 任务评论 |
| 附件 | 2 | 文件管理 |
| 状态 | 4 | 任务状态定义 |
| 工作流 | 6 | 模板 + 依赖 |
| 通知 | 5 | 消息通知 |
| 标签 | 2 | 项目标签 |
| 触发器 | 6 | 自动化规则 |
| 邮件 | 3 | 收发邮件 |
| 小程序 | 4 | Mini App CRUD |

## 权限说明

- MCP 工具的权限与你的 Kanban 账户一致
- 只能访问你是成员的项目
- 管理员可在系统管理中为你配置可用工具范围
- Viewer 角色只能查看，不能创建/修改

## 文件传输最佳实践

- **小文件**（<100KB 配置文件）：用远程 MCP 的附件查看工具
- **大文件**（>100KB）：用本地 MCP 的 upload_file / download_file
- 首次使用本地 MCP 会弹浏览器进行 OAuth 授权
