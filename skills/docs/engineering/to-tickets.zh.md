## What it does

`to-tickets` 接收一份计划、一份 [spec](https://www.aihero.dev/ai-coding-dictionary/spec)、或你所在的对话,把它拆成一组你 issue tracker 上的 **[tickets](https://www.aihero.dev/ai-coding-dictionary/ticket)**。每个 ticket 声明它的 **阻塞边**:在其他 ticket 完成之前不能开始的那些 ticket。

每个 ticket 都是一条 **tracer bullet**:一条穿过变更每一层(schema、API、UI、tests)的窄而完整的路径,落地的瞬间就可以独立 demo。这正是让它和显然那种拆活方式表现不同的约束,后者是一层一层切、最后再集成。它还把每个 ticket 的大小调整到正好装进一个全新的[上下文窗口](https://www.aihero.dev/ai-coding-dictionary/context-window),因为会接 ticket 的东西是一个从未见过你 spec 的[会话](https://www.aihero.dev/ai-coding-dictionary/session)。

## When to reach for it

你通过输入 `/to-tickets` 来调用它。[agent](https://www.aihero.dev/ai-coding-dictionary/agent) 不会自行调用它。

| 你在哪 | 该跑什么 |
| --- | --- |
| 你有一份 spec issue,且构建跨多个会话 | `/to-tickets`,或 `/to-tickets #<spec_issue>` |
| 计划只在对话里,从没写下来 | `/to-tickets` 直接读这段对话,不需要 spec |
| 整个变更能装进一个上下文窗口 | [implement](https://aihero.dev/skills-implement),跳过 tickets |
| 什么都还没决定 | [grill-with-docs](https://aihero.dev/skills-grill-with-docs),然后 [to-spec](https://aihero.dev/skills-to-spec) |
| 一张 [wayfinder](https://aihero.dev/skills-wayfinder) map 已清 | 先 [to-spec](https://aihero.dev/skills-to-spec) 折叠 map,然后 `/to-tickets` |

`to-tickets` 产出的 tickets 在构造上对 agent 是就绪的。不要对它们跑 [triage](https://aihero.dev/skills-triage)。Triage 是给别人送来的工作用的。

## Prerequisites

`to-tickets` 发到 tracker 里,所以 [setup-matt-pocock-skills](https://aihero.dev/skills-setup-matt-pocock-skills) 必须已为本仓库配置好 tracker 以及 triage-label 词汇。两种都行:像 GitHub 或 Linear 这样的真实 tracker,或 `.scratch/` 下的本地 Markdown 文件,后者开箱支持。

## Tracer bullets, not layers

一个 **horizontal** 切片发出一层变更。直到每一层都落地才有东西 work,而且每张 ticket 的验收标准不得不伸进另一张 ticket 的工作里。一条 **vertical** 切片(tracer bullet)同时穿过所有层发出一条细路径,所以它能独立验证,且拥有它所判定的所有东西。

这是人们最常打破的规则,后果有据可查。一个团队跑了一个 26 ticket、按层切(corpus、producer、aggregator、selector)的栈,每个关闭的 ticket 大约 20 次 agent run,大约四分之三是在返工。他们自己的事后分析把每一类失败都追到水平切分,而不是实现本身。

在发布任何东西之前发生两件事。`to-tickets` 寻找 prefactoring("make the change easy, then make the easy change" 这条原则),并把那项工作排到最前。然后它把拆解作为一个编号列表呈现给你并向你提问:粒度对不对、阻塞边是否真实、有没有要合并或拆分的。在你批准之前,什么东西都不会到 tracker,而那个提问环节就是推回去的入口。

## Blocking edges

边是这份工件的重点。它们按 tracker 不同有两种读法:

| Tracker | 边住在哪里 | 你怎么处理 |
| --- | --- | --- |
| 本地 markdown | 在 `.scratch/<feature>/issues/<NN>-<slug>.md` 下,每个 ticket 一份文件,按 blocker-first 编号 | 自上而下,手工 |
| 真实 tracker(GitHub、Linear) | 原生阻塞链接,或在 tracker 有子 issue 时使用子 issue | 任何 blocker 都关闭的 ticket 处在 **frontier** 上,可以被接 |

无论哪种方式,边都活在 ticket 里。介质只决定有没有东西能并行地作用于它们。`to-tickets` 产出工件;跑它(一次一个会话,或一队)是 *你* 的事,不是技能的事。

## The wide-refactor exception

有一种形状打破 tracer-bullet 规则。**wide refactor** 是一次单一的机械变更(改列名、重打一个共享符号的类型),其 **blast radius** 横扫整个代码库,所以一次编辑会破坏成千上万的调用点,没有纵向切片能落地为绿。

`to-tickets` 把那种情形排成 **expand–contract**:

- **Expand**:把新形式加在旧形式旁边,这样不会打破任何东西。
- **Migrate**:按 blast radius(每个 package、每个目录)分批移动调用点,每批一张 ticket,每张都被 expand 阻塞。CI 保持绿,因为旧形式仍然存在。
- **Contract**:在没有调用方剩下时,在被每张 migrate 批次阻塞的一张 ticket 里删掉旧形式。

即使在那些批都无法单独保持绿的地方,它们共享一条 integration branch,并全部阻塞一张最终的 integrate-and-verify ticket。绿只在那里承诺。

## Common questions

**它为一个三行变更产出了十二张 ticket。**
过度拆解是这个技能被报告最多的摩擦,在实践者之间是一致的:[模型](https://www.aihero.dev/ai-coding-dictionary/ticket) 默认用 atomic 单位,并丢掉了让它们有意义的归并。提问步骤正是为此而生:让它合并,它会。更深的答案是,tickets 有一个下限:如果整个变更能装进一个上下文窗口,你根本就不需要这个技能。直接走 [implement](https://aihero.dev/skills-implement)。

**Tickets 出来的是每层一张:全部 schema 在一张,全部 API 在另一张。**
这是纵向切片规则写出来要反对的失败,而这个技能有时还是会产出。在提问步骤用一个每张 ticket 的问题抓它:这事做完我能 demo 什么?一张没有答案的 ticket 是一张水平切片。有些人因此给每张 ticket 加一行 "demo path",报告说它把模型推向纵向分解。

**在 GitHub 上这些 tickets 没被建为 spec issue 的子 issue。**
已知,未修。已在十几次运行、多个模型上报告过,[最完整的是 issue #554](https://github.com/mattpocock/skills/issues/554),Codex 上比 Claude 上更糟。`gh` 自 v2.94 起原生支持:`gh issue create --parent <n>`,以及事后的 `gh issue edit <parent> --add-sub-issue <n>`。在 tracker 模板偏好这些之前,在一份 run 之后手工把父链接接上是可靠的变通。

**"Blocked by" 被写进了 issue body,而不是一个真实的阻塞链接。**
同类问题,被记录在 [issue #513](https://github.com/mattpocock/skills/issues/513),里面那个 agent 甚至断言 GitHub 根本没有原生阻塞关系。其实有:`gh issue create --blocked-by 12,15`。由于 blockers 先发布,它们的编号在创建时总是可用的。Body 文本只是给没有原生边的 tracker 的回退,而不是默认。

**本地 tickets 放哪里?v1.1 注释说的是一个根级别的 `tickets.md`。**
是说过,那是个 bug:并行 agent 写入时,单个共享文件会竞争。本地模式现在按依赖顺序,每个 ticket 一份文件,写到 `.scratch/<feature-slug>/issues/<NN>-<slug>.md`,匹配本地 tracker 模板已经描述过的布局。`NN` 前缀是一个真实的 ticket ID,所以 `/implement 03` 能 work,而不是重打一段长标题。

**它试图读我的 spec 时一直截断。**
非常大 spec 可能长得超过一个 tracker issue 能干净返回的东西,且没有本地副本可以兜底,所以 agent 烧 [tool calls](https://www.aihero.dev/ai-coding-dictionary/tool-call) 反复取块,永远到不了尾。在 `/to-spec` 和 `/to-tickets` 之间不要 [clear](https://www.aihero.dev/ai-coding-dictionary/clearing) 或 [compact](https://www.aihero.dev/ai-coding-dictionary/compaction)。在同一个上下文窗口跑它们,spec 根本不需要被重新拉取。

**验收标准什么也没判:有些在任何工作做之前就过了。**
模板问要标准,却没问它们能否失败,这种情况会发生。三种形状反复出现:一个标准在 base commit 处已经为真,一个标准只能由另一张 ticket 的工作满足,以及一个标准重述了请求而不是从工件推导出来的。纵向切片预防其中大多数(一段切片交付原本不存在的行为,在 base commit 处按构造为红),但这个检查值得手工做。针对每条标准,命名一个能显示它为假的观察,并确认它在 implementer 起步的那个 commit 上确实失败。

**Tickets 已经发布了。我到底怎么跑它们?**
该技能停在工件上,而且没有自动分派模式。分派是手动的:看 board,数没有开放 blocker 的 ticket,开那么多 agent 会话。一个 ticket 一个干净上下文,中间清。注意 [implement](https://aihero.dev/skills-implement) 跑完时不会稳定地关闭或在 ticket 上打勾,无论在 GitHub 还是本地 markdown,所以 ticket 的状态由你来更新。

## It's working if

- 每个 ticket 对 "这事做完我能 demo 什么?" 都有一个答案,而这个答案是行为,不是一层。
- 列表以编号形式返回给你,每张带一行 "Blocked by",在发布之前。
- 顶部的 ticket 没有 blocker,可以立刻开始。
- ticket body 里没有文件路径或行号,除了 prototype 产出的片段。
- 每张 ticket 读起来都像一个全新会话可以在你不在场的情况下完成的事。
- Prefactoring(如果它找到了)在顺序的最前面,而非混在 feature ticket 里。

## Where it fits

`to-tickets` 是主构建链中的一步:

```txt
grill-with-docs → to-spec → to-tickets → implement → code-review
```

上游是 [to-spec](https://aihero.dev/skills-to-spec),它交给它一份已定的 spec 来切片;在两个之间保持一个不打断的上下文窗口。下游是 [implement](https://aihero.dev/skills-implement),它构建每个 ticket 一个干净会话,驱动 [tdd](https://aihero.dev/skills-tdd) 写测试,并以 [code-review](https://aihero.dev/skills-code-review) 收尾。当你不确定哪个技能或流程合适时,[ask-matt](https://aihero.dev/skills-ask-matt) 来路由。
