# 写文档页

`engineering/` 与 `productivity/` 下的每个 skill 都各有一份面向人的 **文档页**，位于 `docs/<bucket>/<skill-name>.md`。docs 目录结构与 `skills/` 下这两个桶目录保持镜像。它发布在 `https://aihero.dev/skills-<skill-name>`；URL 始终是 `skills-<skill-name>`，与所在桶无关，因此 docs 路径只用于仓库内部组织。文档页不是 skill 本身，也不是 `SKILL.md` 的副本。只有这两个桶是已推广的；其余（`misc/`、`in-progress/`、`deprecated/`）不发文档页。

这些 skill 大多是**用户调用**的：agent 永远不会替你触发它们，所以**你**就是那个必须记住它们存在以及何时取用的索引。这份记忆就是**认知负担**。文档页的工作就是缓解它：把一位读者放在一份 skill 周围，让他能在脑中把握它、知道何时取用、看清它在体系中的位置。文档页合在一起就是一份"分布式路由"；每一页就是一个节点。

每当一份已推广的 skill 被新增、重命名或行为变更时，就要动手：在 `docs/<bucket>/<新名>.md` 处创建或重新同步其文档页。改名也要把文件一起搬（`docs/<bucket>/<旧>.md` → `docs/<bucket>/<新>.md`），因为发布的 URL 跟着名字走；一份 skill 在 `engineering/` 与 `productivity/` 之间移动时，文档页也要搬到对应的文件夹。`misc/`、`in-progress/`、`deprecated/` 中的 skill 没有文档页，因为这些桶都没有被推广。一份 skill 从这些桶**移出**到 `engineering/` 或 `productivity/`，就新增一页；反向移动则丢掉一页。

由于这些页面发布在 `aihero.dev`，**每个链接都是绝对路径**：绝不能是仓库内相对路径。指向另一份 skill 的链接指向 `https://aihero.dev/skills-<name>`；指向仓库内的链接指向完整的 `https://github.com/mattpocock/skills/...` URL。在仓库里能用的相对链接，发布之后就废了。

没有 H1。发布页的标题取自 slug。

## 页面结构

按下面模板填空，并保持其顺序。**固定框架**（`## What it does`、`## When to reach for it`、`## Where it fits`）出现在每一页上。`## Prerequisites` 与自由形式的中段，只承载这一份 skill 真正需要的内容；其余删除。

四节构成一份值得读的页面：`What it does`、`When to reach for it`、`Common questions`、`It's working if`。前两节让读者定位，后两节是页面"不再复述 skill、开始回答读者自己处境"的地方。后两节各自有一条门槛，详见下面，但把哪条都没过的页面当作"未完成"，而不是"完成且精简"。

**页面不写任何安装命令。**ai-hero 页面模板会在正文上方自己渲染安装小部件（复制按钮、单 skill 命令、整套命令、更新一行）。如果页面也写一遍，等于让读者看同一段命令两次，而两份副本会漂移：每页手写的那一对早已落后于旁边的小部件。安装措辞是站点的属性，而非页面的属性。需要改，就改 ai-hero；权威措辞住在 [install-block.md](./install-block.md)。

<page-template>

## What it does

一两段平实语言的段落。以这份 skill 的一句话任务开篇，然后陈述**定义性约束**：让这份 skill 与"显然的默认"表现得不同的那一条事实（对 `to-spec` 而言：它不再访谈用户，而是把已经知道的内容合成出来）。写成一个普通的陈述句，绝不要写成"The defining constraint:"或"The key thing:"这种带标签的旁白；这种句式读起来像填充。这句话是页面里最有价值的一句；永远不要漏掉。

## When to reach for it

你如何、在何时伸手抓这份 skill，分两拍，并且两拍实际总是同时存在：

- **调用模式。**说明你是键入它，还是由 agent 触发。用户调用的 skill："你通过键入 `/<name>` 来调用，agent 不会自行抓取它。"模型调用的 skill："可以键入 `/<name>`，也可以在任务契合时由 agent 自动抓取。"
- **触发边界。**索引条目："reach for this when …"。当这份 skill 容易和兄弟 skill 混淆时，补上另一半："for <X> instead, use [<sibling>](https://aihero.dev/skills-<sibling>)。"

