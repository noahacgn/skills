---
name: scaffold-exercises
description: 自动创建包含章节、练习题、参考解答与概念讲解且能通过 Lint 检查的练习目录结构。当用户希望搭建练习脚手架、创建练习桩代码或设置全新课程章节时使用。
---

# 练习题脚手架 (Scaffold Exercises)

创建能够顺利通过 `pnpm ai-hero-cli internal lint` 检查的练习题目录结构，随后通过 `git commit` 提交。

## 目录命名规范

- **章节 (Sections)**: `exercises/` 下的 `XX-section-name/`（例如 `01-retrieval-skill-building`）
- **练习题 (Exercises)**: 章节下的 `XX.YY-exercise-name/`（例如 `01.03-retrieval-with-bm25`）
- 章节编号 = `XX`，练习题编号 = `XX.YY`
- 命名采用 dash-case（全小写，连字符连接）

## 练习题变体子目录 (Exercise variants)

每个练习题至少需要包含以下子目录之一：

- `problem/` - 包含待完成 TODO 的学生工作区
- `solution/` - 官方参考实现
- `explainer/` - 概念讲解资料，不包含 TODO

在生成桩代码时，除非计划另有说明，默认采用 `explainer/`。

## 必需的文件

每个子目录（`problem/`、`solution/`、`explainer/`）均需要一个 `readme.md` 文件，满足：

- **非空**（必须包含实际内容，即使只有一行标题也可）
- 无任何死链/断链

在生成桩代码时，创建一个包含标题和简短说明的基础 readme：

```md
# 练习标题

在此处添加说明描述
```

若子目录包含代码，还需要一个 `main.ts`（行数 > 1）。但对于纯桩代码阶段，仅包含 readme 的练习是完全合法的。

## 执行流程

1. **解析规划** - 提取章节名称、练习题名称以及变体类型
2. **创建目录** - 针对每个路径执行 `mkdir -p`
3. **创建桩 readme** - 为每个变体目录生成一个带有标题的 `readme.md`
4. **运行 Lint 检查** - 运行 `pnpm ai-hero-cli internal lint` 进行校验
5. **修复报错** - 持续迭代直到 Lint 检查全部通过

## Lint 校验规则摘要

校验工具（`pnpm ai-hero-cli internal lint`）负责检查：

- 每个练习均包含子目录（`problem/`、`solution/`、`explainer/`）
- 至少存在 `problem/`、`explainer/` 或 `explainer.1/` 之一
- 主子目录下的 `readme.md` 存在且非空
- 无任何 `.gitkeep` 文件
- 无任何 `speaker-notes.md` 文件
- readme 中无失效死链
- readme 中不包含 `pnpm run exercise` 命令
- 每个子目录需包含 `main.ts`（除非该练习为纯 readme 模式）

## 移动 / 重命名练习题

在重新编号或移动练习题时：

1. 使用 `git mv`（而非普通 `mv`）重命名目录 - 以保留 Git 提交历史
2. 更新数字前缀以维护正确次序
3. 移动完成后重新运行 Lint 校验

示例：

```bash
git mv exercises/01-retrieval/01.03-embeddings exercises/01-retrieval/01.04-embeddings
```

## 实例：根据大纲规划生成桩代码

给定如下大纲计划：

```
Section 05: Memory Skill Building
- 05.01 Introduction to Memory
- 05.02 Short-term Memory (explainer + problem + solution)
- 05.03 Long-term Memory
```

创建目录：

```bash
mkdir -p exercises/05-memory-skill-building/05.01-introduction-to-memory/explainer
mkdir -p exercises/05-memory-skill-building/05.02-short-term-memory/{explainer,problem,solution}
mkdir -p exercises/05-memory-skill-building/05.03-long-term-memory/explainer
```

随后创建 readme 桩文件：

```
exercises/05-memory-skill-building/05.01-introduction-to-memory/explainer/readme.md -> "# Introduction to Memory"
exercises/05-memory-skill-building/05.02-short-term-memory/explainer/readme.md -> "# Short-term Memory"
exercises/05-memory-skill-building/05.02-short-term-memory/problem/readme.md -> "# Short-term Memory"
exercises/05-memory-skill-building/05.02-short-term-memory/solution/readme.md -> "# Short-term Memory"
exercises/05-memory-skill-building/05.03-long-term-memory/explainer/readme.md -> "# Long-term Memory"
```
