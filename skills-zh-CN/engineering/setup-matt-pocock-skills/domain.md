# 业务领域文档消费规则 (Domain Docs)

工程技能在探索代码库时应当如何查阅与消费本仓库的业务领域文档。

## 探索代码库前必读

- 仓库根目录下的 **`CONTEXT.md`**，或
- 根目录下的 **`CONTEXT-MAP.md`**（若存在）——它索引了每个界限上下文对应的 `CONTEXT.md`。阅读与当前任务相关的每一个。
- **`docs/adr/`** — 阅读与你即将开展工作的区域相关的 ADR。在多上下文仓库中，同时检查 `src/<context>/docs/adr/` 下的上下文专有决策。

若上述文件不存在，**静默推进**。切勿大惊小怪标示其缺失；切勿预先建议创建它们。`/domain-modeling` 技能（通过 `/grill-with-docs` 和 `/improve-codebase-architecture` 触发）会在术语或决策真正拍板时按需延迟创建它们。

## 文件组织结构

单上下文仓库（绝大多数仓库）：

```
/
├── CONTEXT.md
├── docs/adr/
│   ├── 0001-event-sourced-orders.md
│   └── 0002-postgres-for-write-model.md
└── src/
```

多上下文仓库（根目录下存在 `CONTEXT-MAP.md`）：

```
/
├── CONTEXT-MAP.md
├── docs/adr/                          ← 系统级全局决策
└── src/
    ├── ordering/
    │   ├── CONTEXT.md
    │   └── docs/adr/                  ← 订单上下文专有决策
    └── billing/
        ├── CONTEXT.md
        └── docs/adr/
```

## 使用术语表中的标准语言

当你的输出提及某个领域概念时（在 Issue 标题、重构建议、假说、测试名称中），严格使用 `CONTEXT.md` 中定义的术语。切勿滑向术语表明确标注避免使用的同义词。

若你所需的概念尚未被收录在术语表中，这是一个信号——要么你在自创项目不使用的生僻词汇（重新考量），要么领域模型确实存在空白（记录下来留待 `/domain-modeling` 补充）。

## 明确标出 ADR 冲突

若你的输出与现有 ADR 相悖，明确抛出而非静默覆盖：

> _与 ADR-0007 (事件溯源订单) 相冲突——但值得重新审视，因为……_