## Prerequisites

可选：仅在 skill 需要某些东西就位才能工作时使用；否则整节删除。覆盖：一段**它会写入的工作区**（像 `grill-with-docs` 这种有状态的 skill 会写 `CONTEXT.md` 与 ADR；`teach` 搭建整张目录，所以要说明它写什么、写到哪里）、**预先 setup**（`triage`/`to-spec`/`to-tickets` 需要 `setup-matt-pocock-skills` 已经配置好 issue tracker），或**仓库特有的工具**。一份无状态、哪儿都能跑的 skill 没有前置要求，于是删掉这一节。

## <自由形式中段>

一到三段短节，用这份 skill **自己的词汇**，让事情一下子说清楚。挑契合这份 skill 的小标题：它跑的循环、它产出的产物、它做的分叉、它消灭的那一条反模式。没有规定的小标题；skill 太异质，一套盖不住。

唯一不可让的：**把这份 skill 的引导词 / 定义性想法亮出来**（`tight` 反馈环、`deep module`、throwaway-code-answers-a-question、red-green）。它的回报是双倍的：读者既学到这份 skill **是什么**，也学到那个他们日后用来**伸手抓**它的词。

## Common questions

读者真正会问的、关于这份 skill 的问题，每个加粗，下方写答案。不要再用子标题。

观察到的真实问题永远胜过凭空捏造，所以在动笔之前先去找：

- **Wiki。**如果本机存在 `~/repos/matt/personal-wiki`，那就是最丰富的源头。它的 `wiki/audience/` 区域围绕"受众想要什么、讨论什么、**困惑什么**"组织：先读 `wiki/index.md` 找到页面索引，再读与这份 skill 相关的页面。每页都带 `sources:` 反向链接到原始的 X、Discord、GitHub 与邮件线索；wiki 是第二手资料，所以请引用提问者自己的原话，而非 wiki 的总结。该目录不存在时跳过这条。
- **本仓库的 issues。**`gh issue list --repo mattpocock/skills --search "<skill-name>" --state all`。同一问题被提两次，就是这份页面欠一份答案。
- **`CHANGELOG.md`。**任何被重命名、移动、或行为变更的事项，都会产生一份"它去哪了？"的问题，文档页必须回答。

猎寻落空时，本节也可以承载一条读者显然会问的问题，但**数量要对得起证据**。被充分讨论的 skill 配得上六条；冷门的配上一两条，甚至一条都不要。把"问题少的 skill"硬撑到和"问题多的 skill"一样长，正是这一节灌满"没人会问的问题"的来由；凭空捏造的问题什么都教不会读者。

按被提及的频率排序，越尖锐的越靠前；并在属实的地方说出不那么体面的话：一场长得过分的 grilling 会话通常意味着范围太大；被要求自己写 skill 的模型，往往写出啰嗦的东西。没什么值得回答的东西时，连小标题一起省掉。

## It's working if

几条 bullet 列出读者在 skill 干好活时能看到的迹象。每条的门槛是：读者不必打开 `SKILL.md` 就能自己核对 —— 要么来自自己工作中的信号，要么来自眼前的 trace。"文档越改越好反而越短"通过；"library 那段与 `template.sh` 字节一致"是给 skill 内部做的合规检查，只是披着这一节的名字。迹象清晰可辨时保留这一节；迹象仍然模糊时就省掉。

## Where it fits

永远存在。一两句话把这份 skill 放进体系里：

