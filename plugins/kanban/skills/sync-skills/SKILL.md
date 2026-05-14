---
name: sync-skills
display_name: 同步 Skills
description: 从 BestFunc Kanban 系统同步 AI Skills 到本地，保持本地 AI 环境与系统一致
user-invocable: true
allowed-tools: mcp__kanban__*
---

# 同步 Skills

将 BestFunc Kanban 系统中维护的 AI Skills 同步到本地 Claude Code 环境。

## 用法

当用户说"同步 skills"、"更新 skills"、"拉取最新 skills"时触发此 skill。

## 执行步骤

1. **获取 Skills 列表**

   调用 Kanban API 获取所有活跃的全局 Skills：

   ```
   GET https://task.bestfunc.com/api/open/skills/export
   Authorization: Bearer <当前 MCP token>
   ```

   响应格式：
   ```json
   {
     "skills": [
       {
         "slug": "skill-name",
         "name": "显示名称",
         "description": "描述",
         "content": "完整的 SKILL.md 内容"
       }
     ],
     "total": 10
   }
   ```

2. **写入本地目录**

   将每个 skill 的 `content` 写入到本地 skills 目录：

   - Claude Code: `~/.claude/skills/{slug}/SKILL.md`
   - 如果目录不存在，创建它

3. **报告结果**

   告知用户同步了多少个 skills，列出名称。

## 注意事项

- 同步是全量覆盖，本地修改会被远端覆盖
- 仅同步全局 skills（scope=global 或 both），项目级 skills 不同步
- 需要有效的 MCP 认证（OAuth 或 API Key）
