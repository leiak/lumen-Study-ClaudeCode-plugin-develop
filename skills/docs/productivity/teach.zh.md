## What it does

`teach` 把你在它里面跑的目录变成一个 standing 的教学 workspace,并在多场[会话](https://www.aihero.dev/ai-coding-dictionary/session)里,以短小的自包含 HTML lessons,教你一个主题。

它不从 [模型](https://www.aihero.dev/ai-coding-dictionary/model) 已经知道的东西教起。[Parametric knowledge](https://www.aihero.dev/ai-coding-dictionary/parametric-knowledge) 被当作不可信:在它教之前,它去找 high-trust 资源,把它们记进 `RESOURCES.md`,并在每个 lesson 里引用它们。另一个结构性的事实是,它是 [stateful](https://www.aihero.dev/ai-coding-dictionary/stateful):任务、资源、lessons 和你已经学到的东西的记录都作为文件活在那个目录里,所以下一场会话从那些文件接续,而不是从上次对话剩下的东西。

## When to reach for it

你通过输入 `/teach` 来调用它;[agent](https://www.aihero.dev/ai-coding-dictionary/agent) 不会自行调用它。

在学习本身就是项目时调用它:一门语言、一个框架、你刚加入的代码库、瑜伽、shader、一份认证。它不是路过的一次解释的工具。

| 你想要 | 该用什么 |
| --- | --- |
| 用几周时间学一个主题,会话之间有累积 | `teach` |
| 在你已经在的那场会话里被解释一个想法 | 直接在那场会话里问 |
| Agent 的上一条消息被重述,因为它没讲明白 | [wait-what](https://aihero.dev/skills-wait-what) |
| 磨利你已经有的思考,而不是获取新材料 | [grill-me](https://aihero.dev/skills-grill-me) |
| 一个后台 agent 去读 [primary sources](https://www.aihero.dev/ai-coding-dictionary/primary-source) 并留给你一份带引用的文档 | [research](https://aihero.dev/skills-research) |
| 学某个 mid-grilling 出现的东西,而不打断 [grilling](https://www.aihero.dev/ai-coding-dictionary/grilling) | 用 [handoff](https://aihero.dev/skills-handoff) 出去到一个 teaching workspace,然后在那里用 `teach` |

## Prerequisites

`teach` 搭一个目录而不是产出文件,而这个技能假设一个 workspace 一个任务,所以在你乐意把整个目录让给一个单一主题的地方跑它。把它放在你工作的项目之外:推荐的家是一个独立的 repo,而不是一个全局的 `~/.learnings/` 文件夹或正在做的项目本身。一个专用 repo 也让 lessons 可被提交,而这正是团队分享它们的方式。

累积在那个目录里的东西:

| 路径 | 容纳 |
| --- | --- |
| `MISSION.md` | 你为什么在学这个。其他一切都挂在它上面;如果它缺失,`teach` 的第一件事就是盘问你,直到它不缺失 |
| `RESOURCES.md` | 它教的时候所引用的被审核过的来源,拆为 Knowledge 和 Wisdom(社区) |
| `lessons/*.html` | 编号 lessons:教的基本单位 |
| `reference/*.html` | 压缩后的 cheat-sheet、算法、术语表:你真正会回头翻的文档 |
| `learning-records/*.md` | 关于你已经 demonstrably 学到了什么的 ADR 式笔记,用来决定下一步教什么 |
| `assets/*` | 可重用组件,从一个共享 stylesheet 开始,所以 lessons 看起来像一门课 |
| `NOTES.md` | 你陈述的教学偏好 |

关于这份清单有两点诚实的话。一个术语表适合多数主题,但这个技能发了一份 `GLOSSARY-FORMAT.md`,而 `SKILL.md` 不再链接它,所以你只有在要求时才会拿到一份([issue #559](https://github.com/mattpocock/skills/issues/559))。而且 workspace 不总是创建在你期望的地方,所以在它上面建一门长课之前,先看下面的第一个问题。

## Storage strength, not fluency

值得去想的词是 **storage strength**:长期保留,对照于 **fluency**,在你正在读的那一阵感受到、且一周后消失的瞬时回忆。`teach` 通过 desirable difficulty 建造前者:retrieval practice、spacing、interleaving。Knowledge 先来,那里 difficulty 是敌人,因为它吃掉你为了理解所需的工作记忆;然后 skill 通过一条紧密的反馈循环被 drill,在那里 difficulty 是工具。

两件事引导你拿到的东西。**任务**(你想要的真实世界理由)给每个 lesson 落地;没有它,lessons 飘向抽象,什么都决定不了下一步。从任务和学习记录出发,`teach` 在你的 **zone of proximal development** 里挑下一个 lesson:足以挑战,而不至于远到无法再学。

这也是为什么这个技能是 push back 而非 oblige。一个需要 **wisdom**(真实世界判断)的问题会被给出一个尝试的回答,然后一个指向你能测试它的社区的指针。一个 quiz 是一道 gate,不是走过场:一位用户报告说了 "thanks a lot",并被告知 drill 还活着。

## Lessons, references and components

一个 **lesson** 是一份自包含的 HTML 文件,够短以在一坐之内完成,与任务挂钩,给出一个切实的赢。它引用自己的来源,推荐一个让你自己去读一遍的 primary source,并链接到兄弟 lessons 和 reference 文档。

值得知道的拆分:lessons 很少被重访,reference 文档是的。所以一段 lesson 的压缩本质(语法表、算法、姿势序列、术语表)属于 `reference/`,而不是埋进介绍它的 lesson 里。

Lessons 由 `assets/` 里的 **components** 搭建:stylesheets、quiz widgets、simulators、diagram helpers。复用是默认。Agent 在写一个 lesson 之前先读 `assets/`,并从已有的东西搭起,而第二个 lesson 能用的任何新东西都被写成 component 而不是内联。共享 stylesheet 是每个 workspace 挣到的第一个 component;它阻止产出变成一堆一次性东西。

## Common questions

**它把文件放在哪里?我的最后落在 `~/.claude/skills`。**
一个真实、open 的 bug ([#377](https://github.com/mattpocock/skills/issues/377))。`SKILL.md` 把 `./` 用于两个不同的根:`./MISSION-FORMAT.md` 等真的就坐在已安装技能里的 `SKILL.md` 旁边,而 `./lessons/`、`./reference/`、`learning-records/`、`assets/` 意在你的目录里。一个把第一种针对技能的安装目录去解析的 agent 接着把第二种也针对那里去解析,然后把你的课程写进技能那个文件夹。检查第一个 lesson 落在了哪里,然后在它上面建之前明确命名目录,而不是依赖 "the current directory" 这种理解。

**我应该留在同一个会话里,还是每堂 lesson 开一个新会话?**
三种方式都 work:留在同一个会话,在一个新会话里重新调用 `/teach`,或在同一文件夹里开一个新会话。每个 lesson 都是它自己的调用。连续性是文件夹,而不是对话。常见做法是在 workspace 里开一个新会话并说 `/teach next lesson for <topic>`。

**我怎么知道它不是在教我编出来的东西?**
不能,不能只凭该技能的话。你读 primary sources。`teach` 单独还不够可靠到可以无核查信任,没有基于 LLM 的技能能。`RESOURCES.md`、每个 lesson 里的引用、每个 lesson 里一份推荐的 primary source 这些基础架构存在,是为了让验证变得便宜,而不是为了消除验证的需要。这个失败不是假想的:一个学 2x2 魔方的用户被给了编造的、不能解它的动作序列。诊断检查单在此类情形下是 model、harness、effort,以及来源是什么。风险在带精确符号的过程性领域最高,在输出可以立即验证的地方最低,比如你能跑的代码。

**正确答案永远是第一个选项。**
被几个人确认,在 Sonnet、Opus 和 GLM 上,仍然未修。`SKILL.md` 现在要求每个答案有同样的字数,这杀掉了一个不同的特征(正确答案曾经是唯一被完整论证的),但没说位置。一位贡献者测了一个针对位置的指令级修复,并报告在九堂 lessons 中正确答案仍 33/33 落在 slot A ([#335](https://github.com/mattpocock/skills/issues/335)),这指向 `assets/` 里一个会打乱的 quiz component 才是真正的修复,而不是更好的措辞。在它发布之前,把答案位置当作无意义。你的 `assets/` 目录是你的,所以请求一份在渲染时打乱的 component 是个合理的本地修法。

**它假设我已经知道一些东西,并用了它从未定义过的术语。**
最常见的实质抱怨。没有评估步骤:`teach` 从任务和学习记录推断你的水平,而在第一场会话里没有学习记录。一份在 wayfinder 流水线里跑它的报告直白说:"It never did grilling to establish my starting point so it made lots of assumptions of what I already knew." 另一位报告 lessons 靠未定义的 jargon,且一份 lesson 是按他们的硬件定制的,讲了硬件能做什么而从未说过它不能。两件事有帮助:在第一条消息里陈述你的先验知识和你的 gap,而当一个 lesson 错过时大声纠正水平,因为纠正成为一份学习记录并引导下一个。一个显式的 knowledge-assessment 步骤是一个 standing 的功能请求 ([#725](https://github.com/mattpocock/skills/issues/725)),不是已发布的行为。

**它做 spaced repetition 吗,它知道什么时候停止教吗?**
第一个不做,第二个不可靠。Spacing 和 interleaving 是 lessons 据以设计的原则,但没有任何东西调度复习,也没有 Anki 或日历集成;两者都是反复的请求。相关的缺口是退出标准:正如一位用户所说,`teach` "is good at making the next lesson, but not as good at knowing when to stop and switch to review or real practice." 如果你想要 review 或 drilling 而不是新材料,开口要;该技能不会主动提议这次切换。

**它只对代码有用吗?**
不,非编码的用途占记录中的更大一份:韩语、日语正式 register、钢琴、吉他、桌游设计、OpenSCAD、电影剧情、Azure 和 CCNA 认证、大学考试、以及八岁和十岁的孩子拿到关于密室和火蝾螈的可打印书。技能里没有任何东西是编程专有的:任务、资源、zone of proximal development 和 drill 在任何领域以同样方式 work。在代码内,被报告最强的用途不是从零学一门语言,而是在一个不熟悉的代码库或新团队的栈里熟悉起来。

**我该跑它用哪个模型?**
没有 canonical 答案,且被报告的差异很大。更高的 [reasoning effort](https://www.aihero.dev/ai-coding-dictionary/effort) 被报告产出明显更好的 lessons。一位用户在 Copilot CLI 上带 Codex 跑同一个技能,得到一张 30 行的 HTML 卡,而 Claude Code 产出完整一课。它未经修改跑在 Claude Cowork,前提是你的组织允许在那里加 skills。如果 lessons 出来很薄,换 model、[harness](https://www.aihero.dev/ai-coding-dictionary/harness) 或 effort,而不是改你的 prompt。

## It's working if

- 它在一个空目录里的第一件事是盘问你为什么想要这个,而不是产出一个 lesson。
- `RESOURCES.md` 在 lessons 之前被填满,每个 lesson 命名一个值得你自己读的 primary source。
- 一个 lesson 里的主张带外链。一个没有引用的 lesson 是该技能凭记忆在教。
- 一堂 lesson 占一坐,让你能做一件你之前不能做的事。
- 在文件夹里开一个新会话说 "next lesson" 让课程继续,而不是重启。
- `learning-records/` 在增长,lessons 不再重复你已经 demonstrably 学过的东西。
- Lessons 看起来像一门课:它们链接 `assets/` 里的 stylesheet,而不是各自带自己的。
- 一个需要判断的问题把你指向一个论坛、subreddit 或课堂,而不仅仅是一个答案。

## Where it fits

`teach` 是一个 **随时可用的独立工具**。它不是任何 build 链中的一个步骤,且与工程 flow 不共享工件;它拥有它的目录,并在主题持续的期间活在那里。

它唯一的真正邻居是 [handoff](https://aihero.dev/skills-handoff),通过 Matt 命名为 "我被 grill 某件我不理解的事怎么办?" 的答案的组合:别停下 grilling 去学:`/handoff` 到一个 teaching workspace,那里用 `/teach` 学它,然后回去从你停下的地方接上。旁边的替代是 [research](https://aihero.dev/skills-research),在你想要一份带引用的文档而不是 lessons 和保留时。当你不确定哪个技能或 flow 合适时,[ask-matt](https://aihero.dev/skills-ask-matt) 在整套技能中路由。
