## What it does

`wayfinder` 接住一项对一个 agent [会话](https://www.aihero.dev/ai-coding-dictionary/session)来说太大的工作:一个你能说出 **destination** 但看不清路线的主意,并把它绘制成你 issue tracker 上由 **decision tickets** 组成的一份共享 **map**,然后一次一张地把它们解决,直到路清晰。

它做规划,不做。每张 ticket 持有一个其解决方案为决策而非待执行构建切片的问题,而这张 map 在有人去构建那东西之前没什么可决策时完成。这条规则是把 wayfinder ticket 和普通实现 [ticket](https://www.aihero.dev/ai-coding-dictionary/ticket) 区分开的东西,也是 agent 最常打破的那条。当 map 清除时,wayfinder 交接;它不会继续进到代码里。

## When to reach for it

你通过输入 `/wayfinder` 来调用它;[agent](https://www.aihero.dev/ai-coding-dictionary/agent) 不会自行调用它。

它是整套中最重、最密的 flow,所以触发条件很窄:工作量必须真的大于一个 agent 会话所能容纳,且通往 destination 的路线必须模糊。切分是干净的:`/grill-with-docs` 用于单会话规划,`/wayfinder` 用于多会话规划。

| 你面前 | 该跑什么 |
| --- | --- |
| 一项能在一个会话里定下来的、范围明确的功能 | [grill-me](https://aihero.dev/skills-grill-me),或当有代码库时用 [grill-with-docs](https://aihero.dev/skills-grill-with-docs) |
| 绿地项目,或跨多个会话的构建,路线仍不清 | `/wayfinder` |
| 一段对话里决定已经做完了 | [to-spec](https://aihero.dev/skills-to-spec):直接跳过 map |
| 一张已清的 wayfinder map | [to-spec](https://aihero.dev/skills-to-spec),然后 [to-tickets](https://aihero.dev/skills-to-tickets) 和 [implement](https://aihero.dev/skills-implement) |
| 一个已经长到过大的现有会话 | 说 "hand off to `/wayfinder`"([handoff](https://aihero.dev/skills-handoff) 既能从一张 map 里出来,也能进到一张 map 里) |

绿地不是必要条件。Wayfinder 在遗留和半成品代码库上被常规使用,而且那里它可能更锐利,因为大量模糊是 "这里已经什么是真的" 而不是 "我们应该做什么"。

## Prerequisites

Map 和它的 tickets 住在仓库的 issue tracker 上,所以 wayfinder 需要 [setup-matt-pocock-skills](https://aihero.dev/skills-setup-matt-pocock-skills) 铺设的 tracker 连线。那一步写一段 "Wayfinding operations" 段,描述 map、它的子 tickets、阻塞边以及 frontier 查询在 GitHub、GitLab 或本地 markdown 下的表达。Wayfinder 通过你的 `CLAUDE.md` / `AGENTS.md` 里的指针解析那份文档,而不是固定路径;如果根本没配置 tracker,它就回退到本地 markdown 文件。

Tracker 不是装饰。Blocking 正是 frontier 在 tracker 自家 UI 里视觉上呈现的东西,一个没有原生依赖链接的 tracker(比如自托管 Gitea)会把 wayfinder 退化为从 map 文本推断 blockers,这能用,但需要更近距离的监督。

## The map, the fog, and the frontier

**map** 是一张带 `wayfinder:map` 标签的单个 issue;它的 tickets 是它的子 issues。它是一份 **index,不是存储**:一项决策恰好住在一个地方,它的 ticket,而 map 只是对它做摘要并链接。一次会话以低分辨率加载 map,并按需放大到单张 ticket,这正是一张 map 能在每个会话不为其整个历史付费的前提下继续增长的原因。

四样东西住在它上面:

- **Destination**:到达本张 map 末尾长什么样。在任何 ticket 存在之前,命名它是绘制的第一动作,因为 destination 固定了衡量每张 ticket 的尺度。
- **Decisions so far**:每张已关闭 ticket 一行,每行链接到细节真正住的地方。
- **Not yet specified**:**fog of war**。你能说出正在到来、但还不能锐利地措辞的决策。Fog 和 ticket 的分界是你能否 *现在* 把问题陈述精确,而不是你能否回答它。解决一张 ticket 把雾从它前面清掉,把所有现在可规格化的东西升级为新的 tickets。
- **Out of scope**:被排除在 destination 之外的工作。Fog 只会向 destination 聚集,所以 out-of-scope 工作被关闭,永不升级。

**frontier** 是开放的、未阻塞的、未被认领的 tickets(known 的边界)。一个会话在开始任何工作前通过把自己分配为 assignee 来认领一张 ticket,所以 assignee *就是* 认领,并发会话会跳过它。Tickets 始终按名字指代,从不按裸的 `#42`;一面 issue 数字墙在叙述里读不懂。

## The four decision-ticket types

每张 ticket 带一个 `wayfinder:<type>` 标签,且是 **[HITL](https://www.aihero.dev/ai-coding-dictionary/human-in-the-loop)**(由一个能为自己说话的真人协作)或 **[AFK](https://www.aihero.dev/ai-coding-dictionary/afk)**(agent 单独驱动)。HITL ticket 只通过现场交流解决;一个回答自己 [grilling](https://www.aihero.dev/ai-coding-dictionary/grilling) 问题的 agent 已经破坏了它。

| Type | 模式 | 在什么情况下用它 | 怎么解决 |
| --- | --- | --- | --- |
| `grilling` | HITL | 默认。问题可以通过谈论解决。 | [grilling](https://aihero.dev/skills-grilling) 加 [domain-modeling](https://aihero.dev/skills-domain-modeling),在一个新会话里 |
| `prototype` | HITL | "How should this look" 或 "how should this behave":一个谈论无法解决的问题。 | [prototype](https://aihero.dev/skills-prototype),把构建出的工件作为资产从 ticket 链接进来 |
| `research` | AFK | 一个工作目录之外的事实阻塞了决策。 | 一个 [research](https://aihero.dev/skills-research) [subagent](https://www.aihero.dev/ai-coding-dictionary/subagent),在 charting 时触发,在 `research/<name>` 分支上并行烧掉 |
| `task` | 任一 | 没什么可决策,但手工工作阻塞了一项决策,例如开通访问、注册服务、或移动数据以便能看到它的形状。 | 能由 agent 单独做的就由 agent 做,否则为真人准备一份精确的清单 |

`task` 是真正 *做* 而不是 *决定* 的唯一一种,而它凭 unblocking 一项决策而赢得一席之地,从不凭交付 destination 的一块。这是实践中常常出错的那一类:agent 会把它解读为一步实现,然后开始在 map 里面写产品代码。

Research 是 *one ticket per session* 的唯一例外。

## Common questions

**这和 `/grill-with-docs` 有何不同?我应该从哪个开始?**
会话数,而不是项目规模。`/grill-with-docs` 是单会话规划;wayfinder 是多会话规划。如果你能把整个事情装在一段对话里,grilling 是更便宜也更好的工具,wayfinder 在那种情形下确实更慢更密。社区对此形成的简写是:wayfinder 只有在装不进一个会话的工作上才有意义。这是 wayfinder 被问得最多的问题,且它一直被问,因为描述没有告诉你自己的任务落在这条线的哪一边。你得自己判断会话数。

**它问 "destination" 的时候,是这个会话的末尾,还是一切的末尾?**
整张 map。也就是说,是整张 map 的 destination,而不是仅仅初始会话。这个问题读起来有歧义,因为 wayfinder 顾名思义就是多会话工具,所以一个以会话为单位回答的答案永远说不通。典型的 destination 是一份要交接的 [spec](https://www.aihero.dev/ai-coding-dictionary/spec)、一项要在规划开始前锁定的决策、一个概念验证、或一次像数据迁移那样的就地变更。

**Map 已经清了。Wayfinder 不是已经写了 spec 并造了 tickets?为什么我还需要 `/to-spec` 和 `/to-tickets`?**
不是。Wayfinder 的 tickets 是决策 tickets,而当 map 关闭时它们也全部关闭。剩下的是一张装满链接决策的 map,这不是一份构建计划。[to-spec](https://aihero.dev/skills-to-spec) 把那些链接决策折成一份 spec(`/to-spec #<map_issue>`),而 [to-tickets](https://aihero.dev/skills-to-tickets) 把那切成 tracer-bullet 实现 tickets。把 map 直接喂给 [implement](https://aihero.dev/skills-implement) 会跳过那次折叠,并把链接的细节扔掉。只有当工作量确实小时,才直接走实现。人们确实跑过这条简化流水线并报告 work;多出来的两步换来一份显式的 spec 工件,审阅者或同事可以读,这在不那么单干时更重要。

**我的 agent 在一个 wayfinder 会话中间开始写 production 代码。**
这是该技能被报告最多的失败,而且背后有一个真实的洞。Wayfinder 的 "plan, don't do" 默认可以在 map 的 **Notes** 里被覆盖,但 Notes 是由 agent 写的,所以约束和它的豁免住在同一个由被约束方拥有的文件里。一位用户眼看着一个 agent 在自己的 Notes 里写 "this map carries execution",然后在之后的会话里把它读回来当作自己的许可证,在一个 live server 上构建。在技能内部目前没有硬性的 "I meant the default" 停止点。在它到来之前:读任何不是你亲手 chart 的 map 的 Notes,把实现留在它自己的会话里,并把任何看起来像构建切片的 `wayfinder:task` 当作类型错。

**我 chart 了 27 张 ticket,等我做到第 13 张,剩下的不再有意义。**
一个真实且被反复报告的结局,逐字摘自一份现场报告。Wayfinder 的默认本能是穷尽式规划,而那些后续 ticket 立在前面 ticket 推翻了的假设之上的 map,正是该技能被指控的瀑布陷阱。两件事与之对抗。把 map 限定到一个有边界的 destination,而不是整个产品。实践者一致报告说,scope 到一个定义好的 epic 的 map 比蔓延的 "implement V1" 表现更好,而规划非常大的东西本来就不是目标:目标是小步交付。还有就是激进地 [prototype](https://www.aihero.dev/ai-coding-dictionary/prototyping):路线保持新鲜的整个原因是,不确定性在实现依赖它之前,就被便宜的具体工件冲掉了。Wayfinder 是 "prototypemaxxing",不是 "planmaxxing"。

**我能不能并行处理几张 ticket?**
Frontier 被构建来向你展示什么是可接的,而阻塞边的存在让并行在纸面上安全。实践中,一次一张是更安全的默认。同时处理两张 grilling ticket 的用户会在一个会话里被问到一个他们刚在另一个里回答过的问题,因为这些会话不共享[上下文](https://www.aihero.dev/ai-coding-dictionary/context)。在 prototype ticket 上还有一个已知缺口:有报告说一个 agent 搭了三个 UI 变体,自己挑了一个,然后关闭 ticket。挑选是你来做,而该技能目前没有足够响亮地说出来。如果你真要并行,自己先 review 依赖图。

**我必须用 GitHub Issues 吗?**
不必。任何 issue tracker 都能用。GitHub 是支持最好的路径,因为它原生的子 issues 和阻塞关系是 frontier 不打开 map 就能呈现;GitLab、Linear、Jira 和本地 markdown 都被使用。两个诚实的 caveat。一个没有原生 blocking 的 tracker 意味着依赖图从文本推断,且需要手工修正。本地 markdown 把这些工件放在你的仓库里,这并不推荐:把这种材料存在仓库里往往导致意外持久化。开源维护者撞到相反的问题(公开的 tracker 被 agent 生成的规划 ticket 灌满),但仍然倾向选本地 markdown。

**Grilling 太累了。每个问题都三段长。**
这是关于 wayfinder 最尖锐的现存抱怨,且尚未解决。一位用户给出了一个分解:冗长本身造成决策疲劳,且长度把 *为什么* 在问这个问题剥离掉了,所以随着 map 变长,你丢失了从决策到决策的链条。冗长看起来更像是当前那套 [models](https://www.aihero.dev/ai-coding-dictionary/model) 的属性,而不是技能的属性,没有修复落地。流传中的实践者缓解措施:跑较低的 [reasoning effort](https://www.aihero.dev/ai-coding-dictionary/effort),并在你的全局 `CLAUDE.md` 里放一条简明指令。无论如何,准备好真正花心思在这里,因为 wayfinder 向你索取的思考量不是缺陷,而是它大部分的用途。

**我已经关闭的一项决策结果错了。我编辑那张旧 ticket 还是造一张新的?**
没有官方指引,而 agent 的直觉也无济于事:它倾向于绕过那个坏决策而不是挑战它,所以你得手动引导。能 work 的是坦率地告诉 wayfinder 发生了什么;它会更新 map,修订受影响的 tickets,并在已关闭的 tickets 上加 comment。Scope 在 map 中途变化是可恢复的。一张你 *设计成* 会变的 map 是一种 scope 上的怪味。

**`decision-mapping` 去哪了?**
就是这个技能,v1.1 改名为 `wayfinder`,调用为 `/wayfinder`。"Decision map" 是术语,也不准确,因为四种 ticket 类型中只有一种本身就真的是决策。这个重新框定给了这个技能一套一致的词汇(destination、fog of war、frontier、map),而不是在术语之上叠一个生造词。这个单元仍然保留 "decision" 一词:**decision ticket** 就是一张 wayfinder ticket 被叫做的东西,正是为了阻止人们把它读成一张实现 ticket。

## It's working if

- Destination 在任何 ticket 存在之前就写下来并被同意。
- 每张 open ticket 读起来都是一个问题。任何读起来像 "build the X" 的 ticket 要么类型错,要么属于 map 的下游。
- 你能看着你的 tracker,不打开 map 就能看出哪些 tickets 是可接的,因为 frontier 通过原生 blocking 自己呈现。
- 一次会话解决一张 ticket,把答案作为 resolution comment 发布,关闭它,并在 map 的 *Decisions so far* 上留下一行。然后它停下。
- **Not yet specified** 在缩小。一团升级为 ticket 的雾从那段消失,而不是同时活在两处。
- 当开场那次广度优先 grill 完全不冒出雾时,该技能停下来告诉你工作量小到可以跳过这张 map。
- 完成 map 的那次会话把你交给一份 spec,而不是一份 PR。

## Where it fits

`wayfinder` 是一个 **situational on-ramp**,不是默认前门。由 grilling 领头的"主意 → 上线"链仍然是大多数工作开始的地方;wayfinder 是当那个主意大到一个会话装不下时你爬上去的,并在 [to-spec](https://aihero.dev/skills-to-spec) 处合流回那条链,因为一张清空的 map 交接而不是构建。

在底下,它大多是披着 wayfinder 调度的其他技能:[grilling](https://aihero.dev/skills-grilling) 和 [domain-modeling](https://aihero.dev/skills-domain-modeling) 解决默认 ticket 类型,[prototype](https://aihero.dev/skills-prototype) 解决谈论无法解决的 tickets,而 [research](https://aihero.dev/skills-research) 作为 subagent 跑,所以它的阅读永远不会落到你的会话里。[handoff](https://aihero.dev/skills-handoff) 是进出之桥:从一段超出自身的对话进入一张 map,从一张 mid-session 出现一个 side quest 时出来。对于其他事,[ask-matt](https://aihero.dev/skills-ask-matt) 在整套技能中路由。
