---
name: claude-handoff
description: 将当前对话交接给一个全新的后台 Agent，以便其立即接续开展工作。
argument-hint: "下一次会话将用于什么？"
disable-model-invocation: true
---

编写一份当前对话的交接摘要，以便全新 Agent 能够继续开展工作。无需将其保存在本地磁盘，而是直接启动一个以后台模式运行的 Agent，并将该摘要作为其 Prompt 启动种子：`claude --bg --name "<描述性名称>" "<交接摘要>"`。它在当前工作目录下启动并立即返回；用户可通过 `claude agents` 对其进行管理。

始终通过 `-n`/`--name` 传入描述性名称（例如 `--name "修复登录 Bug"`）——它会设定在任务列表、会话选择器与终端标题中展示的名称。

在摘要中包含一个“建议技能 (suggested skills)”小节，列明下一位 Agent 应分别为哪些技能调用 Skill 工具。

不要重复已在其他产物（规格说明、计划、ADR、Issue、Commit、Diff）中记录的内容。请通过路径或 URL 进行引用。

对任何敏感信息（如 API 密钥、密码或个人身份信息）进行脱敏处理——该摘要将直接作为新 Agent 的 Prompt。

若用户传入了参数，将其作为下一次会话重点关注内容的描述，并相应定制该摘要。
