# Issue 追踪系统：GitHub

本仓库的 Issue 和规格说明（Spec）以 GitHub Issue 形式管理。所有操作均使用 `gh` CLI。

## 操作约定

- **创建 Issue**: `gh issue create --title "..." --body "..."`。多行正文使用 Heredoc。
- **读取 Issue**: `gh issue view <number> --comments`，通过 `jq` 过滤评论并拉取标签。
- **列出 Issue**: `gh issue list --state open --json number,title,body,labels,comments --jq '[.[] | {number, title, body, labels: [.labels[].name], comments: [.comments[].body]}]'` 并配合适当的 `--label` 和 `--state` 过滤。
- **发表评论**: `gh issue comment <number> --body "..."`
- **添加 / 移除标签**: `gh issue edit <number> --add-label "..."` / `--remove-label "..."`
- **关闭 Issue**: `gh issue close <number> --comment "..."`

自动从 `git remote -v` 推断所属仓库——在克隆的代码库内部运行时 `gh` 会自动完成该推断。

## PR 作为分类定级对象

**PR 是否作为需求来源：否。** _（若本仓库将外部 PR 视为功能需求请求，设为 `yes`；`/triage` 会读取该标志。）_

当设置为 `yes` 时，PR 遵循与 Issue 完全相同的标签与状态机，使用 `gh pr` 的对应命令：

- **读取 PR**: `gh pr view <number> --comments` 以及用于获取 Diff 的 `gh pr diff <number>`。
- **列出待定级的外部 PR**: `gh pr list --state open --json number,title,body,labels,author,authorAssociation,comments`，随后仅保留 `authorAssociation` 为 `CONTRIBUTOR`、`FIRST_TIME_CONTRIBUTOR` 或 `NONE` 的条目（过滤掉 `OWNER`/`MEMBER`/`COLLABORATOR`）。
- **评论 / 打标 / 关闭**: `gh pr comment`、`gh pr edit --add-label`/`--remove-label`、`gh pr close`。

GitHub 的 Issue 与 PR 共享同一套编号体系，因此单露一个 `#42` 可能代表其中任一种——优先通过 `gh pr view 42` 解析，失败则回退到 `gh issue view 42`。

## 当技能提示“发布到 Issue 追踪系统”时

创建一个 GitHub Issue。

## 当技能提示“获取相关 Ticket”时

运行 `gh issue view <number> --comments`。

## 寻路规划操作 (Wayfinding operations)

由 `/wayfinder` 调阅。**地图 (Map)** 是带有 `wayfinder:map` 标签的单一 Issue，其**子项 (Child)** Issue 作为具体 Ticket。

- **地图 (Map)**：单个带有 `wayfinder:map` 标签的 Issue，包含 Notes / 迄今决策 (Decisions-so-far) / 迷雾 (Fog) 正文。`gh issue create --label wayfinder:map`。
- **子 Ticket**：作为 GitHub Sub-issue 关联到地图的 Issue（在 Sub-issues 端点上调用 `gh api`）。若未启用 Sub-issues，在地图正文的任务列表中列出子项，并在子项正文顶部注明 `Part of #<map>`。标签：`wayfinder:<type>`（`research`/`prototype`/`grilling`/`task`）。一旦被认领，该 Ticket 被指派给执行开发者。
- **阻塞关系 (Blocking)**：GitHub 的**原生 Issue 依赖**——标准的 UI 可视化呈现。通过 `gh api --method POST repos/<owner>/<repo>/issues/<child>/dependencies/blocked_by -F issue_id=<blocker-db-id>` 添加阻塞边，其中 `<blocker-db-id>` 是阻塞项的数字 **Database ID**（`gh api repos/<owner>/<repo>/issues/<n> --jq .id`，*而非* `#number` 或 `node_id`）。GitHub 会汇报 `issue_dependencies_summary.blocked_by`（仅限未关闭的阻塞项——即时生效的门禁）。当原生依赖不可用时，回退到在子项正文顶部添加 `Blocked by: #<n>, #<n>`。当所有阻塞 Ticket 均关闭时，该 Ticket 自动解除阻塞。
- **前沿边界查询 (Frontier query)**：列出地图的所有未关闭子项（`gh issue list --state open`，作用域限定于地图的 Sub-issues / 任务列表），剔除存在未关闭阻塞项（`issue_dependencies_summary.blocked_by > 0`，或 `Blocked by` 行中有未关闭 Issue）或已有指派人的条目；按地图中先后顺序排在第一位的即为当前目标。
- **认领 (Claim)**：`gh issue edit <n> --add-assignee @me` — 会话的第一次写操作。
- **解决 (Resolve)**：`gh issue comment <n> --body "<answer>"`，随后 `gh issue close <n>`，然后在地图的“迄今决策”中追加上下文指针（要点 + 链接）。
