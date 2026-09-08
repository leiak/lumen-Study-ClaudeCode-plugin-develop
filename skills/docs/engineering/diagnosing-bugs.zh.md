## What it does

`diagnosing-bugs` 针对一个棘手的 bug 或性能回归运行六阶段的诊断:构建复现,缩小范围,对假设排序,插桩,修复并附带回归测试,清理。

它在没有形成**紧密**反馈环之前,不会让 agent 形成理论:必须存在一个已经运行过一次的、命名明确的命令,它在 *此* bug 上变红,在修复后变绿。把一份 bug 报告交给一个 coding agent 时,它的默认行为是读代码然后猜测;这个技能会阻止这种行为。如果不存在能变红的命令,就没有 Phase 2。这道单一的门正是这个技能存在的全部理由。在它之后的所有事情(二分定位、假设检验、插桩)在信号存在之后都是机械性的。

## When to reach for it

输入 `/diagnosing-bugs`,或在任务匹配时 agent 自行调用它:它是模型调用的,并在 "diagnose" / "debug this" 或在某个报告说某东西坏了、抛错了、失败了、或变慢时触发。

在那些硬骨头 bug 上才动用它:看一眼搞不定的 bug、间歇性的 flaky、在两个已知良好状态之间偷偷溜进来的回归。它故意设计得很重,不适合想一条消息就得到答案的问题。

