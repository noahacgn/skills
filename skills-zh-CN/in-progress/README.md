# 进行中技能 (In Progress)

Beta 测试版。这些技能特意对外公开，欢迎试用并向我反馈踩坑之处。在毕业晋升至稳定分类之前，它们不包含在插件和根目录的 README 中，没有独立的文档页面，并且可能在没有事先通知的情况下发生修改或废弃。

插件不会默认提供这些技能。可通过命令直接单独安装：

```bash
npx skills@latest add mattpocock/skills --skill=<name>
```

- **[loop-me](./loop-me/SKILL.md)**：在当前工作区跨多个会话对自己进行深度盘问，以产出可落地的自动化工作流规格说明。用户触发。
- **[writing-beats](./writing-beats/SKILL.md)**：将文章打造成由节拍（Beats）驱动的行进旅程，类似互动式冒险游戏风格。挑选一个起始节拍，仅编写该节拍，随后转向下一个，直至文章自然完结。
- **[writing-fragments](./writing-fragments/SKILL.md)**：深度访谈会话，旨在挖掘你的碎片想法（各类异构的灵感素材），并将其追加到单个文档中作为未来文章的原材料。
- **[writing-shape](./writing-shape/SKILL.md)**：读取包含原材料的 Markdown 文件，逐段将其打磨成型为正式文章，并在每一步针对排版格式选型进行充分权衡探讨。
- **[claude-handoff](./claude-handoff/SKILL.md)**：将当前对话交接给一个全新的后台 Agent 并立即接续开展工作，通过 `claude --bg` 注入交接摘要作为启动种子。用户触发。
- **[setup-ts-deep-modules](./setup-ts-deep-modules/SKILL.md)**：在 TypeScript 仓库中配置 dependency-cruiser，使每个 package 均成为深模块，实现隐藏在子目录中，仅允许通过其根目录入口文件访问，测试用例亦通过入口文件开展验证。用户触发。
