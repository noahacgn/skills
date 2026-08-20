# Issue 追踪系统：GitLab

本仓库的 Issue 和规格说明（Spec）以 GitLab Issue 形式管理。所有操作均使用 [`glab`](https://gitlab.com/gitlab-org/cli) CLI。

## 操作约定

- **创建 Issue**: `glab issue create --title "..." --description "..."`。多行描述使用 Heredoc。传入 `--description -` 打开编辑器。
- **读取 Issue**: `glab issue view <number> --comments`。使用 `-F json` 获取机器可读格式。
- **列出 Issue**: `glab issue list -F json` 配合适当的 `--label` 过滤。
- **发表评论**: `glab issue note <number> --message "..."`。GitLab 将评论称为“note”。
- **添加 / 移除标签**: `glab issue update <number> --label "..."` / `--unlabel "..."`。多个标签可用逗号分隔或重复该参数。
- **关闭 Issue**: `glab issue close <number>`。由于 `glab issue close` 不直接支持携带关闭评论，先使用 `glab issue note <number> --message "..."` 发送解释说明，然后再执行关闭。
- **合并请求 (Merge Requests)**: GitLab 将 PR 称为“Merge Request (MR)”。使用 `glab mr create`、`glab mr view`、`glab mr note` 等，参数结构与 `gh pr ...` 保持一致，仅将 `pr` 换为 `mr`，将 `comment`/`--body` 换为 `note`/`--message`。

自动从 `git remote -v` 推断所属仓库，在克隆的代码库内部运行时 `glab` 会自动完成该推断。

## 合并请求 (MR) 作为分类定级对象

**MR 是否作为需求来源：否。** _（若本仓库将外部 MR 视为功能需求请求，设为 `yes`；`/triage` 会读取该标志。）_

当设置为 `yes` 时，MR 遵循与 Issue 完全相同的标签与状态机，使用 `glab mr` 的对应命令：

- **读取 MR**: `glab mr view <number> --comments` 以及用于获取 Diff 的 `glab mr diff <number>`。
- **列出待定级的外部 MR**: `glab mr list -F json`，随后仅保留作者非项目成员/所有者的 MR（贡献者的 MR，而非维护者正在进行的工作）。
- **评论 / 打标 / 关闭**: `glab mr note`、`glab mr update --label`/`--unlabel`、`glab mr close`。

不同于 GitHub，GitLab 对 Issue 与 MR 分别独立编号，因此一旦明确了维护者指的是哪类对象，`#42` 就不存在歧义。

## 当技能提示“发布到 Issue 追踪系统”时

创建一个 GitLab Issue。

## 当技能提示“获取相关 Ticket”时

运行 `glab issue view <number> --comments`。

## 寻路规划操作 (Wayfinding operations)

由 `/wayfinder` 调阅。**地图 (Map)** 是带有 `wayfinder:map` 标签的单一 Issue，其**子项 (Child)** Issue 作为具体 Ticket。

- **地图 (Map)**：单个带有 `wayfinder:map` 标签的 Issue，包含 Notes / 迄今决策 (Decisions-so-far) / 迷雾 (Fog) 正文。`glab issue create --label wayfinder:map`。（在支持原生 Epic 的 GitLab 订阅层级中，Epic 也可作为地图载体；带有标签的 Issue 则在所有版本通用。）
- **子 Ticket**：在描述顶部包含 `Part of #<map>` 且带有 `wayfinder:<type>`（`research`/`prototype`/`grilling`/`task`）标签的 Issue。一旦被认领，该 Ticket 被指派给执行开发者。
- **阻塞关系 (Blocking)**：GitLab 的**原生阻塞关联**：标准的 UI 可视化呈现。通过以 Note 形式发送 `/blocked_by #<n>` 快捷指令来添加（`glab issue note <child> --message "/blocked_by #<blocker>"`）。原生阻塞关联属于 Premium/Ultimate 高级功能；在免费版本中（或不可用时），回退到在描述顶部添加 `Blocked by: #<n>, #<n>`。当所有阻塞 Ticket 均关闭时，该 Ticket 自动解除阻塞。
- **前沿边界查询 (Frontier query)**：`glab issue list -F json` 限定在地图的子项范围内，剔除存在未关闭阻塞项（通过 `glab api projects/:id/issues/:iid/links` 查询的原生 `blocked_by` 关联，或 `Blocked by` 行中有未关闭 Issue）或已有指派人的条目；按地图顺序排在首位的胜出。
- **认领 (Claim)**：`glab issue update <n> --assignee @me`，会话的第一次写操作。
- **解决 (Resolve)**：`glab issue note <n> --message "<answer>"`，随后 `glab issue close <n>`，然后在地图的“迄今决策”中追加上下文指针（要点 + 链接）。
