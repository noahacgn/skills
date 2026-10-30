# Issue 追踪系统：GitHub

本仓库的 Issue 和规格说明（Spec）以 GitHub Issue 形式管理。所有操作均使用 `gh` CLI。

## 操作约定

- **创建 Issue**: `gh issue create --title "..." --body "..."`。多行正文使用 Heredoc。
- **读取 Issue**: `gh issue view <number> --json number,title,body,labels,comments`。
- **列出 Issue**: `gh issue list --state open --json number,title,body,labels,comments --jq '[.[] | {number, title, body, labels: [.labels[].name], comments: [.comments[].body]}]'` 并配合适当的 `--label` 和 `--state` 过滤。
- **将 Issue 关联为子 Issue**: 使用 `gh issue create --parent <parent> ...`，或在创建后使用 `gh issue edit <parent> --add-sub-issue <child>`（需要 `gh` 2.94+）。较旧版本的 `gh` 使用 `gh api --method POST repos/<owner>/<repo>/issues/<parent>/sub_issues -F sub_issue_id=<child-db-id>`（这里使用数据库 ID，见下方的**阻塞关系 (Blocking)**）。若不支持子 Issue，在子项正文顶部注明 `Part of #<parent>`。
- **阻塞关系 (Blocking)**：GitHub 的**原生 Issue 依赖**，这是标准且可在 UI 中查看的表示方式。使用 `gh issue create --blocked-by <n>,<n> ...` 或 `gh issue edit <child> --add-blocked-by <blocker>` 添加阻塞边（需要 `gh` 2.94+）。较旧版本的 `gh` 使用 `gh api --method POST repos/<owner>/<repo>/issues/<child>/dependencies/blocked_by -F issue_id=<blocker-db-id>`，其中 `<blocker-db-id>` 是阻塞项的数字**数据库 ID**（`gh api repos/<owner>/<repo>/issues/<n> --jq .id`，*而非* `#number` 或 `node_id`）。当原生依赖不可用时，回退到在子项正文顶部添加 `Blocked by: #<n>, #<n>`。当所有阻塞 Ticket 均关闭时，该 Ticket 解除阻塞。
- **统计未关闭的阻塞项 (Count open blockers)**：`gh api repos/<owner>/<repo>/issues/<n> --jq .issue_dependencies_summary.blocked_by`（只统计未关闭的阻塞项，0 表示可开始）。`gh issue view <n> --json blockedBy` 也会列出已关闭的阻塞项，因此需要过滤：`--jq '[.blockedBy.nodes[] | select(.state == "OPEN")] | length'`。
- **发表评论**: `gh issue comment <number> --body "..."`
- **添加 / 移除标签**: `gh issue edit <number> --add-label "..."` / `--remove-label "..."`
- **关闭 Issue**: 先用 `gh issue comment <number> --body "..."` 发表评论说明原因，再执行 `gh issue close <number>`。对于已经关闭的 Issue，`gh issue close --comment` 会丢弃评论，但仍以退出码 0 结束。

自动从 `git remote -v` 推断所属仓库，在克隆的代码库内部运行时 `gh` 会自动完成该推断。

只在用户自己的仓库中创建 Issue。除非用户是仓库所有者，否则绝不要向 `mattpocock/skills`（这套技能的来源仓库）创建 Issue。对于 Fork，`gh` 可能默认使用上游父仓库，因此在会话第一次执行 `gh issue create` 前，先用 `gh repo view --json nameWithOwner` 检查目标；若它不是用户自己的仓库，向用户确认。

## PR 作为分类定级对象

**PR 是否作为需求来源：否。** _（若本仓库将外部 PR 视为功能需求请求，设为 `yes`；`/triage` 会读取该标志。）_

当设置为 `yes` 时，PR 遵循与 Issue 完全相同的标签与状态机，使用 `gh pr` 的对应命令：

- **读取 PR**: 先执行 `gh pr view <number>`，再执行 `gh pr view <number> --comments`（在非 TTY 环境下，`--comments` 仅输出评论，不包含正文），并使用 `gh pr diff <number>` 获取 Diff。
- **列出待定级的外部 PR**: `gh api --paginate 'repos/{owner}/{repo}/pulls?state=open' --jq '.[] | select(.author_association | IN("OWNER","MEMBER","COLLABORATOR") | not) | {number, title, author: .user.login, author_association, labels: [.labels[].name]}'`。
- **评论 / 打标 / 关闭**: `gh pr comment`、`gh pr edit --add-label`/`--remove-label`、`gh pr close`。

GitHub 的 Issue 与 PR 共享同一套编号体系，因此单露一个 `#42` 可能代表其中任一种，优先通过 `gh pr view 42` 解析，失败则回退到 `gh issue view 42`。

## 当技能提示“发布到 Issue 追踪系统”时

创建一个 GitHub Issue。

## 当技能提示“获取相关 Ticket”时

按上方**读取 Issue**中的方式读取。

## 寻路规划操作 (Wayfinding operations)

由 `/wayfinder` 调阅。**地图 (Map)** 是带有 `wayfinder:map` 标签的单一 Issue，其**子项 (Child)** Issue 作为具体 Ticket。

- **地图 (Map)**：单个带有 `wayfinder:map` 标签的 Issue，包含 Notes / 迄今决策 (Decisions-so-far) / 迷雾 (Fog) 正文。`gh issue create --label wayfinder:map`。
- **子 Ticket**：作为 GitHub Sub-issue 关联到地图的 Issue（见上方**将 Issue 关联为子 Issue**）。若未启用 Sub-issues，在地图正文的任务列表中列出子项，并在子项正文顶部注明 `Part of #<map>`。标签：`wayfinder:<type>`（`research`/`prototype`/`grilling`/`task`）。一旦被认领，该 Ticket 被指派给执行开发者。
- **阻塞关系 (Blocking)**：参见**操作约定**下的**阻塞关系 (Blocking)**。
- **前沿边界查询 (Frontier query)**：列出地图的所有未关闭子项（`gh issue list --state open`，作用域限定于地图的 Sub-issues / 任务列表），剔除存在未关闭阻塞项（参见**统计未关闭的阻塞项**，或 `Blocked by` 行中有未关闭 Issue）或已有指派人的条目；按地图中先后顺序排在第一位的即为当前目标。
- **认领 (Claim)**：`gh issue edit <n> --add-assignee @me`，会话的第一次写操作。
- **解决 (Resolve)**：`gh issue comment <n> --body "<answer>"`，随后 `gh issue close <n>`，然后在地图的“迄今决策”中追加上下文指针（要点 + 链接）。
