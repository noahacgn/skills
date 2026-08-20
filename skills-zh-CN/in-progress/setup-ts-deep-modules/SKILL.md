---
name: setup-ts-deep-modules
description: 在 TypeScript 仓库中配置 dependency-cruiser，使每个 package 均成为深模块，实现隐藏在子目录中，仅允许通过根目录入口文件访问。用户触发。
disable-model-invocation: true
---

# 配置 TypeScript 深模块 (Setup TS Deep Modules)

让本仓库中的每个 package 都成为一个**深模块 (deep module)**：微小的接口背后承载丰富的内部实现。Package 的公开表面积是其**入口文件 (entry points)**：位于 package 根目录下的文件，而其子目录下的所有内容均对外隐藏。本技能安装 [dependency-cruiser](https://github.com/sverweij/dependency-cruiser) 以及强制让入口文件成为唯一外部访问路径的规则，并实证检验规则的有效约束力。

关于词汇体系（深模块、接口、缝隙、深度），调用 Skill 工具并传入 `"codebase-design"`，通篇使用该标准语言。

## 本规范强制推行的目录形态

```
src/packages/
  <name>/
    index.ts        ← 入口文件 (公开). 允许从外部 import.
    client.ts       ← 另一个入口文件. 单个 package 可以暴露多个入口.
    lib/            ← 内部实现: 对外隐藏, 内部文件之间可自由 import.
    tests/          ← 就近放置的测试与 fixtures (子目录, 因而对外部私有).
```

公开表面积是 package 的**根目录文件**：而非仅限于单一指定的 `index.ts`。按约定，内部实现位于 `lib/`，测试位于 `tests/`，从而赋予每个 package 相同的双目录形态。但规则本身是通用的：*任何*子目录中的*任何内容*均属于私有内部实现，因此你永远不需要为了新增目录而扩展配置文件。

四条硬性规则，均为 `error` 级别：

1. **入口文件边界 (Entry-point boundary)**：package 外部的代码（应用代码或其他 package）只能 import 该 package 的入口文件（其根目录文件），绝不能直接 import 其子目录中的任何内容。
2. **Package 内部自由互通 (Intra-package freedom)**：package 自身的内部文件之间可以自由互相 import。
3. **通过入口文件进行测试 (Tests through the entry points)**：`<pkg>/tests/` 下的文件可以 import 任何 package 的公开入口文件及其自身的 `tests/` fixtures，但绝不能 import 任何 package 子目录的内部实现（即使是自己的也不行）。跨 package 的集成测试是完全允许的；但深层直接引用是不允许的。
4. **禁止循环依赖 (No cycles)**：杜绝依赖循环。

**入口文件，而非 Barrel 汇总文件。** 正是因为公开暴露面是*每一个*根目录文件，单个 package 可以暴露多个细粒度的入口点（`index.ts`、`client.ts`、`server.ts`），而非将所有内容强行汇流到单一庞大的 `index.ts` 桶中。强烈不推荐将整个子树盲目重导出的 Barrel 文件，保持入口文件小而精，并将实现细节隐藏在子目录中。

分层依赖控制（哪些 package 可以依赖哪些 package）属于*另一项*关注点，在配置文件中以注释桩形式留给当前代码库后续按需补充。

## 执行步骤

### 1. 探测运行环境

- **包管理器 (Package manager)**：`pnpm-lock.yaml` → pnpm，`yarn.lock` → yarn，`bun.lockb` → bun，其余情况 npm。在下方所有命令中使用对应包管理器（`pnpm`/`yarn`/`npm run`/`bunx`）。
- **Package 根路径**：若存在 `src/` 则使用 `src/packages`，否则使用 `packages`。若仓库已有其他显而易见的约定，与用户确认。
- **既有配置**：检查是否存在 `.dependency-cruiser.*` 文件。若存在，**不要**粗暴覆盖：将四条核心规则和相关选项合并进去，并告知用户你添加了哪些内容。

**完成标准：** 包管理器、packages 根路径及既有配置状态均已明确。

### 2. 安装 dependency-cruiser

使用探测到的包管理器将 `dependency-cruiser` 安装为 devDependency。

**完成标准：** `dependency-cruiser` 成功进入 `devDependencies`。

### 3. 编写配置文件

将 [`dependency-cruiser.config.cjs`](./dependency-cruiser.config.cjs) 复制到仓库根目录并命名为 `.dependency-cruiser.cjs`。将 `PACKAGES_ROOT` 设置为步骤 1 中探测到的路径。规则基于路径深度计算且与文件扩展名无关，因此无需其他额外调整。

**完成标准：** 根目录下生成 `.dependency-cruiser.cjs`，配置了正确的 `PACKAGES_ROOT`，并包含四条禁用违规规则。

### 4. 接入代码检查流程

- 添加 `lint:boundaries` 脚本命令：`depcruise <packages-root>`（或 `depcruise src`）。
- 将其整合进仓库的全局综合检查命令中，即已经包含类型检查的命令（如 `check` / `ci` / `validate` 脚本）。**切勿**修改 `tsconfig` 或添加路径别名。
- 若尚无全局检查脚本，添加 `lint:boundaries` 并提示用户将其纳入 CI 流水线中。

**完成标准：** `lint:boundaries` 成功配置并在执行类型检查的相同命令中作为一部分运行。

### 5. 搭建示例 package 脚手架

在 `<packages-root>/example/` 下创建一个提交到 Git 的模板供参考复用：

- `index.ts`：公开入口文件。导出一个委托给内部文件的函数（使该 package 具备明显的*深模块*特征，而非无用透传）。
- `lib/impl.ts`：**子目录**中的内部实现文件，被 `index.ts` import，无法从外部直接访问。
- `tests/example.test.ts`：**仅** import `../index`（公开入口），并针对公开函数进行断言。

告知用户这是一个可复制或随时删除的初学者模板。

**完成标准：** 示例 package 成功创建，通过根目录入口暴露其行为，并将 `impl` 封装在子目录中。

### 6. 实证检验规则的有效拦截力 (Prove the rules bite)

这是整项技能的核心完成准则，无法对违规行为进行有效拦截报错的配置毫无价值。

1. 运行 `lint:boundaries`。干净的示例 package 必须**通过 (pass)**。
2. 临时向 `tests/example.test.ts` 中添加一条深层私有 import（例如 `import { thing } from "../lib/impl"`）。再次运行 `lint:boundaries`，必须**失败报错 (fail)** 并命中 `tests-through-entrypoints` 规则。
3. 还原该深层 import。再次运行，必须重新**通过 (pass)**。

**完成标准：** 亲眼见证了一次通过、一次因深层 import 导致的失败拦截，以及恢复后的重新通过。若步骤 2 未能拦截报错，说明规则配置有误，必须修复后再收工。

### 7. 记录架构约定

在 packages 根目录下编写 `README.md`（`<packages-root>/README.md`），紧邻其管辖的各个 package，说明：`src/packages/<name>/` 目录布局（根目录为公开入口，`lib/` 存放实现，`tests/` 存放测试）、“仅允许通过 package 的入口文件（根目录文件）进行 import”的原则，以及如何运行 `lint:boundaries`。明确**不推荐 Barrel 文件**：提供多个小巧的入口文件，而非通过单个 index 将整个子树重新导出。保持简短，包含示例模板代码并在段落中解释四条规则。

随后在仓库的 Agent 指南文件中（若存在 `CLAUDE.md` 则在其内，否则在 `AGENTS.md` 中；若两者均不存在则新建 `AGENTS.md`）添加指向它的**上下文指针**。单行即可，例如：`Package 均为深模块设计，在添加或引入 Package 前请查阅 [src/packages/README.md](./src/packages/README.md)。` 这能确保 Agent 在编码时主动发现边界规则，而非触碰报警后才察觉。

**完成标准：** `<packages-root>/README.md` 存在且明确反对 Barrel 模式，且仓库的 `CLAUDE.md`/`AGENTS.md` 包含指向它的超链接。

## 补充备忘

- 配置文件中的 `$1` 反向引用（dependency-cruiser 的分组匹配机制）正是允许 package 访问自身内部实现而阻断外部访问的关键，切勿将其打平成各个 package 的独立规则。
- 公开与私有的区分完全由**目录深度**决定：package 的根目录文件为公开入口；子目录中的任何内容均为私有实现。虽然约定的子目录是 `lib/` 和 `tests/`，但规则并未硬编码子目录名称，任何子目录均自动属于私有，因此新增目录无需修改任何配置。新增入口仅需在根目录下新增文件，无需 Barrel 文件。
- Package 在层级上是**扁平的**：直接位于根目录下的一级子目录。Package 内部可以根据需要自由嵌套任意深度的子目录；但一个 Package 内部不能再嵌套另一个 Package。
- 采用 `.cjs` 扩展名（而非 `.js`），确保配置文件的 `module.exports` 在配置了 `"type": "module"` 的仓库中也能正常工作。