- **角色。**直接点名：一条 **链节**（`grill-with-docs → to-spec → to-tickets → implement → code-review`）、**一次性 setup**（`setup-matt-pocock-skills`）、**周期性维护**（`improve-codebase-architecture`，"每隔几天"），或**随时伸手可抓的独立 skill**（`diagnosing-bugs`、`prototype`、`handoff`）。独立 skill 的地图一句话诚实交代就够了，远胜于干脆省略这一节。
- **邻居。**一两份真正要紧的兄弟 skill，每个带一个 because 从句，并用绝对链接。
- **地图。**指向 [ask-matt](https://aihero.dev/skills-ask-matt)，那是覆盖整套 skill 的路由，让这份页面始终是一个节点而不是再去重画整张图。

</page-template>

## 约定

- 解释**为什么**，而不是过程。页面是给一份 skill 定位和安放的；它从不复述 `SKILL.md` 步骤或模板 dump：选工具的人不需要 runbook。
- **绝不点名作者。**页面是技术文档，不是"谁说了什么"的记录。"Matt says"、"Matt's own answer"、"his position is"、某条引用回复：全部去掉。问题猎寻中得到的发现值得保留；其归属不必保留。把实质作为关于 skill 的普通陈述（"the fix is a direct instruction: …"、"the split comes down to session count"），把框架丢掉。读者在决定是否使用工具；观点的份量无论署不署名都一样，而署了名的那条会随着立场变化而过期。引用**用户**仍然 OK："one user reported …" 是关于 skill 在野外的证据，保持匿名即可。
- 使用 skill 的**引导词**（_seam_、_deep module_、_tracer bullet_），让页面与 skill 讲同一种语言。
- **凡 [AI Coding Dictionary](https://www.aihero.dev/ai-coding-dictionary) 已收的术语，就用它的写法，并在本页首次出现时加上链接。** Dictionary 是 AI 编码的内部词汇表：_context window_、_subagent_、_harness_、_primary source_、_agent mode_。优先用它的词，而不是自己造同义词。每个术语的首次出现链接到 `https://www.aihero.dev/ai-coding-dictionary/<slug>`（slug 是术语的小写、非字母数字替换为连字符：_context window_ → `context-window`），之后再出现一律不加链接。仅当该词承载的是 dictionary 的含义时再链接：领域 *model*、背景 *context* 或鉴权 *token*，是另一回事，只是恰巧撞名。从不在标题、代码 span、已有链接内加链，也从不为"本仓库中一份 skill 的名字"加链（那是 skill，不是概念）。完整术语列表：本机如存在 `~/repos/ai/ai-coding-dictionary/dictionary/`（每条术语一个文件，文件名即术语本身），读它即可；否则读 [mattpocock/dictionary-of-ai-coding](https://github.com/mattpocock/dictionary-of-ai-coding)，它始终是权威来源。
- **分支用表格或列表，绝不用段落。**当页面要呈现一种选择（skill 可能产出的两种产物、触发它的四种情形、边界处的五种选项），读者是在扫描、去匹配自己处境的那一行。段落会逼他通读一遍才能确定。一张简短的 markdown 表格（左列条件、右列怎么做）或一个 bullet 列表，能让他一眼就拿到。无论这条分支出现在哪 —— 最常见是 `## When to reach for it` 与中段 —— 都遵循这一条。
- 页面本身保持低负荷。它是一份**关于**低认知负担 skills 的文档；装饰（多余的小标题、重复的链接）正是它要反对的东西。

## 完成的标志

- 页面存在于 `docs/<bucket>/<name>.md`，且改名或桶迁移之后没有遗留的过期页。
- 页面不带源链接，也不写自己的安装命令。
- `## What it does` 把"定义性约束"写成平实散文，而非带标签的旁白。
- 页面不点名作者，也不引用作者：每条主张自圆其说。
- `## When to reach for it` 写明调用模式与触发边界。
- `## Where it fits` 点名角色，并链接到 `ask-matt`。
- 前置条件（工作区、预先 setup、工具）有就写，没有就把整节省掉。
- 中段把引导词亮出来。
- 用到的每个 AI Coding Dictionary 术语都按字典的写法拼写，且仅在首次出现时链接到字典条目。
- 每处多路分支都写成表格或列表，而不是逼读者通读的段落。
- 已经跑过一次真实问题猎寻（wiki、issues、changelog），`## Common questions` 的体量对齐它找到的，而不是为了迁就一份"问题更多"的 skill 而硬撑。
- `## It's working if` 的每条 bullet 都不必打开 `SKILL.md` 就能核对。
- 各节按模板顺序出现。
- 每条链接都是绝对路径，且都能解析。
