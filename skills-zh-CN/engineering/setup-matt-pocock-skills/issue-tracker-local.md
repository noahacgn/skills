# Issue 追踪系统：本地 Markdown

本仓库的 Issue 和规格说明（Spec）以 Markdown 文件形式保存在 `.scratch/` 下。

## 操作约定

- 每个功能特性对应一个独立目录：`.scratch/<feature-slug>/`
- 规格说明文件为 `.scratch/<feature-slug>/spec.md`
- 具体实现 Ticket 在 `.scratch/<feature-slug>/issues/<NN>-<slug>.md` 下以单文件形式独立管理，从 `01` 开始连续编号——绝不要将多个 Ticket 合并在单个文件中
- 分类定级状态记录在每个 Issue 文件顶部的 `Status:` 行（角色字符串参见 `triage-labels.md`）
- 评论与对话历史追加在文件末尾的 `## Comments` 标题下方

## 当技能提示“发布到 Issue 追踪系统”时

在 `.scratch/<feature-slug>/` 下创建一个新文件（必要时自动创建目录）。

## 当技能提示“获取相关 Ticket”时

读取对应路径下的文件。用户通常会直接传入文件路径或 Issue 编号。

## 寻路规划操作 (Wayfinding operations)

由 `/wayfinder` 调阅。**地图 (Map)** 是一个文件，其每个 Ticket 对应一个**子文件**。

- **地图 (Map)**：`.scratch/<effort>/map.md` — 包含 Notes / 迄今决策 (Decisions-so-far) / 迷雾 (Fog) 正文。
- **子 Ticket**：`.scratch/<effort>/issues/NN-<slug>.md`，从 `01` 开始编号，正文中包含具体问题。`Type:` 行记录 Ticket 类型（`research`/`prototype`/`grilling`/`task`）；`Status:` 行记录 `claimed`/`resolved`。
- **阻塞关系 (Blocking)**：在靠近顶部处添加 `Blocked by: NN, NN` 行。当其列出的所有文件均处于 `resolved` 状态时，该 Ticket 解除阻塞。
- **前沿边界查询 (Frontier)**：扫描 `.scratch/<effort>/issues/` 下处于未关闭、未阻塞且未被认领状态的文件；按编号顺序排在首位的胜出。
- **认领 (Claim)**：在开展任何工作前将状态设置为 `Status: claimed` 并保存。
- **解决 (Resolve)**：在 `## Answer` 标题下追加答案，设置 `Status: resolved`，随后在 `map.md` 的“迄今决策”中追加上下文指针（要点 + 链接）。
