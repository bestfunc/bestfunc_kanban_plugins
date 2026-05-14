---
name: sync-skills
display_name: 同步 Skills
description: 从 BestFunc Kanban 系统同步 AI Skills 到本地，保持本地 AI 环境与系统一致
user-invocable: true
allowed-tools: Bash, Write, mcp__plugin_kanban_kanban__export_skills
---

# 同步 Skills

将 BestFunc Kanban 系统中维护的 AI Skills 同步到本地 Claude Code 环境。

## 用法

当用户说"同步 skills"、"更新 skills"、"拉取最新 skills"时触发此 skill。

## 执行步骤

1. **获取 Skills 列表**

   调用 MCP 工具 `export_skills`（无参数），返回所有活跃全局 Skills：

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

2. **写入插件 skills 目录**

   本 skill 运行时，系统会提供 `Base directory for this skill: <path>` 上下文。
   将 `<path>` 向上一级即为插件的 `skills/` 目录（例如 `<path>` 是 `.../skills/sync-skills`，则目标为 `.../skills/`）。

   将每个 skill 的 `content` 写入到：`{skills目录}/{slug}/SKILL.md`
   - 如果目录不存在，创建它
   - **禁止**写入 `~/.claude/skills/`（那是全局公共目录，不属于本插件）

3. **报告结果**

   告知用户同步了多少个 skills，列出名称。

## 注意事项

- 同步是全量覆盖，本地修改会被远端覆盖
- 同步所有系统内置 skills（包括 global、project、both 三种 scope）
- 认证通过 MCP OAuth 自动完成，无需额外配置
- 不要把 sync-skills 和 kanban-guide 这两个插件自带的 skill 覆盖掉
