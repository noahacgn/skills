---
name: implement
description: 基于规格说明（Spec）或一组 Ticket 开展具体编码实现。
disable-model-invocation: true
---

根据用户在 Spec 或 Ticket 中描述的要求实现代码。

如果用户传入 Ticket 引用，先从 Issue 追踪系统获取该 Ticket，并在开始实现前说明其标题。若引用存在歧义，向用户确认。

尽可能在预先约定的代码缝隙处调用 Skill 工具并传入 `"tdd"`。

定期执行类型检查，频繁运行单个测试文件，并在最终收尾时完整执行一次全量测试套件。

编码完成后，调用 Skill 工具并传入 `"code-review"`（`../code-review/SKILL.md`）审查本次变更。等待审查完成，处理所有可执行的问题，并重复审查与修复，直到不再有待处理问题。

将你的工作成果提交（Commit）到当前分支。
