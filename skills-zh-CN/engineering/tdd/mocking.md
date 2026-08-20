# 何时使用 Mock

仅在**系统边界 (System boundaries)** 处进行 Mock：

- 外部三方 API（支付、邮件发送等）
- 数据库（视情况而定，优先使用真实测试数据库）
- 时间与随机数机制
- 文件系统（视情况而定）

不要 Mock：

- 你自己编写的类与模块
- 内部协作者
- 任何处于你控制范围内的实体

## 面向可 Mock 性设计接口

在系统边界处，设计易于 Mock 的接口：

**1. 使用依赖注入**

将外部依赖作为参数传入，而非在内部自行实例化：

```typescript
// 易于 Mock
function processPayment(order, paymentClient) {
  return paymentClient.charge(order.total);
}

// 难以 Mock
function processPayment(order) {
  const client = new StripeClient(process.env.STRIPE_KEY);
  return client.charge(order.total);
}
```

**2. 优先使用 SDK 风格的具名接口，而非通用请求函数**

为每项外部操作提供专属函数，而非使用带有分支条件的单一泛化函数：

```typescript
// 优秀：每个函数均可独立 Mock
const api = {
  getUser: (id) => fetch(`/users/${id}`),
  getOrders: (userId) => fetch(`/users/${userId}/orders`),
  createOrder: (data) => fetch('/orders', { method: 'POST', body: data }),
};

// 糟糕：Mock 时需要在其内部编写条件判断逻辑
const api = {
  fetch: (endpoint, options) => fetch(endpoint, options),
};
```

SDK 风格意味着：
- 每个 Mock 返回单一且确定的数据结构
- 测试脚手架中无需编写复杂的条件分支
- 一眼就能看出测试触碰了哪些端点
- 每个端点享有专属的类型安全性
