## What it does

`tdd` 以 test-first 的方式构建一个特性或修一个 bug:一个失败的测试,然后只是够让它通过的代码,然后下一个行为。它承载让那个循环产出值得保留的测试的标准:什么是好测试、测试放在哪里、mock 是用来做什么的,以及悄悄毁掉一套测试的三个反模式。

它不在一个未被事先约定的 seam 上写测试。在任何测试存在之前,它命名它打算测试的公共边界,并停下来等你确认,因为测试精力是有限的,而这是你把它花在关键路径而非每条边缘 case 上的地方。另一件要知道的事是,`tdd` 是一个 **reference**,而不是一个 driver。它持有循环的规则,而别的东西(你,或 [implement](https://aihero.dev/skills-implement))跑应用它们的[会话](https://www.aihero.dev/ai-coding-dictionary/session)。

## When to reach for it

输入 `/tdd`,或在任务匹配时 [agent](https://www.aihero.dev/ai-coding-dictionary/agent) 自动调用它:test-first 方式构建特性或修一个 bug,或当你说出 "red-green-refactor"。

在有一个具体行为可构建、有输入和可观察输出,并且你想要能在重构后存活的测试时,使用它。

| 你的处境 | 该去哪里 |
| --- | --- |
| 一个有定义输入和输出的行为(业务逻辑、请求/响应契约、转换、校验) | `tdd` |
| 该行为尚未定型 | [to-spec](https://aihero.dev/skills-to-spec),它也会在任何代码被写之前约定测试 seam |
| 真正的问题其实是接口的形状,而不是测试 | [codebase-design](https://aihero.dev/skills-codebase-design) |
| 你有一份 [spec](https://www.aihero.dev/ai-coding-dictionary/spec) 或 [tickets](https://www.aihero.dev/ai-coding-dictionary/ticket) 并希望整套构建为你跑 | [implement](https://aihero.dev/skills-implement),它对每个 ticket 驱动 `tdd` |
| 配置、连线、胶水、类型注解、纯 CRUD 委托 | 这里没合适的;见下面那个 open gap |

最后一行是个真实的洞,而不是风格偏好。该技能决定 seam *放在哪里*;里面没有任何东西决定一项变更是否值得跑这个循环。把它跑在一段没有独立真值可断言的变更上,你会得到一个重述实现的测试:技能自身警告的那个"同义反复"反模式,只是从另一个方向抵达。这是 [issue #746](https://github.com/mattpocock/skills/issues/746),open。在它关掉之前,这种判断是你或你 `CLAUDE.md` 的事。

## Prerequisites

需要安装 [codebase-design](https://aihero.dev/skills-codebase-design)。`tdd` 曾经带着它自己的深度模块和接口设计笔记;在 v1.0 里那些被删,换成共享技能,`tdd` 现在靠它来提供接口设计词汇。不需要其他;该技能是 [stateless](https://www.aihero.dev/ai-coding-dictionary/stateless) 的,而且自己不写文件。

## The loop, and the seam it runs at

三个词承载这个技能。

**Red-green.** 写失败的测试,然后只写够让它通过的代码。不去预想下一条测试。没有 refactor 阶段:它在 2026 年 6 月被删除,因为 agent 几乎从不执行它,而且因为 review 和 implementation 在分开会话中更好 work。重构属于 [code-review](https://aihero.dev/skills-code-review)。

**Vertical slice.** 一个 seam、一个测试、一份最小实现,然后重复,第一个循环是一条贯穿始终的 **tracer bullet**,证明一条端到端的路径。反过来是 horizontal slicing:先全部测试,再全部代码。批量测试验证的是 *想象中的* 行为,它们检查的是东西的形状而非用户做了什么,并且它们让你在理解实现之前就被钉在了一个测试结构上。

**Pre-agreed seam.** Seam 是你在不伸到里面的情况下观察行为的公共边界。规则是绝对的:不在未确认的 seam 上写测试。在完整链中,seam 在更早的 [to-spec](https://aihero.dev/skills-to-spec) 期间就被约定:"`/tdd` is told to only work at pre-agreed test seams, `/code-review` checks that only agreed-upon test seams were used." 单独调用时,`tdd` 直接问你。

它被写成用来预防的三个反模式:

| 反模式 | 特征 |
| --- | --- |
| Implementation-coupled | 当你重命名一个内部函数时,测试坏掉,尽管行为没变。被 mock 的内部协作者、被断言的调用次数、用来验证而非用接口验证的数据库查询。 |
| Tautological | 期望值按代码计算它的方式来计算,所以测试按构造通过。期望值必须来自别处:一份已知良好的字面值、一个算好的例子、规范。 |
| Horizontal slicing | 一批测试在任何实现之前落地。 |

Mock 只用于系统边界:外部 API、时间、随机性,有时是文件系统或数据库。不是你自己的模块。

## Common questions

**为什么它不做 refactor?描述里说的是 "red-green-refactor"。**
因为 refactor 步骤被删了,描述却没改。删除是刻意的:agent 几乎从不执行它,而把 implementation 和 review 留在不同会话里效果更好。结果还算不算教科书意义上的 TDD,这比循环是否产出更好的代码更不重要。触发短语和正文之间的不匹配已经被记录为 [issue #589](https://github.com/mattpocock/skills/issues/589) 且仍然 open,所以 "red-green-refactor" 继续作为一个能触发该技能的短语。你得到的是 red → green,而 refactoring 在 [code-review](https://aihero.dev/skills-code-review)。

**它让我选一个测试 seam,而我不知道选哪个。**
这是该技能被报告最多的摩擦([issue #607](https://github.com/mattpocock/skills/issues/607))。Prompt 只按名字列出候选 seam,每个能抓住什么、漏掉什么都没说,所以你在标签之间选。尚未发布的修复。实用的变通方法是在回答之前先问 agent 各种 trade-off:组件级 seam 漏掉了什么而集成 seam 抓得到,它又慢多少。这也是为什么链会在 `to-spec` 里就预先约定 seam,因为那里你看到的是整个特性,而不是一个 prompt。

**它在测试之前写了实现,虽然技能说的是 red 在前。**
确实发生过。一位用户逼问 [模型](https://www.aihero.dev/ai-coding-dictionary/model),得到了异常坦诚的回答:"I knew the skill said 'one test at a time, watch it fail for the right reason'. I read it. I just defaulted to my normal habit." 该技能被写成与这件事共处。没有任何指令能让 agent 100% 服从,而把这一点卡得更死限制了 agent 的创造力却收益甚微;即使不完全严格执行,这个循环仍然值得跑,因为总体效果仍然更好。如果对某条切片严格遵守很要紧,看着这个 run,而不是相信该技能会强制它。

**它应该先写浏览器或端到端测试吗?**
通常不该,而该技能不会拦你。一位用户报告说,agent 先写了一份 Playwright 测试,然后烧了一个长循环反复运行它,得出 *测试* 坏掉的结论,针对一个还不存在的特性。在你的 `CLAUDE.md` 里配置这个。浏览器测试慢到红绿反馈循环开始回不了本;在你仓库的 `CLAUDE.md` 里声明它们是在行为 work 之后写的。

**`/tdd` 替代 `/implement`,或者课程的 `/do-work` 吗?**
不替代。`/tdd` 把方法论写成文档;`/implement` 是一个非常简单的 work→feedback→commit 循环,正好是 `/do-work` 的直接等价物。课程的单个 `/do-work` 步骤现在拆为 `/implement`、`/tdd` 和 `/code-review`。如果你在问针对一个 ticket 该跑哪个,答案几乎总是 `/implement`。

**深度模块和接口设计的指南去哪了?**
去了 [codebase-design](https://aihero.dev/skills-codebase-design),v1.0 里被泛化,这样多个技能共享同一份词汇。`refactoring.md` 同时离开;refactoring 现在是 [code-review](https://aihero.dev/skills-code-review) 的工作,而那个技能承载 Fowler smell 基线。

**它知道我的其他 tickets 吗?**
不知道。对一个 ticket 跑它,会乐呵呵地提议属于兄弟 ticket 的工作,因为它看不到 issue 图的全貌([issue #129](https://github.com/mattpocock/skills/issues/129))。Matt 的立场是这不是 `tdd` 的事。把 spec 和 ticket 一起传过去有帮助;从一开始就 right-size 这些 tickets 帮助更大。

## It's working if

- 在任何测试文件存在之前,它停下来命名它打算测试的 seam,然后等待。
- 一个测试出现,变红,得到刚好够通过的代码,然后才出现下一个测试,而不是一批测试跟着一批代码。
- 测试名读起来像能力("user can checkout with valid cart"),而不是像内部("checkout calls paymentService.process")。
- 断言中的期望值是你能追到规范上的字面值,而不是按代码计算它的方式重算的值。
- 重命名一个内部函数不会破坏套件中的任何东西。
- Mock 只出现在外部边界(支付 API、时钟),绝不出现在你自己的模块周围。

## Where it fits

`tdd` 是主链构建步骤内部的引擎,而不是它自己的一个步骤:

```txt
grill-with-docs → to-spec → to-tickets → implement → code-review
```

[to-spec](https://aihero.dev/skills-to-spec) 预先约定测试 seam,[implement](https://aihero.dev/skills-implement) 对每个 ticket 驱动 `tdd`,而 [code-review](https://aihero.dev/skills-code-review) 之后检查只使用了约定的 seam,并承担 `tdd` 不再做的 refactoring。它的另一个邻居是 [codebase-design](https://aihero.dev/skills-codebase-design),`tdd` 说的 seam 和深度模块词汇的共享源头。你也可以独立使用它,在有一个具体行为要构建而又没有完整 spec 在场的时候。当你不确定哪个技能适合你的处境时,[ask-matt](https://aihero.dev/skills-ask-matt) 来路由。
