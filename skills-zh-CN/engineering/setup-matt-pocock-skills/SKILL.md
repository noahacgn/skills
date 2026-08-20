---
name: setup-matt-pocock-skills
description: "为工程技能配置当前仓库：设置其 Issue 追踪系统、分类定级标签词汇表及业务领域文档布局。在首次使用其他工程技能前运行一次。"
disable-model-invocation: true
---

# 配置 Matt Pocock 技能库 (Setup Matt Pocock's Skills)

为工程技能初始化每个仓库所假设的基础配置脚手架：

- **Issue 追踪系统 (Issue tracker)**：Issue 存放位置（默认使用 GitHub；亦原生支持本地 Markdown 文件）
- **分类定级标签 (Triage labels)**：五种规范分类定级角色所使用的具体字符串
- **业务领域文档 (Domain docs)**：`CONTEXT.md` 与 ADR 的存放路径，以及查阅它们的消费规则

这是一项提示词驱动的技能，而非确定性的硬编码脚本。探索环境、呈现发现、与用户确认，随后写入文件。

## 执行流程

### 1. 探索与勘察 (Explore)

检查当前代码库以了解其初始状态。读取现有文件；切勿凭空假设：

- `git remote -v` 和 `.git/config`，这是否为 GitHub 仓库？具体是哪一个？
- 仓库根目录下的 `AGENTS.md` 和 `CLAUDE.md`，是否存在任一文件？其中是否已包含 `## Agent skills` 小节？
- 根目录下的 `CONTEXT.md` 与 `CONTEXT-MAP.md`
- `docs/adr/` 及任何 `src/*/docs/adr/` 目录
- `docs/agents/`：本技能先前的输出是否已存在？
- `.scratch/`：是否已在使用本地 Markdown Issue 追踪约定
- 是否已安装 `triage` 技能？（与本技能并列的 `triage` 目录，或可用技能列表中包含 `triage`。）这决定了是否需要运行 B 小节。
- Monorepo 标识，`pnpm-workspace.yaml`、`package.json` 中的 `workspaces` 字段，或拥有独立 `src/` 的 `packages/*`。仅在真正的大型多包仓库中存在；若无则为单上下文（绝大多数仓库均属此类）。

### 2. 呈现发现并发起确认 (Present findings and ask)

总结已存在与缺失的内容。随后依序推进各个小节，一次一个小节，确认后再进入下一节。

每个小节以推荐答案起头，让用户单字即可快速确认。仅在选项确实存在分叉时提供单行解释；当探索阶段已明确得出结论时完全跳过该小节（若未安装 `triage` 则跳过 B 小节，若非 Monorepo 则跳过 C 小节）。

**A 小节：Issue 追踪系统 (Issue tracker)。**

> 解释说明：Issue 追踪系统是本仓库管理 Issue 的场所。诸如 `to-tickets`、`triage` 和 `to-spec` 等技能需要向其读写数据，它们需要知道是调用 `gh issue create`、在 `.scratch/` 下写入 Markdown 文件，还是执行你描述的其他流程。请选择本仓库实际追踪工作的工具。

默认策略：这套技能专为 GitHub 设计。若 `git remote` 指向 GitHub，推荐 GitHub。若指向 GitLab（`gitlab.com` 或私有部署实例），推荐 GitLab。否则（或根据用户偏好），提供以下选项：

