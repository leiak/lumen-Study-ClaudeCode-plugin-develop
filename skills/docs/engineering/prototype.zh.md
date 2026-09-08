## What it does

`prototype` 写 **回答问题的、用完即弃的代码**:这个状态模型感觉对吗,或者这个屏幕应该长什么样。问题先到,并决定后面一切的形状;一个回答了错误问题的原型,无论看起来多好,都是纯粹的浪费。

用完即弃是对代码 *如何被写* 的一种约束,而不是销毁它的承诺。没有测试、没有让代码跑起来之外的错误处理、没有抽象、没有持久化,因为这些都不能帮你学到那个你唯一想学的东西。留下来的是答案,折进真正的代码,以及原型本身,放在一个不在 main 上的分支上,作为答案来源的证据。

## When to reach for it

输入 `/prototype`,或在任务匹配时 [agent](https://www.aihero.dev/ai-coding-dictionary/agent) 自动调用它。

在你撞上一个不能靠说话定下来的问题时使用它:一个你没法把边界 case 装进脑子里的状态机,一个直到你看到三个版本并列才想得出来的屏幕。[Grilling](https://www.aihero.dev/ai-coding-dictionary/grilling) 会话在正好这些问题上膨胀:agent 换措辞,你猜,scope 增长以填满不确定性。停掉 grilling,搭出用完即弃的版本,看一眼,然后用一句话回答。如果已有的东西行为不对,你想知道为什么,用 [diagnosing-bugs](https://aihero.dev/skills-diagnosing-bugs);原型探索的是要构建什么,而不是构建出来的东西为何坏掉。

你也会在没主动选的情况下落到这里。[wayfinder](https://aihero.dev/skills-wayfinder) 在它的 map 上列出 `prototype` 决策 [tickets](https://www.aihero.dev/ai-coding-dictionary/ticket),做其中一个就是本技能。

## Two branches

问题决定分支,而分支产出非常不同的工件:

- **"这个逻辑/状态模型感觉对吗?"**:一份 **单独可分享的 HTML 文件**。一个自包含的页面,无需构建也无需服务器,有人双击就能打开。它带一个带标签的状态面板,每次点击后重渲染;有 free-play 按钮,可以按任意顺序戳这个模型;以及带标签的 **guided walkthroughs**(每个场景一个 tab,下面是该场景下要按的有序按钮)。一切用领域语言标记,所以你可以把它交给设计师、PM 或领域专家,让他们自己感受这个模型。页面背后的逻辑是一个小型纯模块(一个 reducer、一台 machine、或一组函数),与 DOM 解耦,这样验证过的版本能直接搬进真正的代码。
- **"这应该长什么样?"**:同一条路由上的若干 **截然不同** 的 UI 变体,从一个悬浮的底栏切换,加一个 `?variant=` URL 参数。变体必须在结构上不同,而不是在颜色上不同;三套微调的卡片网格是壁纸,而不是原型。它们尽可能在真实页面里渲染,使用真实数据和真实密度,因为在真空里评判的变体永远看起来都还行。

两者都把状态保存在内存中、开始时不需要任何思考、并在每一步之后给你展示完整状态。当你发现自己开始硬化某个东西(加测试、接真正的数据库、为你可能将来要的某个情况做泛化),你已经停止原型了。

## The prototype is a primary source

一个完成的原型留下两样东西,而它们去不同的地方。

**答案**(判决加上它定下来的问题)被持久化捕获:一条 commit message、一份 ADR、或实现的 issue。那是 main 分支保留的、折进真正代码的东西。

**原型** 是答案来源的可运行证据,它不被删。它也不属于 main:那里没有需要维护的东西,而且它腐化得很快。所以它被 commit 到 main 之外的一个 `prototype/<name>` 分支,永不合并,并在实现 issue 上留一个 [context pointer](https://www.aihero.dev/ai-coding-dictionary/context-pointer) 指向那个分支。Main 保持干净;探索保持可被找到,也能被接手它的人重新运行。

## Common questions

**等等,原型不是应该删掉吗?**
曾经是这样:搭出来,留着答案,把代码扔了。最尖锐的反对意见从来不是关于速度的;是关于*下一个[会话](https://www.aihero.dev/ai-coding-dictionary/session)谁来接手,以及他们手上有什么?* 一份原型的散文总结丢掉让原型有说服力的那个东西。所以原型现在被当作 [primary source](https://www.aihero.dev/ai-coding-dictionary/primary-source) 处理:它落在 main 之外的 `prototype/<name>` 分支,实现的 issue 指向它。变化的是代码住在哪里,而不是纪律;它仍然永不合并到 main。

**它以前搭一个终端 app。那个去哪了?**
逻辑分支现在产出一份单独可分享的 HTML 文件。一个终端 app 只能被一个把仓库克隆下来、并且装了 runtime 的人驱动,这恰好排除了原型真正需要其意见的那些人:设计师、PM、知道状态模型本应意味着什么的领域专家。一个双击就能打开、通过邮件还能发的自包含文件,可以由任何人驱动。下面的纯逻辑模块没变,仍然是搬进真正代码的那部分。

**一个 agent 让我 `/prototype`,而我其实应该去 implement。**
已知,这是一个命名问题。`prototype` 是一个通用的、有吸引力的词,对一个不了解 flow 的 agent 来说读起来像 "明显下一步",只要 tickets 存在,它就被按名字推荐,即便设计在对话里已经完全定型。如果你已经知道要构建什么,下一步就是按 ticket 跑 `/implement`。只有当一个具体的设计问题确实还没解决、而交谈解决不了它的时候,才用原型。

**我是不是应该在构建任何 production 特性之前给整个应用做原型(比如,给潜在客户演示)?**
那是披着本技能名字的另一个工件。这里的原型被限定到一个问题,"整个 app 长什么样?" 不是一个。一次全 app 原型没有自然的停止点,所以它凭惯性变成了 production app:清理 pass 永不发生,在原型规则下写的代码(没测试、没错误处理)最终面对用户。如果你需要一个销售 demo,把它明确地当 demo 搭,并明确说明它没有任何一部分是 production。如果你要定一个设计问题,把它切到那个问题。

**我如何在它自己的会话里跑?**
一个原型活在自己的目录里,并生成大量你不想让提出问题的那个线程带着的[上下文](https://www.aihero.dev/ai-coding-dictionary/context),所以在别处跑它,只把答案带回来。[handoff](https://aihero.dev/skills-handoff) 是双向的桥。

**这不就是烧 token 最快的办法?**
可能是,如果你对本来可以靠说话回答的问题做原型,或让一个原型横跨整个特性。值得做的对照不是 token 对零;而是 [tokens](https://www.aihero.dev/ai-coding-dictionary/token) 对搭建一个错误的状态模型、并在它有了 production 调用方之后才发现。把问题保持窄,把 run 保持短,开销就保持在恰当的比例。

## It's working if

- 你能用一句话说出这个原型存在是为了回答什么问题,这句话写在 demo 的顶部,而不是只在你脑子里。
- 一个不读代码的人能驱动逻辑 demo。他们打开文件,按 walkthrough tab 里的按钮,用他们自己的话描述看到的东西。
- 有人说 "wait, that shouldn't be possible" 或 "huh, I assumed X"。那是 *想法* 的 bug,而那正是全部要点。
- UI 变体在布局和信息层级上不同,而不是只在颜色和文案上不同,你得到的反馈是 "B 的头配 C 的侧栏"。
- 一次坐下来就答完了。如果你过一天还在搭,问题就太大了;拆它。
- 当它结束,main 包含的是决策而没有任何原型,并且实现 issue 指向那个仍然保留它的分支。

## Where it fits

`prototype` 是一个 **随时可用的独立工具**:你进入它去定一个设计问题,然后退出,它也是其他技能会跑起来的机制。

它最大的消费者是 [wayfinder](https://aihero.dev/skills-wayfinder)。一个 wayfinder map 由 **decision tickets** 组成,`prototype` 是 ticket 可以是的那四种之一:用于阻塞的问题是 "how should this look" 或 "how should this behave",而无论多少讨论都解决不了。Wayfinder 通过搭一个具体东西供人反应来提升模糊讨论的保真度,而这个技能是那个具体东西被搭出来的方式。一个 prototype ticket 由答案解决,而原型作为资产从 map 链接进来。

其他邻居是这个流程的上游和下游。[grill-me](https://aihero.dev/skills-grill-me) 和 [grill-with-docs](https://aihero.dev/skills-grill-with-docs) 回答可以 grilling 的问题;不可 grilling 的那些来到此处,然后那行一句话答案回到盘问里。下游,一个被验证的状态模型或 UI 方向成为 [to-spec](https://aihero.dev/skills-to-spec) 的已定输入,后者能把原型产出的、决策密集的片段直接内嵌,而不是用散文描述它。其他情况,[ask-matt](https://aihero.dev/skills-ask-matt) 在整套技能中路由。
