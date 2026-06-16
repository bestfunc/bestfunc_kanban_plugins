---
name: task-review
display_name: 任务评审
description: 对单个任务做设计评审 —— 在不了解项目整体的前提下，仅基于任务内补充的设计描述，检查需求覆盖、前后一致性、明显错误、过度设计四个维度；先给出评审清单与用户确认，确认后再用评审标注写回任务。
user-invocable: true
allowed-tools: mcp__kanban__*, mcp__kanban-files__*, Bash, WebFetch
---

# 任务评审

对指定任务做一次设计评审。

**核心原则**：
1. 不了解项目整体时，**只针对当前任务内的设计描述**评审，不臆断外部信息。
2. 评审意见**先整理成清单给用户确认**（可增 / 删 / 改），**确认后才写入**任务。

## 触发

用户说"评审任务 #XXX""review 某任务"并给出任务编号（如 `PD_001_7`）或任务 ID。

## 评审维度（固定四点）

1. **是否覆盖了需求** —— 逐条对照任务描述里的原始需求，看设计是否都回应（已覆盖 / 部分 / 未覆盖 / 明确延后到下期）
2. **是否存在设计前后不一致** —— 各设计字段之间（功能 / 技术 / 交互 / 依赖）、以及设计与需求之间是否冲突
3. **是否存在明显错误** —— 技术选型、逻辑、归属、依赖时序等硬伤或存疑点
4. **是否存在过度设计** —— 需求外的超前 / 冗余设计，尤其是当前没有消费方的能力

## 流程

### 1. 定位任务
- 任务编号形如 `<项目code>_<序号>`（如 `PD_001_7`）。用 `get_project_tasks` 的 `search` 参数；若拿不到默认项目，先 `list_all_projects` 找到项目 id 再传 `project_id`。
- ⚠️ **务必核对拿到的 task_id 与目标任务编号一致**。同项目多任务、或跨任务复用 id 时极易写错任务——这是高发事故，写标注前请再确认一次。

### 2. 读全任务的设计内容
- `get_task_details` → 描述（`description`）+ 自定义字段（`custom_fields`）。
- **富文本字段（richtext）** 值就是正文，直接读。
- **HTML 字段（type=html）的值是一个附件 id，不是正文**：用 `get_attachment_info(attachment_id)` 拿 `download_url`，再下载内容读取（内网可 `curl` / WebFetch 该 url；或用 `kanban-files` 的 `download_file` 下到本地再读）。
- 需要知道哪些字段可评审、字段标签、类型时：`get_task_template_details`（看字段的 `reviewable` / `label` / `type`）。

### 3. 看已有讨论，避免重复
- `get_task_comments` + `get_task_annotations`：若已有人评审 / 讨论过，不要重复提同一点。

### 4. 分析并给出评审清单（先不要写入）
按四个维度分析，整理成清单交给用户。每条包含：
- **挂哪个字段**（field_key，如 `description` / `富文本字段_2`）
- **锚定原文片段**（该字段内容里真实存在的一小段文字）
- **评审意见**（简明、可行动）
- **维度归类**（覆盖 / 一致性 / 错误 / 过度）

区分"明确延后 / 已沟通划入下期"与"真缺口"；语气中性（确认而非指责）。

### 5. 等用户确认
**必须**让用户过一遍清单（增 / 删 / 改），得到明确确认后才进入下一步。**不要未经确认直接写入。**

### 6. 写入评审
- 每条用 `add_task_annotation(task_id, field_key, anchor_quote, content)` 写成标注。
  - `anchor_quote` **必须是该字段内容里真实存在的连续片段**，否则前端高亮锚不上。优先选独特、且不含 markdown 符号（`**`、`\` 等）的纯文字片段。
  - `anchor_prefix` / `anchor_suffix` 可选，传入有助于精确定位（取片段前后约 50 字符）。
- 可选：`add_task_comment` 写一条总结，用 `@[姓名](user_id)` 提及负责人（`get_project_members` 拿 user_id；负责人通常是任务 assignee，范围类问题可同时 @ 创建者）。

## 注意事项 / 已知坑

- **task_id 张冠李戴**：写标注 / 评论前再确认一次 task_id 对应的就是目标任务编号。
- **HTML 字段无法用 `add_task_annotation` 锚定**（它锚文本，HTML 字段是附件 / 区域标注）。HTML 字段的意见可并入相邻富文本字段，或放进总结评论里说明。
- **标注作者 = 当前 MCP 授权用户**，评审会以该用户名义留痕。
- 标注后续管理：`resolve_task_annotation`（已处理 `resolved` / 不处理 `wont_fix`）、`reply_task_annotation`（回复）、`delete_task_annotation`（删除，仅作者本人）。
- 评论目前没有删除工具，写评论前想清楚内容。

## 输出

写入后告诉用户：写了几条标注（挂在哪些字段）、几条评论（@了谁），并提示在任务「评审模式」查看。
