# Deepening

给定一组浅层模块及其依赖，如何安全地加深它们。假定 [SKILL.md](SKILL.md) 中的词汇：**module**、**interface**、**seam**、**adapter**。

## 依赖类别

在评估加深候选时，对其依赖进行分类。类别决定了加深后的模块如何跨其 seam 进行测试。

### 1. In-process

纯计算、内存状态，没有 I/O。始终可加深：合并模块并直接通过新接口测试。无需 adapter。

### 2. Local-substitutable

有本地测试替身的依赖（PGLite 替代 Postgres、内存文件系统）。如果替身存在就可加深。加深后的模块在测试套件中以替身运行。seam 是内部的；模块的外部接口处没有 port。

### 3. Remote but owned（Ports & Adapters）

跨网络边界的你自己的服务（微服务、内部 API）。在 seam 处定义一个 **port**（接口）。deep module 拥有逻辑；传输作为 **adapter** 注入。测试使用 in-memory adapter。生产使用 HTTP / gRPC / 队列 adapter。

建议形态：*"在 seam 处定义一个 port，为生产实现一个 HTTP adapter，为测试实现一个 in-memory adapter，这样逻辑集中在一个 deep module 中，尽管它被部署跨网络。"*

### 4. True external（Mock）

你无法控制的第三方服务（Stripe、Twilio 等）。加深后的模块将外部依赖作为注入的 port；测试提供 mock adapter。

## Seam 纪律

- **一个 adapter 意味着假设的 seam。两个 adapter 意味着真实的 seam。** 除非至少两个 adapter 是合理的（通常是生产 + 测试），否则不要引入 port。单个 adapter 的 seam 仅仅是间接化。
- **内部 seam 与外部 seam。** deep module 可以有内部 seam（对其实现私有，被其自己的测试使用）以及其接口处的外部 seam。不要仅仅因为测试使用它们，就把内部 seam 暴露到接口上。

## 测试策略：替换而非叠加

- 一旦加深模块的接口处存在测试，原浅层模块上的旧单元测试就成了废品；删除它们。
- 在加深模块的接口处编写新测试。**接口就是测试面。**
- 测试通过接口对可观察的结果进行断言，而不是内部状态。
- 测试应该在内部重构中存活，因为它们描述的是行为，而不是实现。如果一个测试在实现变化时不得不改变，那它就穿越了接口。
