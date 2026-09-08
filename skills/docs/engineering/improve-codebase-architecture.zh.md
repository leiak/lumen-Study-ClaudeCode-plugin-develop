## What it does

`improve-codebase-architecture` 扫描一个代码库,寻找 **deepening opportunities**:那些从一个浅模块(接口和它所隐藏的东西几乎一样复杂)能变成深模块的地方。它把结果写成一份独立的 HTML 报告,然后对你 [grill](https://www.aihero.dev/ai-coding-dictionary/grilling) 你挑中的那一项。

它从不修改代码。整个 run 只产出你 OS 临时目录里一份 HTML 文件和一段对话;重构本身在另一个[会话](https://www.aihero.dev/ai-coding-dictionary/session)里,通过正常的 build flow 完成。这就是它为何是一项调查而非重构工具,也说明为什么这个技能在一个你还没准备好动手的代码库上仍然值得跑。

两道筛子防止报告沦为通用清理建议。每个候选者必须通过 **deletion test**:如果删掉这个模块,复杂性是被集中到更小的接口后面,还是分散到所有调用方?只有"集中"的才有资格成卡。除非你把它指向一个特定区域,否则它先读最近的 commit 历史,并把扫描偏向那些活跃变更的路径,理由是一份在没人碰的代码上的深化,是一次你永远无法兑现的重构。

## When to reach for it

你通过输入 `/improve-codebase-architecture` 来调用它;[agent](https://www.aihero.dev/ai-coding-dictionary/agent) 不会自己调用它。

它处在 build 循环之外:它不是主循环中的一步,而是你周期性运行的、用来为改进代码库排队更多工作的东西。它有四种典型用法:

| 处境 | 怎么用 |
| --- | --- |
| 日常维护 | 隔几天跑一次,或一有空就跑,防止功能之间结构腐化 |
| 大构建之前 | 把它指向 [spec](https://www.aihero.dev/ai-coding-dictionary/spec):"how can we make this change easy?" 这是对它最有效的 prompt |
| Brownfield 审计 | 在一个大型、无结构或 [vibe-coded](https://www.aihero.dev/ai-coding-dictionary/vibe-coding) 的仓库上跑,弄清它实际的形状 |
| 遗留测试工作 | 在针对不可测代码写测试之前,先用它找出缺失的 seam |

在容易和它混淆的兄弟技能里:

- 为了设计你已选定的那个模块,用 [codebase-design](https://aihero.dev/skills-codebase-design):那是一张工作台,这个是去找要放到工作台上的东西的调查。
- 对于大到无法塞进一个会话的整体工作,用 [wayfinder](https://aihero.dev/skills-wayfinder)。
- 对于 "这特定的东西坏了",用 [diagnosing-bugs](https://aihero.dev/skills-diagnosing-bugs)。当真正的发现是没有合适的 seam 可以把 bug 锁住时,它会回交到这里。

## Prerequisites

跑它不需要任何东西。它会读 `CONTEXT.md` 以及 `docs/adr/` 里任何存在的 ADR,并用你领域自己的名词来讲:一个候选者会被写成 "deepen the Order intake module",而不是 "refactor the FooBarHandler"。

它在两个地方写。报告写到 `<tmpdir>/architecture-review-<timestamp>.html`,在仓库之外。在 grilling 循环中它会添加或磨尖 `CONTEXT.md` 中的术语,如果该文件不存在就创建,并提议把一个被拒绝的候选者记成 ADR,这样未来的 run 就不会再次提议它。

## Depth, and the report that hunts for it

这个技能围绕一个想法运转:**depth**。一个深度模块把大量行为藏在一个小而稳定的接口后面。一个浅模块的实现通过一个几乎与底下代码一样宽的接口泄漏出来。报告以三种形式搜寻浅:为可测性而抽取的纯函数,而真正的 bug 藏在它们的调用方式里(没有 **locality**)、跨 **seam** 泄漏的模块、以及一个你不开五个文件就看不懂的概念。它以一份修复它的深化提案收尾。

每个候选者是一张卡片:涉及的文件、摩擦、一段 plain-English 的解法、用 **locality** 和 **leverage** 表述的收益、一份 before/after 图、以及一枚强度徽章。

| 徽章 | 对你意味着什么 |
| --- | --- |
| `Strong` | Deletion test 清晰通过,摩擦是真实的。认真对待。 |
| `Worth exploring` | 可信的深化,但回报取决于代码接下来往哪里走。 |
| `Speculative` | 为完整性浮现出来。这些大部分可以安全忽略。 |

报告以一份 **Top recommendation**(它会首先动的那一项)收尾,然后技能停下来问你想要探索哪个候选者。那一点上什么都没决定,没有任何代码被移动。

## What happens after you pick one

挑一个候选者会启动一个针对它的 [grilling](https://aihero.dev/skills-grilling) 会话:约束、seam 后面是什么、哪些测试存活、深化的接口应该是什么样。该会话的产出是一项决策,而不是一段 diff。从那里开始正常的流程:把决策带进 [to-spec](https://aihero.dev/skills-to-spec),然后 [to-tickets](https://aihero.dev/skills-to-tickets),然后 [implement](https://aihero.dev/skills-implement)。

## Common questions

**它就一个想法盘了我一个小时,而不是给我看选项。我能关掉这个吗?**
可以:在调用时说出来("don't grill me, just show the report")。这是该技能最大的抱怨。一位用户说得很直:他们觉得它是 "a convenient way to get a thorough analysis of improvements",而加入 grilling 循环之后觉得它 "borderline unusable",报告里有会话在它提出一个解法后问 "10's or 100's of questions"。设计意图是报告先到,grilling 只在你选定的候选者上启动,但更弱的[模型](https://www.aihero.dev/ai-coding-dictionary/model)会跳到直接盘问你关于它的第一个想法。该 thread 上不同模型之间的报告差异极大,这是一个 open issue:该技能还没有一个文档化的无 grill 模式。

**报告打开是未加样式的原始 HTML,没有图。怎么回事?**
报告从 CDN 加载 Tailwind 和 Mermaid,所以在你打开它的时候需要网络访问,而当有什么东西拦截这些脚本时会静默失败。被记录的案例是一个安全 hook 索要 SRI 哈希:agent 加上了哈希,CDN 给浏览器发的字节和 `curl` 取哈希时拿到的不同,浏览器拦截了脚本。离线或强锁定的环境撞上同一堵墙。agent 看不到这点,因为它从不渲染该页面。变通方法是请求内联 CSS 和手写 SVG 图,而不是 CDN 脚手架。这是一个 open issue,真实的粗糙边。

**它给了我十二个候选者。我应该在同一个会话里挨个过,还是开新会话?**
一个候选者一个会话。在同一个对话里过多个,会让[上下文窗口](https://www.aihero.dev/ai-coding-dictionary/context-window)被报告、grilling、domain-model 修改和代码改动一起塞满。报告只活在临时文件里,所以带着候选者本身而不是文件:挑一个,grill 它,把决策带进 `/to-spec`,把剩下的转成你可以独立挑起的 [tickets](https://www.aihero.dev/ai-coding-dictionary/ticket)。把选中的改进放进 spec,而不是直接实现。这是一个反复出现的问题,而技能自身里没有文档化的流程。

**我该怎么给它 prompt?**
心里装着你接下来要构建的东西。如果有一项大构建要来,把 spec 指给它,问 "how can we make this change easy?"。无 prompt 的 run 自行扫描热点,这对日常维护没问题,但命名一个方向才是让报告可行动的关键。

**它在一个大型遗留代码库上能用吗?**
部分能。它在缺乏一致结构的大型已有代码库上很强,而且是一次性结构搭建之后推荐的维护机制。诚实的反面:报告说 "helped a little but still doesn't seem to cut it" 的真正失控项目的用户,以及一位开发者报告说他的八年遗留代码库上模型绕圈,而同一个技能在一个干净的仓库上产出一份干净的图。还没有专门的 `/refactor` 技能来处理那种情况。如果代码库完全没有共享词汇,先 [grill-with-docs](https://aihero.dev/skills-grill-with-docs) 建立一份,通常会让这个技能的输出好得多。

**这和 `/codebase-design` 有什么区别?**
`/codebase-design` 是一个 reference,而不是一个会话 driver。它提供词汇(module、interface、depth、seam、adapter、leverage、locality),这个技能借用它。把一个新 agent 指向 `/codebase-design` 把它当作要去 "do" 的东西是一个已知失败:既然没有它自己的流程可循,agent 就会自己编一个,重新探查代码,跑得很远才问你任何事情。用这个技能驱动,让那个技能被消费。

**它会告诉我代码库没问题吗?**
很少,你应该进场就知道。该技能被构建来产出发现,所以它的话术把它推向产出候选者,而不是得出"没事"的结论。强度徽章是它的防线:一份所有内容都是 `Speculative` 的报告,就是该技能用唯一会的方式在说它什么也没找到。

**它在 Codex 或另一个 harness 上能用吗?**
部分能。探索步骤直接命名了 Claude Code 的 `Agent` 工具及其 `subagent_type=Explore`,所以一个没有该工具的 [harness](https://www.aihero.dev/ai-coding-dictionary/harness) 可能跳过并行探索,而不是用自家的工具替代。该技能仍然能跑;扫描只是不那么彻底。一份 harness-neutral 的重写已被提议,但尚未合并。

**我实际上如何在 TypeScript 中实现深度模块?**
目前没有随该技能一起发布的可靠答案。反复被请求的是一份 `TYPESCRIPT.md`,为这些原则给出具体的文件和模块布局,但它不存在。该技能会告诉你哪里需要深化、什么应该藏在 seam 后面;把它翻译成一份 package 或目录结构,目前是你自己的事。

## It's working if

- 候选者命名你领域的概念,而不是凭空造的类名:"the Order intake module",而不是 "the FooBarHandler"。
- 候选者集中在你最近编辑过的文件,而不是仓库里沉睡的角落。
- 整个 run 中没有代码被改动。唯一的新文件就是临时目录里的那份 HTML 报告。
- 它在报告之后停下来问你想要哪个候选者,而不是自行继续。
- 每张卡片把回报解释为 locality 或 leverage,并说哪些测试会变简单,而不是仅仅 "this is cleaner"。
- 因可信理由拒绝一个候选者会换来一份 ADR 的提议,这样下一次 run 就不会再提它。

## Where it fits

`improve-codebase-architecture` 是 **周期性维护**:每隔几天,在任何链之外跑一次,用来排队工作而不是执行工作。它的近邻是 [codebase-design](https://aihero.dev/skills-codebase-design),它拥有每个候选者都按其书写的 depth-and-seam 词汇;[grilling](https://aihero.dev/skills-grilling),在你挑中一个候选者后走决策树;以及 [domain-modeling](https://aihero.dev/skills-domain-modeling),在决策落地时让 `CONTEXT.md` 和 ADR 与之同步。它产出的是一项想法,通过 [grill-with-docs](https://aihero.dev/skills-grill-with-docs) 或 [to-spec](https://aihero.dev/skills-to-spec) 重新进入主 build flow。对于哪个技能适合某种处境,[ask-matt](https://aihero.dev/skills-ask-matt) 在整套技能之间路由。