| 你的处境 | 该去哪里 |
| --- | --- |
| 一个你能描述为症状的具体缺陷 | 本技能 |
| 一个有已知前后状态的慢接口或时间回归 | 本技能:它有一条性能分支(测量基线,然后二分) |
| "这个代码库里瓶颈在哪里?",没有具体症状 | 不是本技能。它诊断一个已知故障,不做审计 |
| 别人发来的一份原始 bug 报告,尚未确认或整理 | 先用 [triage](https://aihero.dev/skills-triage) |
| 一次性代码用于回答设计问题,不是追缺陷 | [prototype](https://aihero.dev/skills-prototype) |
| 构建一个计划好的行为,先写测试 | [tdd](https://aihero.dev/skills-tdd) |
| 没有合适的 seam 可以把 bug 锁死 | [improve-codebase-architecture](https://aihero.dev/skills-improve-codebase-architecture):这个技能本身就会交接过去 |

## The tight loop is the skill

Phase 1 投入了不成比例的精力,因为它是唯一真正困难的阶段。该技能给出了一系列构造这个反馈环的方法,大致按优先级排序:

1. 一个失败测试,放在任何能触及 bug 的 seam 上。
2. 针对运行中的 dev server 的 curl 或 HTTP 脚本。
3. CLI 调用加 fixture 输入,与已知良好的 snapshot 做 diff。
4. 无头浏览器脚本,对 DOM、console 或 network 做断言。
5. 重放捕获:一段保存好的请求、payload 或 event log,在隔离环境下走完代码路径。
6. 一次性 harness:系统的一个最小子集,一次函数调用。
7. 属性或 fuzz 循环,用于 "有时输出错误"。
8. 一个二分定位 harness,可以交给 `git bisect run`。
9. 差分循环:同样的输入,老版本对上新版本。
10. [human-in-the-loop](https://www.aihero.dev/ai-coding-dictionary/human-in-the-loop) bash 脚本,作为最后的手段。该技能自带 `scripts/hitl-loop.template.sh` 用于此:agent 运行该脚本,你在终端跟随提示,你输入的回答以可解析的输出形式返回。

*一个*反馈环并不是目标。**紧**才是:快(秒级)、确定性(每次运行结果一致)、精准(断言你具体的症状,而不是 "没有崩溃"),并且 agent 可以无人值守地运行。一个 30 秒还 flaky 的循环几乎等于没有。对于一个只偶尔出现的 bug,目标不是一个干净的复现,而是**更高的复现率**:循环触发条件,并行、加压、注入 sleep,直到 flake 率高到足以对它调试。

当它真的无法构建一个时,它被指示停下来并说明,列出它尝试过的事情,并向你请求[环境](https://www.aihero.dev/ai-coding-dictionary/environment)访问、一段捕获的工件、或临时插桩的许可。它不应该继续去假设。

## The gates between phases

这些阶段是门,不是清单。每一扇门在某个特定条件成立之前都不会打开。

| Gate | 必须为真的事情 |
| --- | --- |
| 进入 Phase 2 | 一个已命名、已运行并粘贴过输出的命令,它能在本 bug 上变红 |
| 进入 Phase 3 | 复现已经成功重现 *并且* 已被最小化:剩下的每个元素都是承重的 |
| 进入 Phase 4 | 3–5 个排好序、可证伪的假设存在,每个都陈述自己的预测,在任何一个被测试之前先展示给你 |
| 进入 Phase 5 | 探针映射到一个具体的预测,一次一个变量,每个 debug log 都带上 `[DEBUG-a4f2]` 这样的标签,使得清理只需一次 grep |
| 完成 | 原始复现已经不再复现,插桩已被清除,并且被证明正确的假设被写进 commit message |

Phase 5 有一个值得知道的逃生口。回归测试在修复之前写,但只有当存在一个**正确的 seam** 时:seam 让测试在调用点处按 bug 实际发生的模式来运行。当唯一可用的 seam 太浅时,该技能被要求说明这一点,而不是写一个提供虚假信心的测试。这种缺失本身就是发现,它把事后分析路由到 `improve-codebase-architecture`。

## Common questions

**它在一些简短问题上触发,而我只是想要一个直接答案。**
这是该技能被报告最多的问题,而且是真实的。尤其是在 GPT-5.6-Sol 上,用户报告它在普通的描述性问题上也触发:"模型反而触发了那个相当正式的 diagnosing-bugs 技能。然后它开始构建复现场景(常常搭一个价值有限的 mock 场景),然后才给我回复或建议。这导致回复延迟严重。" 四个不同的人在 [issue #578](https://github.com/mattpocock/skills/issues/578) 上报告了同样的情况。已接受的修复方案是先采用较轻的方法,只有当问题足够重时才升级到较重的那种,但这个修改还没有落地。这个技能是按 Claude Code 的调用行为校准的;一个[模型](https://www.aihero.dev/ai-coding-dictionary/model)激活阈值较低时会过度触发它。在它升级之前,实际的修复方法是直接说出你想要什么("就回答这个,不要诊断")或者在[harness](https://www.aihero.dev/ai-coding-dictionary/harness)里禁用它的模型调用。

**我能不能指向一个代码库,问它性能问题在哪里?**
不能。它诊断一个你已经能指名的故障。它的性能分支是为带症状的回归准备的(建立基线测量,然后二分,先测量再修复),不是主动扫描。一个用于主动版本的技能被[提议并关闭](https://github.com/mattpocock/skills/issues/431);目前没有对应的技能。

**它在写修复之前会停下来问我吗?**
不会。只有 Phase 3 有人工检查点:排序好的假设列表在被测试之前展示给你,如果你不在,它就按自己的排序继续。插桩和修复之间没有门,所以 agent 可以在你认同根本原因之前开始写代码。[Issue #124](https://github.com/mattpocock/skills/issues/124) 提出加这扇门,仍然 open。如果你想要这扇门,在调用时说明。

**我已经对这份 bug 报告跑过 `/triage`。这会是同一份工作吗?**
部分重叠,而且两个技能都不承认。正如一位读者说的:"Triage 的步骤 3 本质上是 diagnosing-bugs Phase 1–2 的一个浅层、有界实例,但两个文件都没提到对方。" Triage 做一遍 "这到底是不是 bug,表面是什么" 的有界扫描;这个技能做的是彻底版。先跑 triage 不是浪费(它的验证常常给你 Phase 1 的大部分原材料),但这里要正经重做一遍,而且别指望有交叉引用提醒你。

**它粘贴的复现输出会泄露秘密吗?**
可能会。该技能要求 agent 粘贴命令调用和它的输出,并请求诸如 HAR 文件、日志转储、core dump 之类的工件。指令不会对它们做任何脱敏处理。[Issue #674](https://github.com/mattpocock/skills/issues/674) 正好提出了这个问题(凭据、token、cookie、个人数据随之进入聊天、issue 或 PR),并提议加一道脱敏护栏。它是 open 状态、尚未实施。目前把脱敏当成你自己的工作,特别是在输出要发到任何公开场合之前。

**我的安全扫描器把这个技能标记为高风险。**
Snyk 标记它,而且这是误报。它是整个技能集里唯一自带一个可执行 shell 脚本(`hitl-loop.template.sh`)并附带运行指令和 curl dev server 的技能。自带的 `.sh` 加上运行指令再加上对外 HTTP,就足以触发一个静态扫描器。该脚本本身大约 30 行 `read -r -p` 提示,等待人工输入。扫描器评价的是能力面,而不是已证明的漏洞。

**`/diagnose` 怎么了?**
在 v1.0.0 重命名为 `/diagnosing-bugs`。旧名称不再存在。任何链式调用 `/diagnose` 的东西(包装技能、已存 prompt)都需要更新。

## It's working if

- 它在给出任何理论之前先给你展示一个命令和它变红的输出。如果理论先到,说明这个技能没有在运行。
- 它复现的故障就是你所报告的那个,而不是路上顺手发现的一个邻近问题。
- 它在开始猜测之前先缩小复现,并且能告诉你剩下的每一块为什么是承重的。
- 在任何一个假设被测试之前,你看到一个 3–5 个排序好的假设列表,每个都有一个你可以证伪的预测。
- 它加的每个 debug log 都带有 `[DEBUG-a4f2]` 这种标签,而当它宣布完成时,grep 那个标签返回为空。
- commit 或 PR message 指明了哪个假设是正确的。
- 当它没法用测试把 bug 锁住时,它就直说,而不是写一个浅的测试。

## Where it fits

`diagnosing-bugs` 是一个随时可用的独立工具。你在某样东西坏掉时进入,当修复和它的回归测试落地时退出;它不持有状态,不需要预先设置。[ask-matt](https://aihero.dev/skills-ask-matt) 把 "Something's broken" 路由到这里。

两个邻居值得注意。[improve-codebase-architecture](https://aihero.dev/skills-improve-codebase-architecture) 在真正的发现是代码没有 seam 可以把 bug 锁死时接收[交接](https://www.aihero.dev/ai-coding-dictionary/handoff);建议在修复落地之后、掌握了更多信息时提出。[triage](https://aihero.dev/skills-triage) 位于上游,处理从别人那里以原始报告形式到达的 bug,对同样的前两个阶段做一次更浅的版本。
