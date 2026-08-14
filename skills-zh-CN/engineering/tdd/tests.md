# 优质与糟糕的测试用例对比

## 优质测试 (Good Tests)

**集成风格**：通过真实公开接口开展测试，而非 Mock 内部私有组件。

```typescript
// 优秀：测试可观察的外部行为
test("user can checkout with valid cart", async () => {
  const cart = createCart();
  cart.add(product);
  const result = await checkout(cart, paymentMethod);
  expect(result.status).toBe("confirmed");
  expect(result.status).toBe("confirmed");
});
```

核心特征：

- 测试用户或调用方真正关心的行为
- 严格使用公开 API
- 在内部代码重构中依然保持有效
- 描述“做什么 (WHAT)”，而非“怎么做 (HOW)”
- 每个测试包含单一逻辑维度的断言

## 糟糕测试 (Bad Tests)

**实现细节测试**：与代码内部结构强耦合。

```typescript
// 糟糕：测试实现细节
test("checkout calls paymentService.process", async () => {
  const mockPayment = jest.mock(paymentService);
  await checkout(cart, payment);
  expect(mockPayment.process).toHaveBeenCalledWith(cart.total);
});
```

危险警示信号：

- Mock 内部协作者
- 测试私有方法
- 断言调用次数与调用顺序
- 在业务行为未发生改变的纯重构中测试崩溃
- 测试名称描述“怎么做 (HOW)”而非“做什么 (WHAT)”
- 绕开公开接口直接通过外部手段进行校验

```typescript
// 糟糕：绕过公开接口直接查库验证
test("createUser saves to database", async () => {
  await createUser({ name: "Alice" });
  const row = await db.query("SELECT * FROM users WHERE name = ?", ["Alice"]);
  expect(row).toBeDefined();
});

// 优秀：通过公开接口进行业务验证
test("createUser makes user retrievable", async () => {
  const user = await createUser({ name: "Alice" });
  const retrieved = await getUser(user.id);
  expect(retrieved.name).toBe("Alice");
});
```

**重言式测试 (Tautological tests)**：期望值直接复刻实现的计算逻辑，导致测试在构造上必然通过。

```typescript
// 糟糕：期望值的计算逻辑直接照抄了被测代码的实现
test("calculateTotal sums line items", () => {
  const items = [{ price: 10 }, { price: 5 }];
  const expected = items.reduce((sum, i) => sum + i.price, 0);
  expect(calculateTotal(items)).toBe(expected);
});

// 优秀：期望值是一个独立的、已知的字面常量
test("calculateTotal sums line items", () => {
  expect(calculateTotal([{ price: 10 }, { price: 5 }])).toBe(15);
});
```
