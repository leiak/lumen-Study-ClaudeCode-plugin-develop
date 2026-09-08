## What it does

`handoff` 把你在的那段对话压缩成一份 **handoff document**:一份 markdown 文件,写到你的 OS 临时目录而不是 workspace,这样一个全新的 [agent](https://www.aihero.dev/ai-coding-dictionary/agent) 能读它把工作接过去。

它买来的是 **portability**,而不是 compression。这让这个技能比听上去要窄。你只需要一份文件当工作必须 *迁移* 时:迁到一个新 [harness](https://www.aihero.dev/ai-coding-dictionary/harness)、新目录、同事、或你想在 mid-phase fork 出来的一个侧任务。如果没有什么在迁移,你不需要 handoff:留在 [会话](https://www.aihero.dev/ai-coding-dictionary/session) 里、`/clear`、一个 [subagent](https://www.aihero.dev/ai-coding-dictionary/subagent) 以及 `/compact` 涵盖了普通的阶段结束情况,而 `/compact` 比这个技能涵盖得更频繁。

## When to reach for it

你通过输入 `/handoff` 来调用它;agent 不会自己调用它。传一段关于下一个会话是做什么的笔记,文件就为它写好。

四种处境是全部触发:

| 处境 | 为什么需要一份文件 |
| --- | --- |
| 换 harness(Claude → Codex) | 新的 harness 看不到旧的[上下文](https://www.aihero.dev/ai-coding-dictionary/context) |
| 移到不同的目录或仓库 | prototype 目录是常见情况 |
| 把工作发给同事 | 他们需要能读的东西 |
| Fork 一个 mid-phase 找到的侧任务 | 你继续工作;第二个 agent 接 fork |

对其他任何事(同一个 harness、同一目录、你 done [grilling](https://www.aihero.dev/ai-coding-dictionary/grilling) 要去实现),`/compact` 是动作。[ask-matt](https://aihero.dev/skills-ask-matt) 在阶段边界承载关于全部五个选项的有序决策树。

## Branching is the use people skip

技能的描述读起来像会话恢复:写一份总结,在这里结束,在那里继续。这样读起来像一份更差的 `/compact`,所以它会被扫过去。Fork 那个情况是值得知道的。你 **留在你的会话里** 并把累积的上下文交给第二个并行工作的 agent。

那就是经过 [prototype](https://aihero.dev/skills-prototype) 的分叉绕路所用的。你深陷一场设计对话,你撞上一个只有跑代码才能定夺的问题,而你不想花你搭建起来的那个线程去找出来。交接给一个 prototype 会话,拿到答案,把答案交回来,并从原始线程引用它。两次穿越,一场活的对话,没有东西被重解释。

阶段边界的五个选项中三个保留不同的东西:`/compact` 保留你的意图,`/clear` 一无所留,`/handoff` 保留工作迁移的能力。

## What travels, and what doesn't

这份文件承载活的线程(在飞的、为什么、下一步是什么),加一个 **suggested skills** 段,命名下一个 agent 应该触达的内容。Secrets 在写之前被脱敏。

它刻意不携带任何已经写下来的东西。Specs、plans、ADRs、issues、commits 和 diffs 被引用为路径或 URL,从不被复制。这保持文件小巧,且把已经定下来的细节留在一个地方而不是两个会漂移的地方。

## Common questions

**Handoff 还是 compact?**
除非有什么在迁移,否则是 `/compact`。留在同一个任务上是 compact,不是 handoff:同一个 harness、同一目录、而你还得保持循环,这是阶段边界树多数日子落下的地方。`/handoff` 的优势不是它总结得更好;而是结果是一份你能携带到 `/compact` 走不到的地方的文件。

**所以 compact、clear 和 handoff 之间的实际区别是什么?**
被保留的是三件不同的事。`/compact` 压缩这份上下文并在新的窗口里让你继续:意图存活。`/clear` 清空窗口,从零开始:在身后一切可丢弃时正确,如果不是则单向。`/handoff` 写一份可携带的文件:工作活过迁移到别处。注意三件事都把一份 **[primary source](https://www.aihero.dev/ai-coding-dictionary/primary-source)**(实际发生的对话)变成一份 **[secondary source](https://www.aihero.dev/ai-coding-dictionary/secondary-source)**(它的总结)。继续是唯一一个不做这件事的动作,所以它是第一个该排除掉的。

**我的 handoff 文件去哪了?**
临时目录,这是这个技能被报告最多的摩擦:路径长,因 OS 而异,在 Windows 上 agent 有时要试好几次才找到对的那条。要回路径并在继续之前记下它。Temp 是刻意的:handoff 是一份 transit 文档,不是你来维护的工件。它也不是耐久的那一种;见下一题。

**我的 handoff 在会话之间消失了。**
有些环境在会话之间清空 temp(报告中的情况是 Codex),而 `/private/tmp` 在重启时会消失。如果下一个会话不是在一小时内启动,或是在不同 harness 下启动,在文件被写好之后自己把它复制到耐久的地方。同样的原则适用于这份文件 *指向* 的任何东西:一份调度引用了 temp 里的其他文件,就是一份下一个 agent 跟不上的调度。

**我到底怎么把它交给下一个 agent?**
打开新会话并把路径指给它:读这份文件,然后继续。指向文件,而不是把总结粘进 shell 命令:含有反引号或 `$(...)` 的总结在被插入到 `claude "<summary>"` 时被搞坏,常见的失败是静默截断而不是错误,所以新 agent 拿着一份悄悄不完整的简介开场。

**这和 `/branch`、`--fork-session`、或内置的 `/handoff` 一样吗?**
类似但不同,而且 `/branch` 在这里不是一个已发布的技能;`/handoff` 是 canonical 的名字。Fork 继承一份 context 的精确副本;这个技能产出的是一份面向一个陈述的下一任务的 *有针对性* 压缩,放在一份文件里。在 fork 能做(同一台机器、同一 harness、同一目录)的地方,fork 是更少的工作。文件在目的地是 fork 去不了的那一刻胜出。

**什么时候某样东西应该放在 `CLAUDE.md` 里?**
问它下个月是否仍然为真。`CLAUDE.md` 是关于项目的 standing context,被无差别地加载到每个会话里。Handoff 是关于一项在飞的工作,且在该工作落地之后就死了。反复被重解释的事实是 `CLAUDE.md` 的问题;一项半成品的任务是 handoff。

**它捕获了 what,而不是 why。**
公平且被反复批评。有两件事有帮助。传那个争论(告诉它下一个会话是为什么),这样与 *那个* 相关的推理被保留而不是被压平。留意那些会话从未真正核实过的自信说法:"X isn't built"、"Y is done"。下一个 agent 把这份文件当作契约,不会重新核对,所以一条被写成事实的信念变成之后一切的虚假前提。在你交接之前读这份文件,并 downgrade 任何你只是假设的东西。

**为什么它是一个技能而不是一个 slash command?**
两者都 work;它们适合不同情境。作为技能,它通过与其他一切相同的安装路径发布和更新,这正是让它可分享的原因;agent 不会自己触发它的约束由 frontmatter 而不是机制来设定。

## It's working if

- 这份文件是对话的一小部分,而 specs、issues 和 diffs 在其中以路径和 URL 的形式出现,而不是被复制的文本。
- 你能冷读它,原始会话没打开,就知道下一步做什么。
- 新 agent 立刻开始工作,而不是让你重解释 setup。
- 在 fork 的情况下,当你回来时,你的原始会话还在那里,没被动过。
- Suggested-skills 段命名你自己会想触达的技能。
- 其中没有 key、token 或密码。

## Where it fits

`handoff` 是一个 **随时可用的独立工具**,活在会话之间的接缝处而非任何构建链内,但它是一个窄的,而诚实的地图是,你用的频率会比阶段边界的其他四个选项都低。它最近的邻居是 [prototype](https://aihero.dev/skills-prototype),因为一个 prototype 住在它自己的目录里,出去再回来的往返正好是这个技能为之而生的穿越。当你站在边界,不确定是继续、clear、handoff、委派还是 compact 时,[ask-matt](https://aihero.dev/skills-ask-matt) 承载那份给这五者排序的树,并在你剩下的那套里路由。
