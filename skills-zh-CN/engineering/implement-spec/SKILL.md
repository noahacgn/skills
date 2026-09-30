---
name: implement-spec
description: 将 /to-spec 和 /to-tickets 的产出实现为代码。
disable-model-invocation: true
---

用户已提供一份规格说明（Spec），它应有关联的 Ticket，用于描述如何实现这份 Spec。

Issue 追踪系统应已告知。若未配置，请告知用户运行 `/setup-matt-pocock-skills`。

目标是在单个**集成分支 (integration branch)** 上实现整份 Spec，并按照 Issue 追踪系统的工作关闭方式完成所有 Ticket。

这些 Ticket 不是一份步骤清单，而是带有阻塞关系的**任务图 (task graph)**。因此，总有一组已就绪、可供认领的 Ticket，构成**前沿边界 (frontier)**。

与子 Agent 之间的沟通应保持精简。主要通过**上下文指针 (context pointers)** 交流，指向 Spec、Ticket、调研笔记和此前的提交。不要重复指针所指向内容中已有的信息。

尽可能让**实现子 Agent (implementer subagents)** 在后台运行，以提高并发度。

## 执行步骤

1. 阅读 Spec 和 Ticket，理解任务图。

2. （可选）使用**探索子 Agent (exploration subagent)** 完成 Ticket 所需的探索，例如阅读相关代码文件或外部文档。确保探索子 Agent 能保存文件：它应将 Markdown 笔记保存在仓库外、后续所有子 Agent 都可访问的目录中。这样，**实现子 Agent** 就能专注于实现，而非探索。

3. 创建集成分支。若 Issue 追踪系统通过 PR 关闭工作，或用户要求创建 PR，则在步骤 5 完成首次合并后创建草稿 PR，并标记它会关闭该 Spec 及其 Ticket。相对于 main 尚无新增提交的分支无法创建 PR。

4. 使用**实现子 Agent** 分别实现各个 Ticket，每个子 Agent 使用独立的 worktree 和分支。每个实现子 Agent 都要：
   - 开始前确认自己的 worktree 基于集成分支；若不是，则重置到集成分支；
   - 调用 Skill 工具，传入 `tdd`，实现该 Ticket；
   - 在报告完成前，将集成分支的最新提交合并到自己的分支。

5. 某个**实现子 Agent** 完成后，使用**合并子 Agent (merger subagent)** 将其工作合并到集成分支。

6. 若这次合并改变了可认领 Ticket 的**前沿边界**，则启动更多**实现子 Agent** 处理新就绪的 Ticket，以尽可能提高并发度。

7. 所有 Ticket 完成后，调用 Skill 工具，传入 `code-review`，审查集成分支。由单个**实现子 Agent** 修复审查提出的所有问题。

8. 若存在草稿 PR，将其标记为可供审查；否则，按照 Issue 追踪系统的工作关闭方式完成各个 Ticket，并报告集成分支。

9. 清理所有**实现子 Agent** 的 worktree。
