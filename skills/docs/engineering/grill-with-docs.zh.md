## What it does

`grill-with-docs` 针对一份计划或设计对你做盘问,直到你和 [agent](https://www.aihero.dev/ai-coding-dictionary/agent) 对它形成一致理解,并在此过程中把你仓库里的词汇和艰难决策写进去。它和 [grill-me](https://aihero.dev/skills-grill-me) 跑的是同一种盘问(一轮问题,然后等待,然后下一轮),只不过这里指向一个代码库。

它是 **[stateful](https://www.aihero.dev/ai-coding-dictionary/stateful)** 的。其他 grilling 技能把[会话](https://www.aihero.dev/ai-coding-dictionary/session)留在你脑子里;这个会把文件留在磁盘上。一个术语被确定,它立刻就落到 `CONTEXT.md` 里,而不是最后批量落。一项决策通过三道门,它就作为一份 ADR 落地。这就是全部区别,它也是人们使用这个技能时大多数麻烦的来源:工件是真实仓库里的真实文件,所以它们可能在你期望它们出现时缺席,而且当多于一个人在写它们时,会漂移。

## When to reach for it

你通过输入 `/grill-with-docs` 来调用它;agent 不会自己调用它。

在一项变更的开始、在一个仓库里、计划仍然模糊、事物的词还没有定下来时,使用它。它是单会话工具。要选哪个 grilling 技能,取决于你面前的东西:

| 你拥有 | 调用 |
| --- | --- |
| 你根本不在一个工作目录里 | [grill-me](https://aihero.dev/skills-grill-me) |
| 一个仓库,和一个能在一个会话里定下来的变更 | `grill-with-docs` |
| 一项大到无法塞进一个会话的工作(绿地构建、大型特性) | [wayfinder](https://aihero.dev/skills-wayfinder) |
| 仓库完全没有领域文档,而且心里没具体特性 | `grill-with-docs`,对准仓库而非一项变更 |
| 一个被卡在别人脑子里的知识所阻塞的决策 | [to-questionnaire](https://aihero.dev/skills-to-questionnaire) |

Wayfinder 分割的根据是会话数:`/grill-with-docs` 用于单会话规划,`/wayfinder` 用于多会话规划。

## Prerequisites

这个技能往你仓库里写,所以你得待在一个它能安全写入的地方。已确定的术语写到根目录的 `CONTEXT.md` 术语表,或者当根目录的 `CONTEXT-MAP.md` 把仓库标记为多上下文时,写到相应上下文的 `CONTEXT.md`。决策写到 `docs/adr/`。两者都是懒创建;在第一个术语或决策成形之前什么都不存在,所以事前不需要任何脚手架。

它还需要另外两个技能在场,因为它自己的 `SKILL.md` 只有一行,委派给它们:[grilling](https://aihero.dev/skills-grilling) 提供盘问,[domain-modeling](https://aihero.dev/skills-domain-modeling) 提供落字。只安装 `grill-with-docs` 会得到一个跑不起来的技能。

## The paper trail

一个会话产出三样东西,而它们并不相等。

| 什么被确定 | 落在哪里 |
| --- | --- |
| 一个术语:项目自己的、对一个事物的词 | `CONTEXT.md`,内联,确定的那一刻 |
| 一项难以回退、没上下文会令人意外、且来自真正权衡的决策 | `docs/adr/` 下的一份 ADR |
| 其他你决定的所有事 | 对话里,且仅在那里 |

第三行才是让人栽跟头的那行。`CONTEXT.md` 是一份术语表,而且刻意只做术语表:没有实现细节、没有 [spec](https://www.aihero.dev/ai-coding-dictionary/spec)、没有随手笔记。ADR 是三条门槛同时生效的,所以大多数决策不达标,大多数会话不产 ADR。一个产出更锐利的术语表且零 ADR 的会话是按设计运行的,但这意味着你达成的大部分共识只存在于你达成它的那个[上下文窗口](https://www.aihero.dev/ai-coding-dictionary/context-window)里。把那段对话交给 [to-spec](https://aihero.dev/skills-to-spec) 而不是 [clearing](https://www.aihero.dev/ai-coding-dictionary/clearing) 它。

术语表才是重点。领域语言是这个技能真正构建的东西:项目自己的词,只达成一次,这样你、agent 和你的同事不用反复重新推导。最尖锐的公开反对意见是:一个术语和它的 plain-English 展开,从[模型](https://www.aihero.dev/ai-coding-dictionary/model)那里得到的结果相同,词汇真正压缩的是共享它的人之间的沟通。这种解读下术语表仍然有价值;只是把价值的位置挪了一下。

## Common questions

**我应该用这个还是 `/wayfinder`?**
范围决定它。能在单会话定下来的事情用本技能;某项工作大到无法塞进一个会话时,用 [wayfinder](https://aihero.dev/skills-wayfinder),它先以决策 [tickets](https://www.aihero.dev/ai-coding-dictionary/ticket) 的 map 来规划工作。Wayfinder 更慢也更密集,在一个范围明确的功能上去用它是常见的错误。它不能替代本技能:它能为 map 中适合单会话的部分降到一个 grilling 会话里。

**它跑了,但 `CONTEXT.md` 和 ADR 都没出现。**
两个已知原因。平淡的那个:没东西达标。ADR 三道门都要过,一次围绕某项变更、没引入新词汇的会话,真的没东西可写。真正的 bug:当这个技能跑在另一个编排层里面(一个 spec-driven-development 包装器、一个多 agent 框架、一条把它当流水线某一步调用的规则),落文件的那一半被报告为静默未发生,而盘问照跑。已记录,未修复。如果你处于这种设置,在你相信该会话的输出之前,先检查工作目录。

**它把所有问题一次性抛出,没有任何推荐,也从未提到 `CONTEXT.md`。**
那是这个技能没能加载它的两个依赖。因为 `SKILL.md` 是一行委派,一个没有抓起 [grilling](https://aihero.dev/skills-grilling) 和 [domain-modeling](https://aihero.dev/skills-domain-modeling) 的 agent 会猜测 grilling 是什么意思,然后你得到一堆未分化的提问。部分加载是更让人困惑的情况:`grilling` 加载了,`domain-modeling` 没加载,你会得到一次好的盘问但没有 paper trail。这与模型和 [effort](https://www.aihero.dev/ai-coding-dictionary/effort) 等级相关,是这个技能被报告最多的问题。如果你怀疑,直接问 agent 它加载了哪些技能。

**我其他所有的决策都去哪了?**
只在对话里。这是对这个技能最实质的 open 抱怨:术语表不是 spec,大多数回答挣不到一份 ADR,也没有任何台账把每个已确定的回答串到 spec、ticket 和测试上。精确的回答(顺序保证、负向需求、数字默认值)在下游被软化为更弱的散文,结果看似完整,却漏掉你真正决定的那件事。当下可用的缓解措施是保留这次会话,把它直接喂给 [to-spec](https://aihero.dev/skills-to-spec),并对照你自己的回答重读 spec,而不是假设它抓住了它们。

**我能不能把它指向一个完全没有任何文档的现有仓库?**
可以。对于一个没有 ADR、没有领域语言、没有设计原则的代码库:调用它并说 "help me document my repo"。社区的模式是把它和 [improve-codebase-architecture](https://aihero.dev/skills-improve-codebase-architecture) 配对,以构建或修复 `CONTEXT.md`。预期你会亲自引导:它会读代码并问你在它发现的东西里,代码库里已经存在的哪些词才是对的那批。

**会话结束时我应该做什么?**
这个技能的收尾消息常常是开放式收尾,这是一个已知的粗糙边。在主流程中,答案是 [to-spec](https://aihero.dev/skills-to-spec),在同一段对话里。如果变更小到可以立刻构建,直接走 [implement](https://aihero.dev/skills-implement) 即可。

**为什么这么叫这个名字?**
没人对这个名字满意。有一项 open 建议把它改名为 `grill-domain-model`,后者更诚实地描述了它的行为。这件事没有任何进展。如果哪天真的改了,文档页跟着改,URL 也跟着变。

## It's working if

- `CONTEXT.md` 在会话 *期间* 一项一项地变化,而不是在末尾一次性冒出来。
- 这份术语表读起来像纯粹的词汇(项目自己的词和紧凑的定义),没有任何实现细节或类 spec 的散文。
- 代码库能回答的问题由代码库自己回答,而不是来问你。
- 你收到很少甚至没有 ADR,有的话也是那些你得重新审理一遍会不爽的决策。
- 它因为你用了某个词而你已有的术语表对它的定义不同,反过来挑战你。

## Where it fits

`grill-with-docs` 是主构建链的头:

```txt
grill-with-docs → to-spec → to-tickets → implement → code-review
```

它出现在任何东西被写成 spec 之前:它产出共享理解和已确定词汇,[to-spec](https://aihero.dev/skills-to-spec) 然后在没有再对你盘问的情况下综合这些。它的最近邻居是 [grill-me](https://aihero.dev/skills-grill-me),同一场盘问但无仓库无文件;以及 [domain-modeling](https://aihero.dev/skills-domain-modeling),它驱动的术语表与 ADR 纪律;这两者都建在 [grilling](https://aihero.dev/skills-grilling) 原语上。它上游是 [wayfinder](https://aihero.dev/skills-wayfinder),后者为大到无法塞进一个会话的工作绘制 map,并能把 map 的部分回交给它。当你不确定哪个技能或流程合适时,[ask-matt](https://aihero.dev/skills-ask-matt) 来路由。
