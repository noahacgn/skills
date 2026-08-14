---
name: implement
description: 基于规格说明（Spec）或一组 Ticket 开展具体编码实现。
disable-model-invocation: true
---

根据用户在 Spec 或 Ticket 中描述的要求实现代码。

尽可能在预先约定的代码缝隙处结合 `/tdd` 进行测试驱动开发。

定期执行类型检查，频繁运行单个测试文件，并在最终收尾时完整执行一次全量测试套件。

编码完成后，使用 `/code-review` 审查本次变更。

将你的工作成果提交（Commit）到当前分支。
