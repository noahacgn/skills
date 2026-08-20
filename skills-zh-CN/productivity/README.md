# 生产力 (Productivity)

通用工作流工具，非代码特定。

## 用户触发 (User-invoked)

仅在用户手动输入时触发（Claude Code 中配置 `disable-model-invocation: true`；Codex 中在 `agents/openai.yaml` 中配置 `policy.allow_implicit_invocation: false`）。

- **[grill-me](./grill-me/SKILL.md)**：针对计划或设计接受深度拷问，逐一解决决策树上的每一个分支。
- **[handoff](./handoff/SKILL.md)**：将当前对话浓缩为交接文档，以便另一个 Agent 继续开展工作。
- **[teach](./teach/SKILL.md)**：在当前工作区跨会话向用户传授新技能或概念，将当前目录作为带状态的教学工作区。
- **[to-questionnaire](./to-questionnaire/SKILL.md)**：将无法独立回答的决策转化为 Markdown 问卷，提供给能够解答的单个人选，支持异步填写或在会议中共同完成。
- **[wait-what](./wait-what/SKILL.md)**：当消息未能被有效理解时立即触发。Agent 将补充缺失的上下文，以浅显通俗的语言结合 `CONTEXT.md` 中的通用词汇重新阐述。

## 模型触发 (Model-invoked)

模型可自主调用，用户亦可直接调用（配备丰富的触发短语供模型识别）。

- **[grilling](./grilling/SKILL.md)**：针对计划、决策或构想对用户进行深度盘问，直至决策树的每个分支都得到明确解决。
- **[writing-for-agents](./writing-for-agents/SKILL.md)**：为 Agent 编写文档指南：涵盖技能（skills）、AGENTS.md/CLAUDE.md 以及通过指针索引的任何文档。
