# Design It Twice

当用户希望为所选加深候选探索替代接口时，使用这种并行 sub-agent 模式。基于 "Design It Twice"（Ousterhout）：你的第一个想法几乎不是最好的。

使用 [SKILL.md](SKILL.md) 中的词汇：**module**、**interface**、**seam**、**adapter**、**leverage**。

## 流程

### 1. 框定问题空间

在派生 sub-agent 之前，先为所选候选写一份面向用户的问题空间说明：

- 任何新接口需要满足的约束
- 它将依赖的依赖，以及它们分别属于哪个类别（参见 [DEEPENING.md](DEEPENING.md)）
- 一段粗略的示例代码草图，把约束具体化，而不是提案，只是让约束具体化的方式

向用户展示这个，然后立即进入步骤 2。用户在 sub-agent 并行工作的同时阅读和思考。

### 2. 派生 sub-agent

并行派生 3 个或更多 sub-agent。每个都必须为加深后的模块产出一个**截然不同**的接口。

用一份独立的技术简报（文件路径、耦合细节、来自 [DEEPENING.md](DEEPENING.md) 的依赖类别、seam 背后的内容）来提示每个 sub-agent。简报独立于步骤 1 中面向用户的问题空间说明。给每个 agent 一个不同的设计约束：

- Agent 1："最小化接口：目标最多 1–3 个入口点。每个入口点最大化 leverage。"
- Agent 2："最大化灵活性：支持许多用例和扩展。"
- Agent 3："为最常见的调用方优化：让默认情况变得平凡。"
- Agent 4（如果适用）："围绕 ports & adapters 设计跨 seam 依赖。"

简报中同时包含 [SKILL.md](SKILL.md) 的词汇和 CONTEXT.md 的词汇，让每个 sub-agent 用与架构语言和项目领域语言一致的方式命名事物。

每个 sub-agent 产出：

1. 接口（类型、方法、参数，加上不变量、顺序、错误模式）
2. 展示调用方如何使用它的使用示例
3. 实现在 seam 背后隐藏了什么
4. 依赖策略和 adapters（参见 [DEEPENING.md](DEEPENING.md)）
5. 权衡：leverage 高的地方、leverage 薄的地方

### 3. 展示与比较

依次展示设计，让用户能逐一理解，然后用散文形式比较它们。按 **depth**（接口处的 leverage）、**locality**（变更集中之处）和 **seam 位置** 进行对比。

比较之后，给出你自己的建议：你认为哪个设计最强，为什么。如果不同设计的元素可以很好地结合，提出一个混合方案。要有主张：用户想要的是明确判断，而不是一份菜单。
