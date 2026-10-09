---
name: implement
description: "Implement a piece of work based on a spec or set of tickets."
disable-model-invocation: true
---

Implement the work described by the user in the spec or tickets.

If the user passes a ticket reference, fetch it from the issue tracker and state its title before starting. If the reference is ambiguous, ask.

Call the Skill tool with "tdd" where possible, at pre-agreed seams.

Run typechecking regularly, single test files regularly, and the full test suite once at the end.

完成实现后，用 `code-review` 调用 Skill 工具审查本次工作。等待评审完成，处理所有需要处理的问题，并重复评审，直到没有待处理的问题。

Commit your work to the current branch.
