---
name: setup-pre-commit
description: 在当前仓库中配置集成了 lint-staged (Prettier)、类型检查与测试套件的 Husky pre-commit 钩子。当用户希望添加 pre-commit 钩子、配置 Husky、配置 lint-staged、或添加提交期格式化/类型检查/测试时使用。
---

# 配置 Pre-Commit 钩子 (Setup Pre-Commit Hooks)

## 本配置所搭建的内容

- **Husky** pre-commit 钩子
- **lint-staged** 对所有暂存区（Staged）文件执行 Prettier
- **Prettier** 配置文件（若缺失）
- 在 pre-commit 钩子中执行 **typecheck** 和 **test** 脚本

## 执行步骤

### 1. 探测包管理器

检查 `package-lock.json` (npm)、`pnpm-lock.yaml` (pnpm)、`yarn.lock` (yarn)、`bun.lockb` (bun)。使用已存在的那一个。若不明确则默认使用 npm。

### 2. 安装依赖包

作为 devDependencies 安装：

```
husky lint-staged prettier
```

### 3. 初始化 Husky

```bash
npx husky init
```

这将创建 `.husky/` 目录并在 package.json 中添加 `prepare: "husky"`。

### 4. 创建 `.husky/pre-commit`

写入该文件（Husky v9+ 无需 Shebang）：

```
npx lint-staged
npm run typecheck
npm run test
```

**适配调整**：将 `npm` 替换为实际探测到的包管理器。若仓库的 package.json 中没有 `typecheck` 或 `test` 脚本，省略对应行并告知用户。

### 5. 创建 `.lintstagedrc`

```json
{
  "*": "prettier --ignore-unknown --write"
}
```

### 6. 创建 `.prettierrc`（若缺失）

仅在尚无 Prettier 配置时创建。使用以下默认配置：

```json
{
  "useTabs": false,
  "tabWidth": 2,
  "printWidth": 80,
  "singleQuote": false,
  "trailingComma": "es5",
  "semi": true,
  "arrowParens": "always"
}
```

### 7. 验证生效

- [ ] `.husky/pre-commit` 存在且具备可执行权限
- [ ] `.lintstagedrc` 存在
- [ ] package.json 中的 `prepare` 脚本为 `"husky"`
- [ ] `prettier` 配置文件存在
- [ ] 运行 `npx lint-staged` 验证其能正常工作

### 8. 提交代码

暂存所有变更/新建文件并以如下信息提交：`Add pre-commit hooks (husky + lint-staged + prettier)`

这将触发刚配置的 pre-commit 钩子，正好作为一次验证一切正常运行的冒烟测试。

## 补充备忘

- Husky v9+ 的钩子文件无需包含 Shebang
- `prettier --ignore-unknown` 会自动跳过 Prettier 无法解析的文件（如图片等）
- pre-commit 会优先运行 lint-staged（快速，仅针对暂存文件），随后执行全量类型检查和测试套件
