## What it does

`research` 通过阅读持有答案的来源来回答一个问题,然后在仓库里留下一份带引用的 Markdown 文件。它只基于 **[primary sources](https://www.aihero.dev/ai-coding-dictionary/primary-source)** 工作:官方文档、源代码、规范、第一方 API。它把每一条主张都追到持有它的来源,所以当 API 自家的文档可达时,它不会复述一份博客文章对它的描述。

它不在对话里回答你。产出是一个文件,写在仓库已经存放这类笔记的地方,每条主张配一个链接。这就是重点:你能对它做出反应、把它交给另一个 agent、或丢掉它,而不是一个[会话](https://www.aihero.dev/ai-coding-dictionary/session)结束时一起消失的答案。

## When to reach for it

输入 `/research`,或当任务变成阅读腿子活时,[agent](https://www.aihero.dev/ai-coding-dictionary/agent) 自动调用它。

当下一步是 *从工作目录之外找出某件事*(一个第三方 API 的行为、一份规范实际说的是什么、一个版本说法是否站得住),而且你不想让阅读拖垮你自己的线程,就使用它。你需要什么决定哪个技能:

| 你需要 | 调用 |
| --- | --- |
| 一个决策正在等的一个外部事实 | `research` |
| 一个 *与你一起* 做出的、靠盘问的决策 | [grilling](https://aihero.dev/skills-grilling) |
| 一项持久的架构决策,写进 `CONTEXT.md` 和 ADR | [grill-with-docs](https://aihero.dev/skills-grill-with-docs) |
| 找出一个做法在你代码库里能不能 work | [prototype](https://aihero.dev/skills-prototype) |
| 大到无法塞进一个会话的计划 | [wayfinder](https://aihero.dev/skills-wayfinder) |

`research` 和 `grill-with-docs` 之间的分界线在于 **产出物的保质期**。Research 产出短期资产:这份库本周的认证机制是怎样的。一份 ADR 记录你保留的一项决策。如果你在产出的是决策而不是事实,你在 [grilling](https://www.aihero.dev/ai-coding-dictionary/grilling),不是在 research。

## Delegated legwork

定义性的动作是,阅读作为一个 **后台 agent** 跑。你继续工作;它跑出去,沿着每条主张追到它的 primary source,写一份 Markdown 文件,然后回报。Research 是你委派的腿子活,不是外包的思考:你拿到一份文件,可以对它 grill、做规划、或对着它做设计,而决策仍然由你做。

委派是不设防的,而且后台 agent 可以再生成一个后台 agent。这是该技能被记录最多的粗糙边。

文件落在哪里由仓库决定,而不是由技能决定:它匹配仓库已有的笔记约定,如果一个都没有,它挑一个合理的位置并告诉你。一次 run 写一份文件。

## Common questions

**它又生成了第二个 research agent。这是故意的吗?**
不是。这是一个 open bug, [issue #530](https://github.com/mattpocock/skills/issues/530)。该技能告诉它的调用者起一个后台 agent 但不限制 agent 类型,所以它生出来的 agent 拿着 `Agent` 工具和同一份指令的 `general-purpose` agent,会再次触发。一位报告者测量了一次 research 任务跨三次重叠运行大约花了 450k [tokens](https://www.aihero.dev/ai-coding-dictionary/token),重复的那一份在半小时后才完成,完全在视野外。在 Claude Code 之外也能复现;同样的嵌套在带 GPT-5.6-sol 的 Codex 上被确认。没有随版本发布的修复。用户在他们自己的安装副本上加了一行:已经是一个 [subagent](https://www.aihero.dev/ai-coding-dictionary/subagent) 的 agent 自己去做这件事,这有点帮助但只是指令层,不是结构性的。调用之后留意你的后台任务列表,停掉那个重复的。

反过来也有失败:如果你自己的全局禁止 agent 再委派工作,那个后台 agent 会礼貌地拒绝任务,这个技能就悄悄什么都不做。

**文件应该放在哪里,我应该 commit 它吗?**
该技能把文件放在仓库已经放笔记的地方,除此之外没有意见。社区的一个意见相当定:ADR 留着,research 文件不留。最尖锐的版本来自一个 Discord thread,正好讨论这个问题:"ADRs yes. Everything else archive or delete after done. It otherwise becomes cruft of work and can poison future repo reads if you've drifted away from the spec/research." 一份 research 文件记录的是它被写下那天什么是真的,所以一份过期的比没有更糟。总的来说这些工件不该进 git,而且也没有规范的位置:人们用 Obsidian、一个独立的知识仓库、或 issue tracker。

**什么算 "high-trust" 的 primary source,由谁决定?**
[模型](https://www.aihero.dev/ai-coding-dictionary/model) 决定。该技能命名了合格的来源 *种类*(官方文档、源代码、规范、第一方 API),没有白名单、没有域名门槛、也没有验证 pass。这在技能被首次提议时就是最大的反对意见,而且从未被公开回应:"Five research subagents pointed at junk just gives you five confident wrong answers faster. How are you gating what counts as high-trust sources?" 你真正拥有的缓解措施是每条主张上的引用。随机追两三条。如果它们落在一份对那个东西的总结上而不是那个东西本身,这次 run 在它唯一的工作上失败了。

**后来的会话会不会复用先前 run 发现的东西?**
不会。没有任何东西会自动加载过去的 research 文件;它就是一份坐在仓库里的文档,直到人或某个技能指向它。这在早期被作为对设计最强有力的挑战被提出:"the value's the markdown becoming context the agent re-reads later, not the fetch itself. A write-once dead file is just a fancy search." 已发布的技能不解决这个问题。实际上,这份文件通过被有意识地喂给下一步来挣回自己的价值:贴进 spec、引用到 grilling session、让一张 [ticket](https://www.aihero.dev/ai-coding-dictionary/ticket) 指向它。

**为什么不直接让 agent 去读文档?**
你可以,一段两行的 prompt 就是这个技能所替代的实践。这个技能相对于那段 prompt 买来两件事:它在后台跑,所以你的会话保持[上下文](https://www.aihero.dev/ai-coding-dictionary/context) 干净,以及 primary-source 约束和带引用的输出每次都按同样的方式出来,而不是看你恰好怎么措辞。相对于 [harness](https://www.aihero.dev/ai-coding-dictionary/harness) 自家的 deep-research 模式,差别在工件和来源纪律,不在搜索。如果一段两行 prompt 在一个简单问题上就能给你要的,就用那段两行 prompt。

**它什么时候停止阅读?**
技能里没有停止标准,这以两种看似相反但其实是同一种缺口的形式出现:跑得过深的 agent,以及覆盖面很广却漏掉真正要紧的那项具体细节的 agent。一位实践者这么说:"deep-research skills are a bit too deep sometimes. And telling an agent to research usually results in missing crucial details." 范围划定是你的事。一个窄的、可回答的问题(一个 API、一个行为、一个版本说法)远比 "research X" 回来得好。

**`/wayfinder` 创建了 research tickets。我要自己解决那些吗?**
不用,它现在替你去跑。在 v1.1 之后未发布的变更中,一个 charting 会话为每个 research ticket 起一个 `/research` subagent,并行地把它们烧掉,在一条 `research/<name>` 分支上捕获发现,带一个从 ticket 出发的 [context pointer](https://www.aihero.dev/ai-coding-dictionary/context-pointer)。Research tickets 是 wayfinder 的"一个 ticket 一个会话"规则之外的唯一例外,因为它们是 [AFK](https://www.aihero.dev/ai-coding-dictionary/afk):没有什么在等者你。这些分支有两个已知的卡点:subagent 已被看到从一条永远不该合并的分支开一份 draft PR ([issue #576](https://github.com/mattpocock/skills/issues/576)),以及之后删除这条分支会破坏 tickets 持有的 context pointer。

## It's working if

- 你自己的会话保持继续。如果你在那坐着看它读,委派就没发生。
- 正好出现一个新的后台任务。一个名字几乎相同的第二个就是嵌套那个 bug。
- 在仓库已经用来放笔记的那个文件夹里出现一个新的 Markdown 文件,而且 agent 把路径告诉你。
- 每条主张带一个链接,随机追两个落在一份官方文档、一份规范、或真正的源文件上,而不是某人对它的介绍。
- 你能只凭这份文件、不再回去看来源,做出你卡住的那项决策。

## Where it fits

一个随时可用的独立工具,它喂养思考类技能而不是坐在 build 链里。它的文件是 *被带进* flow 的东西:[grilling](https://aihero.dev/skills-grilling) 和 [grill-with-docs](https://aihero.dev/skills-grill-with-docs) 在事实已经在桌上时问得更锐利,[to-spec](https://aihero.dev/skills-to-spec) 能对着它合成。[wayfinder](https://aihero.dev/skills-wayfinder) 是直接调用它的那个技能,在 map 上为每个 research ticket 派一个 `/research` subagent。对于整张地图,见 [ask-matt](https://aihero.dev/skills-ask-matt)。
