---
name: codebase-design
description: 设计 deep module 的共享词汇。在用户希望设计或改进一个模块的接口、寻找加深机会、决定 seam 放在哪里、使代码更易测试或更便于 AI 导航，或其他 skill 需要 deep-module 词汇时使用。
---

# 代码库设计

设计 **deep module**：在小接口背后承载大量行为，放在清晰的 seam 处，可通过该接口测试。在设计或重构代码的任何地方都使用这种语言和这些原则。目标是为调用方提供 leverage，为维护者提供 locality，为所有人提供可测试性。

## 词汇表

请精确使用这些术语：不要替换为 "component"、"service"、"API" 或 "boundary"。一致的语言才是全部意义所在。

**Module**：任何具有接口和实现的东西。刻意保持规模无关：函数、类、包、跨层切片。_Avoid_：unit、component、service。

**Interface**：调用方正确使用模块所需知道的一切：类型签名，也包括不变量、顺序约束、错误模式、必需的配置和性能特征。_Avoid_：API、signature（过于狭窄，它们只指类型层面的表面）。

**Implementation**：模块内部的内容，即其代码主体。不同于 **Adapter**：一个东西可以是带大实现的小 adapter（Postgres repo），也可以是带小实现的大 adapter（in-memory fake）。当 seam 是话题时用 "adapter"，否则用 "implementation"。

**Depth**：接口处的 leverage。调用方（或测试）每学习一单位接口所能驱动行为的数量。当大量行为位于小接口背后时，模块是 **deep**；当接口几乎和实现一样复杂时，模块是 **shallow**。

**Seam** _(Michael Feathers)_：可以在不修改该位置的情况下改变行为的地方；模块接口所处的*位置*。seam 放在哪里本身就是一项设计决策，与它背后是什么无关。_Avoid_：boundary（与 DDD 的 bounded context 重载）。

**Adapter**：在 seam 处满足接口的具体事物。描述的是*角色*（它填补哪个槽位），而不是实质（里面是什么）。

**Leverage**：调用方从 depth 中获得的东西。每学习一单位接口，获得更多能力。一个实现在 N 个调用点和 M 个测试中得到回报。

**Locality**：维护者从 depth 中获得的东西。变更、bug、知识和验证集中在一处，而不是分散在调用方之间。修一次，处处修好。

## Deep 与 Shallow

**Deep module** = 小接口 + 多实现：

```
┌─────────────────────┐
│   Small Interface   │  ← Few methods, simple params
├─────────────────────┤
│                     │
│  Deep Implementation│  ← Complex logic hidden
│                     │
└─────────────────────┘
```

**Shallow module** = 大接口 + 少实现（避免）：

```
┌─────────────────────────────────┐
│       Large Interface           │  ← Many methods, complex params
├─────────────────────────────────┤
│  Thin Implementation            │  ← Just passes through
└─────────────────────────────────┘
```

在设计接口时，问问自己：

- 我能减少方法的数量吗？
- 我能简化参数吗？
- 我能把更多复杂度藏在里面吗？

## 原则

- **Depth 是接口的属性，而不是实现的属性。** 一个 deep module 内部可以由小的、可 mock、可替换的部分组成；它们只是不在接口中。一个模块可以有**内部 seam**（对其实现私有，被其自己的测试使用）以及接口处的**外部 seam**。
- **删除测试。** 想象删除该模块。如果复杂度消失了，它就是一个 pass-through。如果复杂度重新出现在 N 个调用方中，它就物有所值。
- **接口就是测试面。** 调用方和测试穿越同一个 seam。如果你想测试*穿过*接口，那么模块的形状可能就是错的。
- **一个 adapter 意味着假设的 seam。两个 adapter 意味着真实的 seam。** 除非确实有东西在 seam 两侧变化，否则不要引入 seam。

## 为可测试性而设计

好的接口让测试变得自然：

1. **接受依赖，不要创建它们。**

   ```typescript
   // 可测试
   function processOrder(order, paymentGateway) {}

   // 难以测试
   function processOrder(order) {
     const gateway = new StripeGateway();
   }
   ```

2. **返回结果，不要产生副作用。**

   ```typescript
   // 可测试
   function calculateDiscount(cart): Discount {}

   // 难以测试
   function applyDiscount(cart): void {
     cart.total -= discount;
   }
   ```

3. **小表面积。** 更少的方法 = 更少的测试。更少的参数 = 更简单的测试设置。

## 关系

- 一个 **Module** 恰好有一个 **Interface**（它向调用方和测试呈现的表面）。
- **Depth** 是 **Module** 的一个属性，针对其 **Interface** 度量。
- 一个 **Seam** 是 **Module** 的 **Interface** 所在的位置。
- 一个 **Adapter** 处于一个 **Seam** 之上并满足 **Interface**。
- **Depth** 为调用方产生 **Leverage**，为维护者产生 **Locality**。

## 否定的表述

- **把 depth 当作"实现行数 / 接口行数"比率**（Ousterhout）：会奖励给实现凑行数。我们使用 depth-as-leverage。
- **把 "Interface" 当作 TypeScript 的 `interface` 关键字或类的公共方法**：过于狭窄：这里的 interface 包括调用方必须知道的每个事实。
- **"Boundary"**：与 DDD 的 bounded context 重载。说 **seam** 或 **interface**。

## 深入

- **给定依赖对一组模块加深**，参见 [DEEPENING.md](DEEPENING.md)：依赖类别、seam 纪律、以及替换而非叠加的测试。
- **探索替代接口**，参见 [DESIGN-IT-TWICE.md](DESIGN-IT-TWICE.md)：启动并行 sub-agent，以几种截然不同的方式设计接口，然后比较 depth、locality 和 seam 位置。
