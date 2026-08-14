---
name: migrate-to-shoehorn
description: 将测试文件中的 `as` 类型断言迁移替换为 @total-typescript/shoehorn。当用户提及 shoehorn、希望替换测试中的 `as`、或需要构造部分测试数据时使用。
---

# 迁移至 Shoehorn (Migrate to Shoehorn)

## 为什么选择 Shoehorn？

`shoehorn` 允许你在测试中传递部分（Partial）数据，同时让 TypeScript 保持类型满意。它使用类型安全的方式替代了粗暴的 `as` 类型断言。

**仅用于测试代码。** 绝不要在生产代码中使用 shoehorn。

在测试中使用 `as` 存在的问题：

- 团队规范通常禁止滥用 `as`
- 必须手动指定冗长的目标类型
- 针对刻意构造的错误数据需要双重断言（`as unknown as Type`）

## 安装依赖

```bash
npm i @total-typescript/shoehorn
```

## 迁移模式

### 仅需少量属性的大型对象

迁移前：

```ts
type Request = {
  body: { id: string };
  headers: Record<string, string>;
  cookies: Record<string, string>;
  // ...以及其他 20 多个属性
};

it("gets user by id", () => {
  // 仅关心 body.id 但不得不伪造整个 Request 的所有字段
  getUser({
    body: { id: "123" },
    headers: {},
    cookies: {},
    // ...伪造全部 20 多个字段
  });
});
```

迁移后：

```ts
import { fromPartial } from "@total-typescript/shoehorn";

it("gets user by id", () => {
  getUser(
    fromPartial({
      body: { id: "123" },
    }),
  );
});
```

### `as Type` → `fromPartial()`

迁移前：

```ts
getUser({ body: { id: "123" } } as Request);
```

迁移后：

```ts
import { fromPartial } from "@total-typescript/shoehorn";

getUser(fromPartial({ body: { id: "123" } }));
```

### `as unknown as Type` → `fromAny()`

迁移前：

```ts
getUser({ body: { id: 123 } } as unknown as Request); // 刻意构造的错误类型
```

迁移后：

```ts
import { fromAny } from "@total-typescript/shoehorn";

getUser(fromAny({ body: { id: 123 } }));
```

## 各函数选用指南

| 函数名称 | 适用场景 |
| -------- | -------- |
| `fromPartial()` | 传递部分属性数据，同时保留既有字段的类型检查 |
| `fromAny()` | 传递刻意构造的错误数据（保留 IDE 自动补全能力） |
| `fromExact()` | 强制要求完整对象（后续可无缝切换为 fromPartial） |

## 工作流

1. **收集需求** - 询问用户：
   - 哪些测试文件中的 `as` 断言引发了维护困扰？
   - 是否涉及只关心少数属性的大型复杂对象？
   - 是否需要为了错误测试而传递刻意构造的非法数据？

2. **安装与迁移**：
   - [ ] 安装依赖：`npm i @total-typescript/shoehorn`
   - [ ] 查找带有 `as` 断言的测试文件：`grep -r " as [A-Z]" --include="*.test.ts" --include="*.spec.ts"`
   - [ ] 将 `as Type` 替换为 `fromPartial()`
   - [ ] 将 `as unknown as Type` 替换为 `fromAny()`
   - [ ] 添加来自 `@total-typescript/shoehorn` 的 import 引入
   - [ ] 运行类型检查以验证通过
