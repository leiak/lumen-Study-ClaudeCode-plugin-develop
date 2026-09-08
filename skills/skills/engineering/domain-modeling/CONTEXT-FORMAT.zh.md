# CONTEXT.md 格式

## 结构

```md
# {Context 名称}

{一两句话描述这个 context 是什么以及为什么存在。}

## Language

**Order**:
{对这个术语的一两句描述}
_Avoid_: Purchase, transaction

**Invoice**:
交付后发送给客户的付款请求。
_Avoid_: Bill, payment request

**Customer**:
下达订单的个人或组织。
_Avoid_: Client, buyer, account
```

## 规则

- **要有主张。** 当同一个概念存在多个词时，挑出最好的一个，并将其余的列在 `_Avoid_` 下。
- **保持定义精炼。** 最多一两句话。定义它 IS 是什么，而不是它做什么。
- **只包含该项目 context 特有的术语。** 即使项目大量使用，通用编程概念（超时、错误类型、工具模式）也不属于这里。在添加一个术语之前，问一下：这是该 context 独有的概念，还是通用编程概念？只有前者才属于这里。
- **当自然聚类出现时，将术语按子标题分组。** 如果所有术语都属于同一连贯领域，扁平列表也可以。

## 单 context 与多 context 仓库

**单 context（大多数仓库）：** 仓库根目录下一个 `CONTEXT.md`。

**多 context：** 仓库根目录下一个 `CONTEXT-MAP.md`，列出 context、它们所在的位置以及彼此之间的关系：

```md
# Context Map

## Contexts

- [Ordering](./src/ordering/CONTEXT.md): 接收并跟踪客户订单
- [Billing](./src/billing/CONTEXT.md): 生成发票并处理付款
- [Fulfillment](./src/fulfillment/CONTEXT.md): 管理仓库拣货和发货

## Relationships

- **Ordering → Fulfillment**: Ordering 发出 `OrderPlaced` 事件；Fulfillment 消费它们以开始拣货
- **Fulfillment → Billing**: Fulfillment 发出 `ShipmentDispatched` 事件；Billing 消费它们以生成发票
- **Ordering ↔ Billing**: 共享 `CustomerId` 和 `Money` 的类型
```

skill 会推断适用哪种结构：

- 如果存在 `CONTEXT-MAP.md`，读取它以查找 context
- 如果只有根目录的 `CONTEXT.md`，则是单 context
- 如果两者都不存在，则在第一个术语确定下来时懒创建一个根目录的 `CONTEXT.md`

当存在多个 context 时，推理当前话题与哪个相关。如果不清楚，就问。
