## What it does

`codebase-design` 固定你用来设计一个模块的词:**module**、**interface**、**depth**、**seam**、**adapter**、**leverage**、**locality**。它精确定义每个词,禁用那些松散的替代("component"、"service"、"API"、"boundary"),并陈述由此衍生的几条原则。

它是一个 reference,不是一个流程。它没有要跑的循环、没有它产出的工件、也没有它问你问题的检查点。每个触碰设计的其他技能都借用它的词汇;它自身只给你这门语言就停。这是你在调用它之前就该知道的事,因为一个没有流程、没有停止规则的技能,如果你把一个[会话](https://www.aihero.dev/ai-coding-dictionary/session)对准它并说 "go",它会自己即兴造出一个。看看下面的常见问题,就能知道实际中那是什么样子。

## When to reach for it

输入 `/codebase-design`,或在任务匹配时,agent 会自动调用它。

当你已经知道要重新设计哪段代码,并需要思考它的形状时调用它:seam 应该放在哪里、interface 能小到什么程度、抽取是否值得。这也是你用来裁定某个词的定义之争的入口。

几个技能和它邻近。要选哪个,取决于实际的问题是什么:

| 问题 | 技能 |
|---|---|
| 单个模块的形状:它的接口、它的 seam、它的深度 | `codebase-design` |
| *领域的词*:"account" 意味着三件事,两个人对 "cancellation" 的理解不同 | [domain-modeling](https://aihero.dev/skills-domain-modeling) |
| 你还不知道 *要* 重新设计哪个模块 | [improve-codebase-architecture](https://aihero.dev/skills-improve-codebase-architecture)(用来发现候选者的调查) |
| 你想要设计被辩论,而不只是命名 | [grilling](https://aihero.dev/skills-grilling) |
| 有一个具体行为要构建,你希望测试能在重构后存活 | [tdd](https://aihero.dev/skills-tdd) |

## The vocabulary

这份词汇表就是这个技能。每个术语都相对于其他术语定义,并且每个都附上它要替代的词。

| 术语 | 它意味着什么 | 不要说 |
|---|---|---|
| **Module** | 任何有接口和实现的东西。刻意与规模无关:一个函数、一个类、一个包、一个跨层级的 slice。 | unit, component, service |
| **Interface** | 调用方为了正确使用它必须知道的一切:类型签名,加上不变量、顺序约束、错误模式、必需的配置、性能特征。 | API, signature |
| **Depth** | 接口处的 leverage:调用方或测试每学习一份接口可以行使多少行为。**Deep**:少量接口背后藏着大量行为。**Shallow**:接口几乎和实现一样复杂。 | 无 |
| **Seam** | Michael Feathers 的术语:可以在不修改该处的情况下改变行为的地方。它是一个接口的 *位置*,把它放在哪里本身就是一个决策,和它背后放什么是分开的。 | boundary |
| **Adapter** | 在 seam 处满足某个接口的具体事物。命名一个角色而非一种实质:内存 fake 和 Postgres repo 都是 adapter。 | 无 |
| **Leverage** | 调用方从 depth 得到的东西:每学一份接口就得到更多能力。 | 无 |
| **Locality** | 维护者从 depth 得到的东西:变更、bug 和验证集中在同一处。修一次,处处修复。 | 无 |

Depth 故意 *不* 用实现行数与接口行数之比来定义,那是 Ousterhout 自己的定义。那种度量会奖励给实现掺水。采用 depth-as-leverage。

## The four principles

- **Depth 是接口的属性,不是实现的属性。** 一个深度模块内部可以由若干可替换的小部分搭建而成。它们就是不外露给调用方而已。一个模块可以有自己的测试所用的内部 seam,以及它接口处的一条外部 seam。
- **The deletion test.** 想象删除这个模块。如果复杂性消失,它就是 pass-through。如果复杂性在 N 个调用方那里重现,它就是值得保留的。
- **接口就是测试面。** 调用方和测试穿越同一条 seam。如果你想 *穿过* 接口去测试,那这个模块的形状就是错的。
- **一个 adapter 意味着一个假设的 seam。两个 adapter 才意味着一个真正的 seam。** 直到真有东西在 seam 两侧变化,不要切。一边一个 adapter 的 seam 只是多一层间接。

两份支持性文件走得更远,且该技能按需而非预先读取它们。[DEEPENING.md](https://github.com/mattpocock/skills/blob/main/skills/engineering/codebase-design/DEEPENING.md) 把候选者的依赖分成四类(进程内、本地可替换、远端但拥有、真正外部),因为类别决定了加深后的模块如何跨 seam 被测试。[DESIGN-IT-TWICE.md](https://github.com/mattpocock/skills/blob/main/skills/engineering/codebase-design/DESIGN-IT-TWICE.md) 启动并行的 [sub-agents](https://www.aihero.dev/ai-coding-dictionary/subagent),为同一模块产出三种或更多截然不同的接口,然后在 depth、locality 和 seam 位置上比较它们。

## Common questions

**我实际上如何在 TypeScript 中构建一个深度模块?**
这是这个技能被问得最多的问题,而该技能不回答。它定义了什么是深度模块;对如何阻止一个多余的 import 越过接口,它只字未提。[Issue #458](https://github.com/mattpocock/skills/issues/458) 把这一点说得很清楚:"let's say we're happy with the interface, it hides the details, etc. But how do we enforce it? I think without linting or clear guardrails, humans and LLMs alike will start making it messy over time." Matt 在该 issue 里的回答是三种方案:用一个 class 或 IIFE 包住它,并接受这个 class 会变得巨大;在 monorepo 里把它做成一个 package,并接受 monorepo 工具链;或者用一个像 [dependency-cruiser](https://github.com/sverweij/dependency-cruiser) 这样的 linter 来禁止绕过接口的 import。他还另外说过,Effect 是最佳机制,dependency-cruiser 是次佳。仓库 `in-progress/` 里有一个 `setup-ts-deep-modules` 技能,它落地了 `src/packages/<name>/index.ts` 这种约定,但它是一个 beta 通道的技能,没有文档页,也没有随它发布 lint 规则。

**我把一个会话指向它,它烧掉 100k [tokens](https://www.aihero.dev/ai-coding-dictionary/token) 在重做一些我根本没问过的东西。**
已知,被记录为 [issue #449](https://github.com/mattpocock/skills/issues/449)。这个技能是模型调用的,把自己描述为词汇,但里面没有任何东西会硬性阻止 agent 把它当作可运行的流程。被告知 "resume in /codebase-design and drive the open decisions",agent 会伸手去抓它能找到的最像动作的内容:`DESIGN-IT-TWICE.md` 里的并行 sub-agents。它重新探查前一会话已经绘制过的代码,跑了很远才问你任何东西。driver 技能所拥有的护栏(检查点、一次一个问题、不自动推进)这里一个都没有,因为一个 reference 不需要任何东西。可行的变通方法是命名一个 driver 技能,让这一个垫在它下面:`/grill-with-docs`、`/improve-codebase-architecture` 或 `/tdd`,让 `codebase-design` 当作词汇。该 issue 仍然 open。

**`design-an-interface` 去哪了?有没有一个 `/interface-design` 技能?**
`design-an-interface` 已被移除并吸收进本技能。没有任何东西丢失:它的 "design it twice" 技巧(并行 sub-agent 生成截然不同的设计,源自 Ousterhout)作为 `DESIGN-IT-TWICE.md` 出现在这里。另有人请求一个专用的 `/interface-design` 技能服务于"深度模块/薄接口"理念;那个理念已经在这里,没有计划再开一个独立的技能。如果你来找这两个名字,本页就是了。

**这难道不是文件结构约定,比如目录、barrel 文件、feature slice 之类吗?**
不是,该技能在多次施压下仍坚持这条线。[Issue #95](https://github.com/mattpocock/skills/issues/95) 提议把形式化的 fractal-tree 文件结构作为深度模块的具体实现;回复是这两件事正交:"deep modules are about the design of the interface and accessing through a strict interface, no matter what the file system looks like. It seems perfectly possible that you could have shallow modules with this approach." 在 #458 也提了同样的意思:"I think you might be tying the concept of modules too closely to the file system. The file system can certainly be a useful hint to the shape of modules, but there's no need to use the file system in the construction of deep modules." 词汇表刻意把 **module** 定义成与规模无关。

**`tdd` 真的使用了这套词汇吗?**
是的,从某个时间点开始是的。曾经有段时间没有。在 v1.0 里,`tdd` 里以前的内联深度模块笔记被删除,换成这个共享技能,但替代它们的指针一直没加,所以 `tdd` 自己定义了 "seam" 却什么都没引用。这个缺口已经补上:该指针现在在技能里,当接口形状本身是开放的问题(而非测试)时触达。`tdd` 仍然把 "seam" 看作测试所穿越的边界;本技能拥有 seam 背后模块的形状。

**design-it-twice 这种模式在 Claude Code 之外也能用吗?**
不太干净。`DESIGN-IT-TWICE.md` 写的是 "spawn 3+ sub-agents in parallel using the Agent tool",这是 Claude Code 的[工具](https://www.aihero.dev/ai-coding-dictionary/tool)名。仓库为其他 [harnesses](https://www.aihero.dev/ai-coding-dictionary/harness) 也发了元数据,包括 Codex,但这些 harness 在那个名字下可能什么也没暴露,所以并行设计阶段的可移植性比元数据暗示的要差。已记录在 [issue #564](https://github.com/mattpocock/skills/issues/564),open。

**我能不能把自己的概念加进词汇表,比如 connascence、module secrets、[progressive disclosure](https://www.aihero.dev/ai-coding-dictionary/progressive-disclosure)?**
人们确实提出过这些。[Issue #180](https://github.com/mattpocock/skills/issues/180) 添加 Parnas 的 module secrets 和 Page-Jones 的 connascence 作为对 *什么* 正在跨 seam 泄露的一层命名,并附带一份能 work 的 diff;[issue #303](https://github.com/mattpocock/skills/issues/303) 提议在实现内部做 progressive disclosure,这样深度模块的公共接口下面不是一个未分化的整块。两份都 open 未合并。已发布的词汇表刻意保持小,而它保持小的原因在技能自身里有陈述:一致的词汇是全部意义,一个没人一致使用的术语比没有术语更糟。

## It's working if

- 设计对话不再产出 "component"、"service"、"boundary",而开始产出 "module"、"interface"、"seam"。
- 有人能指着某个提议的抽取说它是否通过 deletion test,而不打太极。
- 一个提议的 seam 伴随第二个被命名的 adapter,而不只是第一个。
- 对接口的讨论覆盖不变量、顺序和错误模式,而不仅是类型签名。
- 调用它不开启一个会话。如果 agent 在只基于 `/codebase-design` 就开始读文件、提议重构,那它把这个 reference 当成了 driver。

## Where it fits

`codebase-design` 是一个 **随时可用的独立 reference**,是工程类技能的词汇底层,而非任何链中的一个步骤。它最接近的近邻是 [domain-modeling](https://aihero.dev/skills-domain-modeling),后者是面向 *问题域* 词汇的对应 reference,而本技能面向模块的形状。两者通常一起想要,因为把一个深度模块命名好需要这两者。[improve-codebase-architecture](https://aihero.dev/skills-improve-codebase-architecture) 是另一个:它扫描一个代码库寻找加深的候选者,并把每一个候选者用本词汇表写出,所以它找出模块,而本技能是你设计它的工作台。当你不确定哪个技能或流程合适时,[ask-matt](https://aihero.dev/skills-ask-matt) 来路由。
