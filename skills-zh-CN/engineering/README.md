# 工程技能 (Engineering)

日常代码开发工作中使用的技能。

## 用户触发 (User-invoked)

仅在用户手动输入时触发（Claude Code 中配置 `disable-model-invocation: true`；Codex 中在 `agents/openai.yaml` 中配置 `policy.allow_implicit_invocation: false`）。

- **[ask-matt](./ask-matt/SKILL.md)**：询问哪种技能或流程最契合你当前的情境。本仓库中所有用户触发技能的总路由器。
- **[grill-with-docs](./grill-with-docs/SKILL.md)**：结合文档的深度盘问会话，同时构建项目的领域模型，提炼专业术语并就地更新 `CONTEXT.md` 和 ADR。
- **[triage](./triage/SKILL.md)**：推动 Issue 穿越由不同分类定级角色构成的状态机。
- **[improve-codebase-architecture](./improve-codebase-architecture/SKILL.md)**：扫描代码库以寻找模块深化（deepening）机会，以可视化 HTML 报告形式呈现，随后针对你选定的方案进行深度盘问。
- **[setup-matt-pocock-skills](./setup-matt-pocock-skills/SKILL.md)**：为工程技能配置当前仓库（Issue 追踪系统、分类标签、领域文档布局）。每个代码仓库运行一次。
- **[to-spec](./to-spec/SKILL.md)**：将当前对话沉淀为规格说明（Spec）并发布到 Issue 追踪系统。
- **[to-tickets](./to-tickets/SKILL.md)**：将任意计划、规格说明或对话拆解为一组示踪弹 Ticket（Tracer-bullet tickets），每个 Ticket 明确声明其阻塞依赖边，支持输出为本地文件中的文本，或真实追踪系统上的原生阻塞关联。
- **[implement](./implement/SKILL.md)**：针对规格说明或一组 Ticket 描述的工作开展编码实现，在预先约定的代码缝隙处驱动 `/tdd`，并在 Commit 前通过 `/code-review` 收尾。
- **[wayfinder](./wayfinder/SKILL.md)**：规划超出单个 Agent 会话容量的超大型工作，在 Issue 追踪系统上绘制决策 Ticket 的共享地图，逐一解决，直至通向目标的路径完全清晰。

## 模型触发 (Model-invoked)

模型可自主调用，用户亦可直接调用（配备丰富的触发短语供模型识别）。

- **[prototype](./prototype/SKILL.md)**：构建一次性抛弃型原型以解答设计疑惑：输出用于验证状态/逻辑的单个可共享 HTML 文件，或多个可切换的 UI 变体。
- **[diagnosing-bugs](./diagnosing-bugs/SKILL.md)**：针对疑难 Bug 与性能退化的严谨排查诊断循环：构建能稳定复现该 Bug 飘红的反馈循环 → 最小化 → 建立假说 → 埋点插桩 → 修复 → 回归测试。
- **[research](./research/SKILL.md)**：针对高信任度的一手来源深入调研某个问题，并将调研成果作为附带引用的 Markdown 文件留存在仓库中，以子 Agent 形式后台运行。
- **[tdd](./tdd/SKILL.md)**：遵循红-绿-重构（Red-Green-Refactor）循环的测试驱动开发。一次构建一个垂直切片的功能或修复 Bug。
- **[domain-modeling](./domain-modeling/SKILL.md)**：主动构建和提炼项目的领域模型，推敲模糊概念、通过场景进行压力测试、就地更新 `CONTEXT.md` 和 ADR。
- **[codebase-design](./codebase-design/SKILL.md)**：用于设计深模块（Deep modules）的通用规范与词汇体系：小接口、清晰缝隙、通过接口可测试。
- **[code-review](./code-review/SKILL.md)**：针对相对于基准点的 Diff 开展双维度审查：**规范维度 (Standards)**（代码是否遵循仓库规范及 Fowler 代码坏味道基准？）与 **规格维度 (Spec)**（代码是否忠实实现了原始 Issue/Spec 的诉求？），由并行子 Agent 独立运行。
- **[resolving-merge-conflicts](./resolving-merge-conflicts/SKILL.md)**：逐块（hunk by hunk）处理正在进行的 Git merge 或 rebase 冲突，追溯双方一手来源的意图进行化解，进而完成合并操作，绝不执行 `--abort`。
- **[wizard](./wizard/SKILL.md)**：生成交互式 Bash 向导脚本，引导人类完成只有人类才能执行的操作：配置基础设施、设置凭据或 CI 密钥、操作陌生的第三方后台、或运行一次性数据迁移/切换。
