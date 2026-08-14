---
name: handoff
description: 将当前对话浓缩为交接文档，以便另一个 Agent 接手。
argument-hint: "下一次会话将用于什么？"
disable-model-invocation: true
---

编写一份交接文档以总结当前对话，以便全新的 Agent 能够继续开展工作。将其保存至用户操作系统的临时目录——而非当前工作区。

在文档中包含一个“建议技能 (suggested skills)”部分，推荐接手的 Agent 应当调用的技能。

不要重复已在其他产物（规格说明、计划、ADR、Issue、Commit、Diff）中记录的内容。请通过路径或 URL 进行引用。

对任何敏感信息进行脱敏处理，例如 API 密钥、密码或个人身份信息。

如果用户传递了参数，将其作为下一次会话重点关注内容的描述，并相应定制该交接文档。
