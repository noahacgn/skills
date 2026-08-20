# CONTEXT.md 格式规范

## 结构示例

```md
# {上下文名称}

{一到两句话描述该上下文的定义以及它存在的意义。}

## 语言规范 (Language)

**Order (订单)**:
{一到两句话定义该术语}
_避免使用_: Purchase, transaction

**Invoice (发票/账单)**:
在商品交付后发送给客户的付款请求。
_避免使用_: Bill, payment request

**Customer (客户)**:
下达订单的个人或组织实体。
_避免使用_: Client, buyer, account
```

## 编写规则

- **保持鲜明主见。** 当同一概念存在多种词汇时，挑选最优的一个，并将其余表述列入 `_避免使用_` 中。
- **定义保持简短。** 最多一到两句话。定义它“是什么”，而非“它能做什么”。
- **仅收录本项目的专有领域术语。** 通用编程概念（超时时间、错误类型、工具类模式）即使在项目中被频繁使用，也不属于此处。在添加术语前自问：这是属于当前业务上下文独有的概念，还是通用编程概念？唯有前者属于此处。
- **出现自然聚类时按小标题分组。** 若所有术语均属于单一连贯的领域，使用平铺列表即可。

## 单上下文与多上下文代码库

**单上下文（绝大多数代码库）：** 位于仓库根目录下的单个 `CONTEXT.md`。

**多上下文：** 位于仓库根目录下的 `CONTEXT-MAP.md`，其中列出各个上下文、它们所在的路径以及相互之间的协作关系：

```md
# 上下文地图 (Context Map)

## 界限上下文列表 (Contexts)

- [Ordering](./src/ordering/CONTEXT.md)：接收并跟踪客户订单
- [Billing](./src/billing/CONTEXT.md)：生成发票并处理支付流程
- [Fulfillment](./src/fulfillment/CONTEXT.md)：管理仓库分拣与履约发货

## 上下文间关系 (Relationships)

- **Ordering → Fulfillment**: Ordering 发布 `OrderPlaced` 事件；Fulfillment 消费该事件以启动分拣流程
- **Fulfillment → Billing**: Fulfillment 发布 `ShipmentDispatched` 事件；Billing 消费该事件以生成对应发票
- **Ordering ↔ Billing**: 跨上下文共享 `CustomerId` 与 `Money` 基础类型
```

技能自动推断适用的结构：

- 若存在 `CONTEXT-MAP.md`，读取它以定位各个界限上下文
- 若仅存在根目录下的 `CONTEXT.md`，按单上下文处理
- 若两者均不存在，在确定第一个领域术语时按需延迟创建根目录下的 `CONTEXT.md`

当存在多个上下文时，推断当前任务与哪一个上下文相关。若不明确，主动询问。
