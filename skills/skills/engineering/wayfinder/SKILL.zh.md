---
name: wayfinder
description: 将一项大型工作（超过一个 agent 会话所能容纳的）规划为仓库 issue tracker 上的决策 ticket 共享地图，并一次一个地解决它们，直到到达目的地的道路清晰为止。
disable-model-invocation: true
---

一个松散的想法已经到来，对一个 agent 会话来说太大，并被迷雾包裹：从这里到**目的地**的路还看不见。Wayfinding 关心的是找到这条路，而不是冲向目的地。这个 skill 将这条路绘制为仓库 issue tracker 上的一份**共享地图**，然后逐个处理它的**决策 ticket**（其解决方式是一个决策，而非待执行的工作切片），直到路线清晰。

目的地因工作量而异，命名它是绘制的第一个行为：它塑造了每个 ticket。它可能是一份要交付并迭代的 spec、一个在规划开始前要锁定的决策，或一项就地进行的变更（例如数据结构迁移）。地图是领域无关的：工程工作、课程内容、任何适合该形状的东西。

## 计划，而非执行

Wayfinder 默认是**规划**：每个 ticket 解决一个决策，当路线清晰、且在有人去做那件事之前再没有要决定的事情时，地图就完成了。直接去做工作的冲动通常是你已经达到地图边缘、是时候交接的信号。一个工作量可以在其**Notes**中覆盖这一点，将执行带入地图本身，但如果没有，就产出决策，而不是可交付成果。

## 按名称引用

每张地图和 ticket 就是一个 issue，所以它有一个**名称**：它的标题。在人类阅读的所有内容（叙述、地图的 Decisions-so-far）中，按该名称引用它，绝不要用裸露的 id、数字或 slug。一墙的 `#42, #43, #44` 难以辨认；名字一眼就能读懂。id 和 URL 不会消失；一个名字包裹它的链接，但它们*内嵌于*名字之内，绝不取代它。

## 地图

地图是该仓库 issue tracker 上的一个 issue，标签为 `wayfinder:map`，即权威工件。它的 ticket 是地图的子 issue。

地图是一个**索引**，而不是存储。它列出已做出的决策并指向保存其细节的 ticket；一个决策只存在于一个地方，即它的 ticket，所以地图从不复述它，只概述它并链接。

**地图、它的子 ticket、blocking 和 frontier 查询在物理上位于何处取决于 tracker。** issue tracker 应该已经被提供给你。如果没有，告诉用户运行 `/setup-matt-pocock-skills`。查阅 tracker 文档中的 "Wayfinding operations" 部分，了解*该*仓库如何表达它们。如果没有提供 tracker，则默认为 local-markdown tracker。

### 地图主体

整张地图以低分辨率加载，每个会话一次。未结 ticket **不**被列出：它们是未结的子 issue，通过查询发现。

```markdown
## Destination

<到达此地图尽头看起来像什么：该 spec、决策或此工作量正在找到的变更。一两行；每个会话在选择 ticket 之前都朝向它定向。>

## Notes

<领域；每个会话应查阅的 skill；该工作量的常驻偏好>

## Decisions so far

<!-- 索引：每个已关闭 ticket 一行，足够判断相关性，然后放大链接以获取 ticket 保存的细节 -->

- [<已关闭 ticket 标题>](链接): <答案的一行概述>

## Not yet specified

<!-- 参见 "Fog of war"：范围内但你还无法 ticket 的雾；随着 frontier 推进而毕业 -->

## Out of scope

<!-- 参见 "Out of scope"：超出目的地被排除的工作；已关闭，永不毕业 -->
```

### Tickets

每个 ticket 是地图的一个**子 issue**；tracker 的 issue id 是其身份。其主体是问题，大小适合一个 100K token 的 agent 会话：

```markdown
## Question

<此 ticket 解决的决策或调查>
```