- **GitHub**：Issue 存放在仓库的 GitHub Issues 中（使用 `gh` CLI）
- **GitLab**：Issue 存放在仓库的 GitLab Issues 中（使用 [`glab`](https://gitlab.com/gitlab-org/cli) CLI）
- **本地 Markdown (Local markdown)**：Issue 作为文件保存在本仓库的 `.scratch/<feature>/` 目录下（适合个人独立项目或无远程仓库的项目）
- **其他 (Other)**（Jira、Linear 等）：请用户用一段话描述其操作工作流；技能将以自由文本形式记录

将选定结果记录在 `docs/agents/issue-tracker.md` 中。GitHub 和 GitLab 模板附带一个“PR 是否作为需求来源”的标志，默认**关闭 (no)**：保持关闭且无需主动提及；若用户希望将外部 PR 纳入分类定级队列，后续可自行在文件中修改该标志。

**B 小节：分类定级标签词汇表 (Triage label vocabulary)。** 若未安装 `triage` 技能（探索阶段已获知），完全跳过本节；未安装的技能无需标签。

若已安装，提出且仅提出一个问题：

> 你是否希望保留默认的分类定级标签？（推荐：**是**）

默认值为五种规范角色，每个标签字符串与角色名相同：`needs-triage`、`needs-info`、`ready-for-agent`、`ready-for-human`、`wontfix`。若选择**是**，原样写入。仅当用户回答否时，通常因为其追踪系统已有既定命名（例如用 `bug:triage` 代替 `needs-triage`），收集其映射别名，以便 `triage` 复用现有标签而非创建重复项。

**C 小节：业务领域文档 (Domain docs)。** 默认采用**单上下文 (single-context)**：根目录下的单份 `CONTEXT.md` + `docs/adr/`。这适合绝大多数仓库；无需询问直接采纳。

仅在探索发现 Monorepo 标识时，才提议**多上下文 (multi-context)**：根目录下的 `CONTEXT-MAP.md` 索引各个子上下文的 `CONTEXT.md`。随后与用户确认所需布局。

### 3. 确认与预览草稿 (Confirm and edit)

向用户展示以下内容的草稿：

- 即将添加到被选定的 `CLAUDE.md` / `AGENTS.md` 中的 `## Agent skills` 代码块（选择规则参见步骤 4）
- `docs/agents/issue-tracker.md`、`docs/agents/domain.md` 和 `docs/agents/triage-labels.md`（仅在安装了 `triage` 时包含后者）的正文内容

在写入前允许用户进行修改。

### 4. 写入文件 (Write)

**选择要编辑的文件：**

- 若存在 `CLAUDE.md`，编辑它。
- 否则若存在 `AGENTS.md`，编辑它。
- 若两者均不存在，询问用户希望创建哪一个，不要替用户做决定。

当 `CLAUDE.md` 已存在时绝不要再新建 `AGENTS.md`（反之亦然），始终编辑已有的那一个。

若选定文件中已存在 `## Agent skills` 区块，就地更新其内容，而非在末尾追加重复内容。不要覆盖用户对周边小节的修改。

该区块内容：

```markdown
## Agent skills

### Issue tracker

[用一句话总结 Issue 的追踪位置]. 参见 `docs/agents/issue-tracker.md`.

### Triage labels

[用一句话总结标签词汇表]. 参见 `docs/agents/triage-labels.md`.

### Domain docs

[用一句话总结文档布局，“单上下文 (single-context)”或“多上下文 (multi-context)”]. 参见 `docs/agents/domain.md`.
```

仅在安装了 `triage` 且运行了 B 小节时，才包含 `### Triage labels` 子区块并写入 `docs/agents/triage-labels.md`。若未安装，两者均省略。

随后以本技能目录中的种子模板为起点写入文档文件：

- [issue-tracker-github.md](./issue-tracker-github.md)：GitHub Issue 追踪系统
- [issue-tracker-gitlab.md](./issue-tracker-gitlab.md)：GitLab Issue 追踪系统
- [issue-tracker-local.md](./issue-tracker-local.md)：本地 Markdown Issue 追踪系统
- [triage-labels.md](./triage-labels.md)：标签映射（仅在安装了 `triage` 时生成）
- [domain.md](./domain.md)：领域文档消费规则与布局

对于“其他”类型的 Issue 追踪系统，根据用户的描述从零编写 `docs/agents/issue-tracker.md`。

### 5. 完成收尾

告知用户配置已就绪，并列出后续将读取这些文件的各项工程技能。提示用户后续可以直接编辑 `docs/agents/*.md`，仅在希望切换 Issue 追踪系统或从零重置时才需要重新运行本技能。