每个 ticket 带有一个 `wayfinder:<type>` 标签，取值为 `research`、`prototype`、`grilling`、`task` 之一（参见 [Ticket Types](#ticket-types)）。

会话通过将 ticket 分派给驱动地图的开发者来**认领**它，**首先**，在任何工作之前，这样并发的会话就会跳过它。该受让人*就是*认领：一个未结、未分派的 ticket 是未被认领的。

Blocking 使用 tracker 的**原生**依赖关系：这很关键，因为它在 tracker 自己的 UI 中**直观地**渲染 frontier，因此人类无需打开地图就能看到可领取的内容。只有缺少原生 blocking 的 tracker 才会退回到正文约定。一个 ticket **解除阻塞** 当阻塞它的每个 ticket 都关闭时；**frontier** 是未结、未阻塞、未被认领的子项，即已知世界的边缘。

答案不是正文的一部分；它在解决时记录（参见 [Work through the map](#work-through-the-map)）。解决 ticket 时创建的资产从 issue 链接，而不是粘贴进来。

## Ticket Types

每个 ticket 要么是 **HITL**（人在回路中，与为自己说话的*人类*协同工作），要么是 **AFK**，由 agent 单独驱动。一个 HITL ticket 只通过那种现场交流来解决；agent 永远不站在人类一方（一个自己回答自己问题的 grilling agent 违反了这一点）。

- **Research**（AFK）：阅读文档、第三方 API 或本地资源（如知识库），以发现一个决策所等待的事实。由调用 Skill 工具并使用 "research" 的子 agent 解决。当需要当前工作目录之外的知识时使用。
- **Prototype**（HITL）：通过制作一个廉价的、粗糙的、具体的工件（大纲、粗略的尝试、stub 或 UI / 逻辑代码）来提高讨论的保真度，以便对其作出反应，方法是调用 Skill 工具并使用 "prototype"。将原型作为资产链接。当 "应该长什么样" 或 "应该如何表现" 是关键问题时使用。
- **Grilling**（HITL）：对话。默认情况。始终调用 Skill 工具两次，分别为 "grilling" 和 "domain-modeling"。
- **Task**（HITL 或 AFK）：在可以做出*决策*之前必须发生的手工工作：没什么可决策、原型或研究的，但讨论被阻塞直到它完成。注册一项服务以便评估其 API、预配访问权限、移动数据以便可以看到其形状。这是唯一*做*而不是决定的类型，它的存在理由是解除一个决策的阻塞，而不是交付目的地。Agent 在可以的情况下单独驱动它（AFK）；否则它交给人类一份精确的清单（HITL）。当工作完成时解决；答案记录所做的事以及任何结果事实（凭据位置、新 URL、行数）这些稍后的 ticket 依赖于。

## Fog of war

地图是*刻意*不完整的：不要绘制你还没看到的东西。在活的 ticket 之外是 **fog of war**：你能看出即将到来但还无法确定的决策和调查的朦胧视图，因为它们依赖于仍未解决的问题。解决 ticket 会清除其前方的雾，将现在可 spec 的内容一段段毕业为新的 ticket，逐段进行，直到通往目的地的路清晰并且没有 ticket 剩下。

地图的 **Not yet specified** 部分就是写下那种朦胧视图的地方：被怀疑的问题、稍后要重温的区域。它是朝向目的地的未被发现的 frontier：这里的一切都在范围内，只是不够清晰到可以做成 ticket。可以根据视野允许的程度写得松散或完整；它兼作协作读者阅读工作量去向的路标。

**雾还是 ticket？** 判定标准是你能否*现在*精确陈述问题，*而不是*你现在能否回答它。

- **当问题已经清晰时做成 ticket**，即使它被阻塞且你还无法对它采取行动。
- **当你还不能把它措辞得那么清晰时放进 Not yet specified**。不要把雾预先切成 ticket 大小的块：它比 ticket 更粗糙，一旦 frontier 到达它，一块可能毕业为几个 ticket，或一个也没有。

**Not yet specified** 不包括已经决定的内容（Decisions so far）、已经是活 ticket 的内容、以及超出范围的内容（下一个小节）。

## Out of scope

雾只朝目的地聚集。目的地固定范围，所以超出它的工作是 **out of scope**：它不是雾，也不属于 **Not yet specified**。它得到自己在地图上的 **Out of scope** 部分：你有意识地把它排除在该工作量之外。是范围而非清晰度把它放到这里。

超出范围的工作永远不毕业（frontier 在目的地处停下），所以它只会在目的地被重新绘制时返回，并且作为一项新的工作量，而不是一次恢复。

将某些东西排除在范围之外是一种 scoping 行为，而不是路线上的一步。当一个已经存在的 ticket 结果证明位于目的地之外（绘制时错入范围，或被一项解决所暴露），**关闭它**（已关闭的 ticket 明确不在 frontier 上）并在 **Out of scope** 部分留下一行：概述加上为什么它超出范围，链接到已关闭的 ticket。它仍然不在 **Decisions so far** 中，后者记录实际走过的路线；范围边界不是其上的一步。

## Invocation

两种模式。无论哪种方式，**永远不要在一个会话中解决超过一个 ticket**，研究 ticket 是例外。

### Chart the map

用户用一个松散的想法调用。

1. **命名目的地。** 调用 Skill 工具两次，分别为 "grilling" 和 "domain-modeling"，以确定这张地图正在寻找什么：spec、决策或变更。目的地固定范围，所以先解决它。
2. **绘制 frontier。** 再次 Grill，这次**广度优先**：在整个空间上分散展开，而不是在任何一条线索上深挖，找出开放决策以及现在可以采取的第一步。**如果这没有浮现任何雾**（通往目的地的路已经清晰，整个旅程小到足以容纳一个会话），你不需要地图。停下，问用户希望如何进行。
3. **创建地图**（标签 `wayfinder:map`）：填入 Destination 和 Notes，Decisions-so-far 为空，把雾勾勒到 **Not yet specified** 中。
4. **将你现在可以 spec 的 ticket 创建** 为地图的子 issue，然后在**第二轮**中接入 blocking 边（issue 需要 id 才能相互引用）。接入将它们分成 frontier 和被阻塞的；你暂时无法 spec 的一切都保留在雾中：**Not yet specified** 部分。
5. **启动研究子 agent。** 对于你刚刚创建的每个 `research` ticket，启动一个调用 Skill 工具并使用 "research" 的子 agent 来并行解决它们，把它的发现捕获到一个一次性的 `research/<name>` 分支上，并在 ticket 上留下一个上下文指针。
6. 停下：绘制地图是一个会话的工作；它不手动解决任何东西。

### Work through the map

用户用地图（URL 或编号）调用。一个 ticket 是**可选的**：没有的话，你挑选下一个决策，而不是用户。

1. 加载**地图**：低分辨率视图，而不是每个 ticket 的主体。
2. 选择 ticket。如果用户指定了一个，就用它。否则按顺序取第一个 frontier ticket。**认领它**：在任何工作之前把它分派给自己。
3. 解决它。**按需放大**：按需取任何相关或已关闭 ticket 的完整主体；为 `## Notes` 块命名的 skill 调用 Skill 工具。如果有疑问，调用 Skill 工具两次，分别为 "grilling" 和 "domain-modeling"。
4. 记录解决：将答案作为**解决评论**发布，**关闭** issue，然后向地图的 Decisions-so-far **追加上下文指针**。
5. 新浮现的 ticket（先创建再连线）；将答案已使其可 spec 的任何雾毕业，把每一段毕业的部分从 **Not yet specified** 清除，使其只作为新 ticket 存在。如果答案揭示一个 ticket（此 ticket 或另一个）位于目的地之外，**将其排除在范围之外**而不是在路线上解决它。如果该决策使地图的其他部分无效，更新或删除那些 ticket。

用户可以并行运行未阻塞的 ticket，所以要预期其他会话会同时编辑 tracker。
