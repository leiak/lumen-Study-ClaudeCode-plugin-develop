# mattpocock-skills

## 1.2.3

### Patch Changes

- [#779](https://github.com/mattpocock/skills/pull/779) [`efce423`](https://github.com/mattpocock/skills/commit/efce423018fc6468a3239621f1c1bcaacc723801) 致谢 [@mattpocock](https://github.com/mattpocock)! - 让 `diagnosing-bugs` 对密钥进行脱敏。

  - 在 `SKILL.md` 中新增 **Redact（脱敏）** 一节。该 skill 会让 agent 展示命令、输出和捕获到的产物；该节把"脱敏"作为每条命令的第一步 —— 写成 `<REDACTED>`，围绕环境变量构建循环以使凭据始终留在环境中，并在捕获的产物中只引用"承载信号"的行。
  - 第 1 阶段的完成准则原本是"粘贴调用及其输出"。现在它说的是"以脱敏后的形式展示"，并且第 1 阶段会向用户索取一份**脱敏后的**捕获产物。
  - 在 `scripts/hitl-loop.template.sh` 中注明 `capture` 会把它的值打印回终端，因此它会在"登录"仍是一项 `step` 的同时承担观察记录的工作。

- [#781](https://github.com/mattpocock/skills/pull/781) [`14bfbbd`](https://github.com/mattpocock/skills/commit/14bfbbd8654a8d2910299e1a004c19c1979687d8) 致谢 [@mattpocock](https://github.com/mattpocock)! - 从 `code-review`、`codebase-design` 与 `improve-codebase-architecture` 的子 agent 调度指令中去掉了 Claude Code 的工具名与 agent 类型名，使该步骤也能在 Codex 与其他 harness 上跟随。

- [#783](https://github.com/mattpocock/skills/pull/783) [`c0fd1e9`](https://github.com/mattpocock/skills/commit/c0fd1e973e040347d424e09934099f1bd6c2dee0) 致谢 [@mattpocock](https://github.com/mattpocock)! - wizard：去掉时间估算。模板丢弃了 `TOTAL_MINUTES` 与"剩余时间"显示，`stage` 只接受名字，进度按 stage 计数。

## 1.2.2

### Patch Changes

- [#766](https://github.com/mattpocock/skills/pull/766) [`4aaccb5`](https://github.com/mattpocock/skills/commit/4aaccb58d40559d7e3c59a029b2290ae5ba538de) 致谢 [@mattpocock](https://github.com/mattpocock)! - 让 `writing-for-agents` 重新可在 Codex 中被模型调用。

  - 从 `agents/openai.yaml` 中移除 `policy.allow_implicit_invocation: false`。Codex 会把该 skill 从"模型可见的 skills 列表"里过滤掉，因此它的描述无法触发它 —— 只能靠显式的 `$writing-for-agents` 提及才行。
  - 更新了过时的 `interface.display_name` 和 `interface.short_description`，它们当时还叫旧的 `writing-great-skills`。
  - 在 `README.md` 与 `skills/productivity/README.md` 中将该 skill 从 **用户调用** 列表移到 **模型调用** 列表。

## 1.2.0

### Minor Changes

- [#551](https://github.com/mattpocock/skills/pull/551) [`697d4ce`](https://github.com/mattpocock/skills/commit/697d4ce9742da558fd1ba6697c8e9775e2e302dd) 致谢 [@mattpocock](https://github.com/mattpocock)! - 在每个 skill 的 Claude Code frontmatter 旁边新增 Codex 元数据，使整套 skill 在两个 harness 都能工作，而无需生成副本。

  - 在每个 `SKILL.md` 旁边添加一份 `agents/openai.yaml`，承载 Codex UI 元数据（`interface.display_name`、`interface.short_description`）。
  - 把每个用户调用的 skill 都标记为 `policy.allow_implicit_invocation: false`（即 Codex 中 `disable-model-invocation: true` 的对应物），使 Codex 在隐式调用时排除它，但显式的 `$skill` 调用仍然有效。
  - 在 `.agents/invocation.md`、`CLAUDE.md` 与已推广 bucket 的 README 中记录双 harness 调用模型。
  - 添加 `AGENTS.md` 作为 `CLAUDE.md` 的符号链接，以便 Codex 读取同一份仓库指令。

- [#593](https://github.com/mattpocock/skills/pull/593) [`0f2bdbd`](https://github.com/mattpocock/skills/commit/0f2bdbdb06220d2df3718b8f0483157c6c8a8600) 致谢 [@mattpocock](https://github.com/mattpocock)! - 把 **`to-questionnaire`** 从 `in-progress/` 升级到 **Productivity** bucket，于是它随插件一起发布。它把"你独自无法回答"的决策，转化为一份 Markdown 问卷交给那个能回答的人 —— 可异步填写，也可以在会议上一同完成。

  它的标志性动作是：它访谈的对象是**发送动作**，而非主题本身：普通的访谈会话会拷问主题，而那正是这里你回答不了的东西；所以这次访谈只问这份问卷要寄给谁、需要拿回什么，然后让每一个问题都对准"这两者之间的缺口"。

  现在它以已推广 skill 的身份接入 —— 插件条目、顶层 + Productivity README 中的 **User-invoked** 列表、`docs/productivity/to-questionnaire.md` 文档页，以及 `ask-matt` 中作为"独立（Standalone）"路由的一节，将其框定为 `/grill-me` 的反向（拷问别人，而非自己）。

- [#680](https://github.com/mattpocock/skills/pull/680) [`b3376f8`](https://github.com/mattpocock/skills/commit/b3376f8d39848dd08572ec2667da4739a67c8c04) 致谢 [@mattpocock](https://github.com/mattpocock)! - 把 **`wizard`** 从 `in-progress/` 升级到 **Engineering** bucket，于是它随插件一起发布 —— 并改为模型调用。它会生成一份交互式 bash 脚本，带人走完一个手工流程 —— 第三方设置、一次性迁移、A→B 状态切换 —— 打开每个 URL，告诉你该点什么，抓取值，然后写入 `.env` 文件与 GitHub Actions secrets。

  让人愉悦的 UX 由捆绑的 `template.sh`（带剩余时间的进度、确认门、跨平台打开 URL（含 WSL）、隐藏 secret 输入、幂等的 `.env` upsert、带优雅降级的 `gh secret`/`gh variable` 写入、收尾时的 skip 汇总）预先解决。`STAGES` 标记之上的全部是固定库、从不手工编辑 —— 这个 skill 的工作只是界定流程的边界并撰写它的 **stages**。

  归在 Engineering 而非 Productivity 的原因：它会读取 `.env*`、`docker-compose*`、框架配置，以及 `.github/workflows/` 中每一个 `secrets.*`/`vars.*` 引用来自我界定范围，会写入 CI secrets，并用 `bash -n` 与 `shellcheck` 校验自己的输出。

  因为它是模型调用，agent 可以在撞上"只有人能完成的步骤"的瞬间立刻抓取它，而不再是把编号说明一股脑倒进聊天框指望你照着做。手动键入 `/wizard` 行为与之前完全一致 —— 模型调用只是**额外**给了 agent 一条抓取路径。描述被写成"决定何时触发它"的指针：它会产出什么、四个触发分支（provisioning 基础设施、设置凭据或 CI secret、走过陌生的第三方 dashboard、一次性迁移或切换），以及一条显式的"不触发" —— 不要为 agent 自己能完成的步骤去调用它。Agent 能做的事，agent 就该做；wizard 只留给那些你不会丢给 agent 的"点击、审批、跑 dashboard"动作。在写入一行之前的"stage 列表"确认，也顺带成为 agent 在构建过程中触发它时的"提案"。

  现在以已推广 skill 接入 —— 插件条目、顶层 + Engineering README 中的 **Model-invoked** 列表、`docs/engineering/wizard.md` 文档页，以及 `ask-matt` 中作为"独立"路由的一节，描述"只有人能完成的步骤"。模型调用也使它避开了 [#693](https://github.com/mattpocock/skills/issues/693) 的影响 —— 该 issue 在 Claude 桌面端和 Web 端把用户调用的 skill 从清单中移除。

- [#763](https://github.com/mattpocock/skills/pull/763) [`77d207e`](https://github.com/mattpocock/skills/commit/77d207ef03219cc603e2832e1159cbdd1c91818e) 致谢 [@mattpocock](https://github.com/mattpocock)! - 围绕两个想法重塑 **`prototype`** skill：演示稿是一份**单一可共享的 HTML 文件**，原型是一份**主源（primary source）**。

  逻辑分支现在产出一份自包含的文件（纯 HTML/CSS/JS，无构建、无服务器），只需双击即可打开、由非开发者用他们自己的领域语言驱动：一个带标签的状态面板、随时可用的自由操作按钮，以及一组**分页式引导演练** —— 每个场景下都附带一组按顺序排列的按钮。可移植的纯逻辑模块仍会提升到真实代码中；HTML 外壳才是"一次性"的。

  "一次性"不再等于"删除"。原型在回答完它的问题之后不再被丢弃，而是作为可运行的证据，被捕获到一个从 main 分出的临时分支（`prototype/<name>`）上，并在实现 issue 中留下一个上下文指针 —— 这样 main 分支只保留经过验证的决策，而探索过程仍然可以查到。答案（结论 + 问题）依然会通过 issue/ADR/commit 持久捕获。

- [#536](https://github.com/mattpocock/skills/pull/536) [`42a5b70`](https://github.com/mattpocock/skills/commit/42a5b70fcacc7baff1977b13f3919fb2f63af14e) 致谢 [@mattpocock](https://github.com/mattpocock)! - 将这套 skill 以原生 **Claude Code 插件** 形式发布，列入 Claude Code 官方市场。你现在可以"订阅"已推广的 skill，作为一个托管的、只读的 bundle，而无需复制可编辑文件：

  ```bash
  claude plugins install mattpocock-skills
  ```

  或者，在一个会话内：

  ```
  /plugin install mattpocock-skills
  ```

  无需先添加任何市场 —— 官方市场是默认配置的。

  `.claude-plugin/plugin.json` 承载完整的插件元数据（版本、描述、作者、license、关键词）以及已推广 skill 的显式清单。`skills.sh` 仍然是通用安装器（也是当下 Codex 与其他 harness 的路径）；原生 Codex 插件被推迟 —— 见 `.agents/adr/0002-ship-as-a-claude-code-plugin.md` 了解原因。

- [#751](https://github.com/mattpocock/skills/pull/751) [`355fa74`](https://github.com/mattpocock/skills/commit/355fa7420b418af838998f7ec4365ceda1c8dfcc) 致谢 [@mattpocock](https://github.com/mattpocock)! - 新增 **`wait-what`** —— 一个用于纠正模型啰嗦的单词级指令。在对方一句话没接住的瞬间键入它，agent 会重新讲一遍：一点点上下文、ASD-STE100 简化技术英语，以及你 `CONTEXT.md` 中的通用语言。用户调用，三行长度。

  机制就藏在名字里。简洁类 skill 失败的常见方式是"越长越大"——一份 400 行的 skill 仍然会让模型啰嗦 —— 所以这一份只是一个精准的引导词、除此之外别无他物。描述**输出**的命名（`/tldr`、`/no-fluff`）会让模型去"删词"，反而让你更听不懂；以**听者的状态**来命名则同时要求两半 —— 更少的词**加上**你缺失的上下文。它还复用了你已经写在全局 `CLAUDE.md` 里的引导词，因此这份 skill、`CLAUDE.md` 与每份 `CONTEXT.md` 抓取的都是同一批 token。

  它只修补这一条消息；它防止不了下一条。Jargon 的真正解法是事前通过 `/grill-with-docs` 建立起共同语言；这是你在还没共同语言时伸手抓的。

- [#763](https://github.com/mattpocock/skills/pull/763) [`77d207e`](https://github.com/mattpocock/skills/commit/77d207ef03219cc603e2832e1159cbdd1c91818e) 致谢 [@mattpocock](https://github.com/mattpocock)! - 把 `/wayfinder` 的基本单位命名为 **decision ticket（决策工单）**，并用子 agent 把 research 工单逐个烧掉。

  大家一直把 wayfinder 的工单当成普通的**实现**工单 —— 一片待执行的构建 —— 而 wayfinder 把它们用作**决策工单**：答案本身就是一个决策的问题。Skill 描述和它的开篇现在引入这个术语（并说明是什么让它成为决策工单），`ask-matt`/engineering README 的简介和文档页也跟着调整 —— "ticket"则保留为术语确立后的日常用词。`CONTEXT.md` 把 **Decision ticket** 记录为一个领域术语，于是"avoid: ticket"这条指引就不再与 wayfinder 有意为之的"工单"用法相矛盾。

  Research 工单不再为单独启动的会话而搁置。Research 仍然是一种真实的工单类型 —— 它是一个真正会被下游决策共享阻塞的东西，而这种依赖正是 frontier 的 blocking edges 存在的原因。变化的是它被解决的方式：因为 research 是 AFK 的，绘图工作不会停下来等它读。在创建工单之后，绘图会话会为每张 research 工单各自启动一个 `/research` 子 agent 并行烧掉它，把结论捕获到一个临时的 `research/<name>` 分支上，并留下一个上下文指针。Research 工单是"一张工单一个会话"原则的唯一例外。

- [#763](https://github.com/mattpocock/skills/pull/763) [`77d207e`](https://github.com/mattpocock/skills/commit/77d207ef03219cc603e2832e1159cbdd1c91818e) 致谢 [@mattpocock](https://github.com/mattpocock)! - **破坏性变更：** 把 **`writing-great-skills`** 重命名为 **`writing-for-agents`**，重构它，并新增一个引导词。

  参考文档现在覆盖任何 agent 会消费的文档 —— skills、`AGENTS.md` / `CLAUDE.md`、通过指针取到的 docs —— 而不仅仅是 skills。`GLOSSARY.md` 合并进了 `SKILL.md`（每个术语只在一处权威陈述；`_Avoid_` 同义词列表以及独立的 Predictability 定义被去掉）；skill 专属的机制（frontmatter、模型调用 vs 用户调用、路由 skill、调用的"拆分"切法）披露到一个新的 `SKILL-MECHANICS.md`。该 skill 现在是**模型调用**：它在创建或编辑 skill、修改 `AGENTS.md`/`CLAUDE.md` 时触发。`ask-matt` 的指针已更新。请在新名字下重新安装；旧名字已消失（无别名）。

  "剪枝"一节增加了 **cache（缓存）**。单一真相之源现在延展到文档之外，进入到环境中 —— `package.json` 脚本、配置文件、目录布局、`--help` 输出本身就是权威，因此一份"复述它们"的文档就是一次查找的缓存，只有在查找代价高昂时才值得加载。积极的方针是：缓存那些 agent 靠"看"找不到的东西（未成文的约定、选择背后的理由、没有任何配置会承认的坑），并把"一个文件、一条命令就能查到"的内容留给环境，因为环境不会过时。

- [#533](https://github.com/mattpocock/skills/pull/533) [`45afd80`](https://github.com/mattpocock/skills/commit/45afd8074a8b7de5fe073845d080fa9dd6c429fa) 致谢 [@mattpocock](https://github.com/mattpocock)! - 给 **`improve-codebase-architecture`** skill 的 Explore 步骤加上一道 YAGNI 范围过滤器。它不再均匀扫描整个仓库，而是把范围收窄到"改动实际落点"的地方：如果你指一个方向它就照办；否则它会读取最近大约 20 条 commit message，把探索偏向"正在活跃开发的路径"。在没人碰的代码里挖出来的"深化"机会，是一笔永远兑现不了的 refactor —— 这笔杠杆只有在你持续编辑的地方才能兑现 —— 因此报告不再去收拾仓库里沉睡的死角。

### Patch Changes

- [#763](https://github.com/mattpocock/skills/pull/763) [`77d207e`](https://github.com/mattpocock/skills/commit/77d207ef03219cc603e2832e1159cbdd1c91818e) 致谢 [@mattpocock](https://github.com/mattpocock)! - 锐化 `/ask-matt` —— 路由现在覆盖阶段边界、wayfinder 两种易错情形、以及它原本未提及的两个 skill。

  **阶段边界**。**阶段（phase）** 是会话内的一块工作 —— 访谈、实现、QA —— 在两块阶段的边界处，你需要决定"已经构建起来的上下文"要怎么办。原本两行的 `Crossing sessions` 小节被替换为一张决策树，按顺序列出全部五种选项（**continue**、`/clear`、`/handoff`、**subagent**、`/compact`），其推理过程披露在新文件 `PHASE-BOUNDARIES.md` 里。随之带来三处修正：

  - **`/handoff` 被过度推崇了。** 它原本读起来像"上下文窗口之间的一般桥梁"。它其实很窄：你只在某些东西必须**迁移**时才需要它 —— 新的 harness、新的目录、一位同事、或在阶段中途分叉出来的旁支任务。它换来的是"可移植性"。
  - **`/compact` 是默认值，不是第一反应。** 它在决策树的最底部，在它上方还有四种更便宜或更精确的选项。一上来就用它会得到一个"对自己压平的总结充满自信"的会话。
  - **两个分支原本完全缺失。** **Continue** 是第一个应当排除的选项 —— 它是唯一一个把对话本身作为主源（而非其摘要）保留下来的动作；**子 agent** 则用于任何范围紧凑到可以 AFK 跑完的任务。

  "上下文卫生"的逃生口现在写的是 `/compact` 而不是 `/handoff`（同一个 harness、同一个目录、处于阶段边界 —— handoff 的条款不适用），smart zone 的数值也从约 120k token 更新到约 150k token。

  **Wayfinder 路由**。在使用这条最重、认知负荷最高的流程时，最常犯的两个错误：

  - **伸手够得过高。** 它比一次访谈更慢、更密，因此被标记为"最重的流程"，仅留给那种一次会话装不下的想法 —— 范围明确的小功能属于 `/grill-with-docs`，不属于这里。
  - **在交接时丢了路。** 当地图变清晰时，wayfinder 只交接、不构建：在 `/to-spec` 处汇入主流程（它会把地图上相互关联的决策压缩成可构建的计划），而不是把地图直接回环进 `/implement`。只有当工程最终确实很小时才直接走 `/implement`。

  **缺失的路由**。`/grilling` 与 `/resolving-merge-conflicts` 原本在路由里完全缺席，现在都已纳入；`grill-me` 与 `grill-with-docs` 的拆分依据是"你是否处于工作目录里"。

- [#502](https://github.com/mattpocock/skills/pull/502) [`44eed54`](https://github.com/mattpocock/skills/commit/44eed545186ffd0263e8004867750b80cfddd215) 致谢 [@mattpocock](https://github.com/mattpocock)! - 让 `/setup-matt-pocock-skills` 更友好，并把本地 markdown tracker 与最新规范对齐。

  - **Triage 标签**现在只在 `triage` skill 已安装时才询问，并作为一个单一的"推荐是"问题（"保留默认的 triage 标签？"），而不是让你去覆盖的盘问。当 `triage` 未安装时，连同 `docs/agents/triage-labels.md` 一起跳过这一节。
  - **外部 PR 作为请求面** 不再是 setup 时的问题。GitHub/GitLab 模板仍然保留这个开关，默认关闭；用户可以稍后在 `docs/agents/issue-tracker.md` 中翻转它。
  - **Domain docs** 默认单 context、不再询问；只有当仓库出现 monorepo 信号时才提供 multi-context 选项。
  - **Local-markdown tickets** 现在是每张工单一个文件，位于 `.scratch/<feature>/issues/<NN>-<slug>.md` —— 永远不会是一份合并的 `tickets.md`。`/to-tickets` 与本地 issue-tracker 模板现在达成一致，规格文件也改为 `spec.md`（不再是 `PRD.md`），与 `/to-spec` 保持一致。

  `setup-matt-pocock-skills` 与 `to-tickets` 的文档页已重新同步。

- [#532](https://github.com/mattpocock/skills/pull/532) [`170ad48`](https://github.com/mattpocock/skills/commit/170ad48655825783d0193e850e31a9aac957bb95) 致谢 [@mattpocock](https://github.com/mattpocock)! - 把 **`grilling`** 的措辞改为通用。它的描述和正文不再把访谈范围限定在"软件计划"："this plan" → "this"、"enact the plan" → "act on it"、"exploring the codebase" → "exploring the environment"。技术本身不变；现在它读起来是对任何计划、决策或想法的压力测试。

- [#593](https://github.com/mattpocock/skills/pull/593) [`a4b2009`](https://github.com/mattpocock/skills/commit/a4b2009a1a3ac9575506c10b4c84f08f9bba7a38) 致谢 [@mattpocock](https://github.com/mattpocock)! - 把 **`grilling`** 从"一次一题"重做为"一轮一轮"。它现在先把决策树画出来，然后在**单一一轮**里问出整个 **frontier** —— 所有前置条件已落定的问题 —— 然后根据用户的回答重算 frontier、再问下一轮。同一组 13 道题大约 3 轮就问完，而不是 13 轮。环境能够回答的事实被分派到后台子 agent，使 research 永远不阻塞这一轮：只有"在某次探索之后才能成立"的问题才会等探索。当 frontier 空掉时，会话结束。

  一轮里的每个问题都以同一固定形式输出 —— `❓ **Q1** - **<title>**`，然后是正文（散文或多项选择），再以一条独立的 `➡️` 行给出推荐。一轮读起来像一份可扫读的编号列表，每条推荐在视觉上与问题分开，因此你可以用编号回答，而不必把问题原文引用回来。

  `grill-me`、`grill-with-docs` 与 `triage` 也以"一轮一轮 frontier"的方式运行 —— `triage` 的 grill 步骤与 `grilling` 的 Codex `short_description` 现在都这么说，而不是描述旧的节奏。一题一题的"反悔口"（在你的全局 `CLAUDE.md` 里写一行）保持不变。

- [#752](https://github.com/mattpocock/skills/pull/752) [`c66bdee`](https://github.com/mattpocock/skills/commit/c66bdeeee002d81e3f8b21403c07f9a0d7bea6da) 致谢 [@mattpocock](https://github.com/mattpocock)! - 从仓库中移除六个 skill。它们都不在 Claude Code 插件里，但都曾通过 [skills.sh](https://skills.sh/mattpocock/skills) 可安装 —— skills.sh 服务的是仓库中的每一个 skill —— 所以这就是这份清单上少掉的东西、以及它们各自去了哪里。

  四个被淘汰的 skill，每一个都已被一个更胜任的 skill 吸收：

  - **`ubiquitous-language`** → **`/domain-modeling`**，后者构建并维护整个领域模型，而不是从一次对话里倒一份词表出来。
  - **`design-an-interface`** → **`/codebase-design`**。没有损失："design it twice"技巧 —— 由并行子 agent 生成截然不同的设计，源自 Ousterhout —— 以 `DESIGN-IT-TWICE.md` 的形式被装进了那个 skill。
  - **`qa`** → **`/triage`** 与 **`/to-tickets`**。
  - **`request-refactor-plan`** → **`/to-spec`** 与 **`/improve-codebase-architecture`**。

  还有两个从来只属于我自己 —— 绑死在我自己的机器上、从未打算给任何人用。`personal/` 桶也跟着它们一起没了：

  - **`edit-article`**
  - **`obsidian-vault`**，里面硬编码了我自己 Obsidian vault 的路径。

  `skills/deprecated/` 仍然作为桶保留，但现在它是空的。`skills/in-progress/` 未变，现在按它实际的样子来描述：一个 beta 频道，故意公开，可以通过 skills.sh 一条一条地安装。

- [#734](https://github.com/mattpocock/skills/pull/734) [`a2f9333`](https://github.com/mattpocock/skills/commit/a2f9333669ff53db762c87ecda5a15442060a3be) 致谢 [@mattpocock](https://github.com/mattpocock)! - 完成 `to-prd` → `to-spec` 的改名收尾："spec" 现在是已发布文本中唯一的术语。

  - **`to-spec`** 不再以"you may know this document as a PRD"开场 —— 这个括号说明从 skill 与它的文档页里被去掉了。本地 markdown tracker 模板也去掉了同样的兜底。
  - **`code-review`** 在 frontmatter 描述、两轴总结、以及 spec 源搜索顺序里都说的是"来源 issue/spec"而不是"issue/PRD"。两份 README 重新同步。
  - **GitHub 与 GitLab tracker 模板** 现在写的是"本仓库的 Issues 和 specs 以 GitHub/GitLab issues 形式存在" —— 它们之前一直被留在 "PRDs" 上、在本地模板更新之后，所以这个过时的术语被带入每一个被它们写出的仓库。
  - **`docs/engineering/research.md`** 之前指向 `https://aihero.dev/skills-to-prd`，那个 slug 已经因为改名而死链；现在它和另外十九份文档页一样，链接到 `to-spec`。

  CHANGELOG 与既有 changesets 在描述改名这件事本身时仍然写着 PRDs，这是对的。

## 1.1.0

### Minor Changes

- [#406](https://github.com/mattpocock/skills/pull/406) [`930a450`](https://github.com/mattpocock/skills/commit/930a450089f77a49af09001d955db8452a4b867d) 致谢 [@mattpocock](https://github.com/mattpocock)! - 让 **`ask-matt`** 路由与整套 skill 同步。它现在映射了它原本缺失的五个 skill：**`tdd`**（作为 `implement` 驱动的红绿引擎织入主流程）、**`diagnosing-bugs`**（一条新的 "Something's broken" 接入支线 —— 之前根本没有 bug 的路由）、**`domain-modeling`** 与 **`codebase-design`**（新增的"底层词汇层"一节），以及 **`grilling`**（共享的访谈原语）。`prototype` 被扩充为一个独立 skill，描述也从"user-invoked skills"放宽到"the skills"。`CLAUDE.md` 中新增一条维护规则：今后任何 skill 的新增/重命名/移除或流程变更，都会触发对 `ask-matt` 的复核，与既有的"文档页重新同步"规则并列。

- [#464](https://github.com/mattpocock/skills/pull/464) [`639df6e`](https://github.com/mattpocock/skills/commit/639df6e7386dfddc739b2aecdeff37a876f2483b) 致谢 [@mattpocock](https://github.com/mattpocock)! - 推广并加固 **`code-review`**。`in-progress` 中的 **`review`** skill 被重命名为 **`code-review`** 并从 `in-progress/` 搬进 `engineering/`：现在它随插件一起发布，被列入顶层 README 与 Engineering README（Model-invoked），并有一份 `docs/engineering/code-review.md` 文档页。`/implement` skill 与文档指向 `/code-review`。

  它还在 Standards 轴上获得了一道常开的 **Fowler 坏味基线** —— 一份精选的大约 12 个高信号"代码坏味"（神秘命名、重复代码、依恋情结、数据泥团、基本类型偏执、反复 switch、霰弹式手术、发散式变化、投机性一般性、消息链、中间人、拒绝遗赠）被内联进 `SKILL.md`，作为与仓库自身文档并列的固定基线，而不是新的第三轴。两条硬性规则使其保持安全：仓库明文规范高于基线，并且每个坏味都以"判断题"而非"硬性违规"的方式上报。

- [#464](https://github.com/mattpocock/skills/pull/464) [`639df6e`](https://github.com/mattpocock/skills/commit/639df6e7386dfddc739b2aecdeff37a876f2483b) 致谢 [@mattpocock](https://github.com/mattpocock)! - 从两方面锐化 **`grilling`**。

  **确认门**。在用户确认"共同理解已经达成"之前，agent 不会执行计划 —— 把 skill 中已有的"共同理解"完成准则显式化为一个 stop-gate。`description` 同样引入了预训练过的 **`grill`** 引导词（"Grill the user relentlessly"），以锐化触发，文档页也重新同步。

  **事实 vs. 决策**。Grilling 现在把**事实**（去查 —— 探索代码库）与**决策**（把每一个摆在人面前、等他回答）拆开。原本那条一刀切的"如果一个问题能通过探索代码库来回答，就去探索代码库" —— 是按真人会话场景写的；一旦另一个 skill 在"解决工单"框架里运行 grilling，它就会被读成"自己拿主意回答决策问题"的许可证。把两者分开，能防止一个 grilling agent 自己抢答、往前跑。

- [#463](https://github.com/mattpocock/skills/pull/463) [`af6d692`](https://github.com/mattpocock/skills/commit/af6d6922c3e2b5288eef155346cbe319e4ed3bd0) 致谢 [@mattpocock](https://github.com/mattpocock)! - 给 **`writing-great-skills`** 新增两条相邻的"Steering 失败模式"，都关于你以为"关掉了"的语言，仍然在暗中引导 agent。**Negation（否定）—— 大象** —— 是通过禁令来引导：命名"不该做的事"会把禁忌行为拽进上下文，让它变得**更**可用而非更不可用（_别想那只大象_），因此解法是**正向**地提示。**Negative Space（负空间）—— 虚空** —— 是一种"看不见自己留下的东西也在引导"的盲目：每一条 skill 拒绝做的决策，都会被悄悄推给 agent 的先验，而不是被设为"中性"，因此解法是读草稿的沉默、刻意地决定每一处省略（要么填上，要么作为一条真正的**分支**保留为开放）。两条作为独立条目 —— 它们有不同的诊断与不同的解法 —— 各占一份完整的 `GLOSSARY.md` 条目加一条 `SKILL.md` 失败模式 bullet，与其他失败模式的承载方式保持一致。

- [`850873c`](https://github.com/mattpocock/skills/commit/850873cd73d5f81826ebf512ad35d2b1e113001f) 致谢 [@mattpocock](https://github.com/mattpocock)! - 把 **`prototype`** skill 改为模型调用，这样 agent 可以自主抓取它（其他 skill 也可以）。它的描述被围绕引导词 _prototype_ 重写 —— 用一次性代码回答一个设计问题 —— 每条分支对应一个触发条件（state/logic sanity-check，或 UI exploration）。

- [#409](https://github.com/mattpocock/skills/pull/409) [`0d74d01`](https://github.com/mattpocock/skills/commit/0d74d01cbc64ca27778a49b38599f70c534e76a0) 致谢 [@mattpocock](https://github.com/mattpocock)! - 新增 **`research`** skill —— 一份小巧的、模型调用的 skill，它启动一个**后台 agent**，针对**主源**（官方文档、源码、规范、第一方 API）调查一个问题，然后把一份带引用的 Markdown 文件留在仓库约定存放这类笔记的地方。它就是把阅读杂活委托出去：你继续工作，它替你读，回来时给你一份文档，供你去做访谈、规划或设计。已列入顶层与 Engineering README（Model-invoked），已加入 `.claude-plugin/plugin.json`，已生成 `docs/engineering/research.md` 文档页，并在 `ask-matt` 中作为"独立（Standalone）"路由。

- [#469](https://github.com/mattpocock/skills/pull/469) [`a0329ba`](https://github.com/mattpocock/skills/commit/a0329ba95751f58566ed7ab484475917a68f1629) 致谢 [@mattpocock](https://github.com/mattpocock)! - 把 **`to-issues`** skill 拆成精简的 **Process** 与一份 **Reference** 节，并教它处理一种**大型重构** —— 一种纯机械的改动（例如重命名一列），它的**爆炸半径**横跨整个代码库，瞬间炸出上千个调用点，于是没有哪一片垂直切片能独立落地变绿。起草步骤现在指向两份同地协作的参考块：**Vertical slice rules** 用于普通的示踪弹，以及 **Wide refactors** —— 它按 **expand–contract（扩展-收缩）** 切分变更（在新形态旁边扩展旧形态、按爆炸半径分批迁移调用点，然后再收缩掉旧形态），使 CI 一批一批保持绿 —— 或者当它做不到时，只在最后一张"整合并验证"工单里让它短暂变绿。Issue 正文模板也一并移入 Reference。

- [#464](https://github.com/mattpocock/skills/pull/464) [`386d4ff`](https://github.com/mattpocock/skills/commit/386d4ff719a7c420ad1454232d0436b01f1b8c17) 致谢 [@mattpocock](https://github.com/mattpocock)! - 统一规划类 skill。**`to-prd` 被重命名为 `to-spec`** —— "spec" 现在是贯穿全线的唯一术语（为可发现性，开头仍保留 "you may know this document as a PRD"）。**`to-plan` 与 `to-issues` 合并为单一的 `to-tickets` skill，`to-issues` 被删除。**

  `to-tickets` 把一份计划、spec 或对话拆成一组 **tickets** —— 示踪弹式的垂直切片，每张声明自己的 **blocking edges**。同一个产物在 `/setup-matt-pocock-skills` 配置的 tracker 下有两种读法：**本地文件**（`tickets.md`）以文字形式写下阻塞边，你手工按从上到下推进；**真实 tracker** 把它们写成原生阻塞链接，于是任何"阻塞已解除"的工单都处于 frontier 上，多个 agent 可以并行开跑。无论哪种介质，阻塞边都写在工单里 —— 介质只决定是否真的有人会并行去执行它们。

  在发布时，优先使用 tracker 的**原生 sub-issues** 来表示 parent → slice，以及**原生 blocking edges** 来表示 `Blocked by`（当 tracker 支持时），并把 `## Parent` / `## Blocked by` 这些正文小节作为兜底。"What to build" 模板指向 `/prototype` 代码所在的位置，而不是从里面内联一段代码片段。

  `ask-matt` 的主流程现在按 `idea → /to-spec → /to-tickets → /implement` 来路由，并在 `docs/engineering/to-spec.md` 与 `docs/engineering/to-tickets.md` 处各有一份面向用户的文档页。

- [#464](https://github.com/mattpocock/skills/pull/464) [`0557d57`](https://github.com/mattpocock/skills/commit/0557d57579d9b3d39839fdaf8d4a6542b17539ce) 致谢 [@mattpocock](https://github.com/mattpocock)! - 在文档里把 wayfinder 定位成一条**情境化的接入支线**，而不是新的主入口流程 —— 由访谈领头的 _idea → ship_ 链仍然是正门（把 wayfinder 捧成默认脊梁是一次 v2 级的动作，不属于 1.1）。**`ask-matt`** 路由现在把 wayfinder 的具体触发条件写出来 —— 一个绿地项目，或者一项超出单个会话承载能力的大功能构建 —— 而两条访谈正门（**`grill-me`**、**`grill-with-docs`**）则**向上**指给 wayfinder，用于"单次会话装不下"的工作量，使接入支线在读者真正起步的地方就能被发现。

- [#464](https://github.com/mattpocock/skills/pull/464) [`639df6e`](https://github.com/mattpocock/skills/commit/639df6e7386dfddc739b2aecdeff37a876f2483b) 致谢 [@mattpocock](https://github.com/mattpocock)! - 推广并重新定位 **`wayfinder`** —— 用于规划一块巨大的、超出单个 agent 会话承载能力的工作。它从 `in-progress/` 搬出，进入 `engineering/`（插件条目、顶层 + Engineering README 的 **User-invoked** 列表、`docs/engineering/wayfinder.md` 文档页、`ask-matt` 中的一条路由），以一份成熟 skill 落地。带它走到这一步的改名与重定位：

  - **`decision-mapping` 被重命名为 `wayfinder`**，调用方式是 `/wayfinder`。"Decision map" 又 jargon 又不准确 —— 实际上只有一种工单类型才是真正的决策。这次重新定位改成"为一片迷雾问题找路"，给出一套统一的引导词框架 —— **fog of war（战争迷雾）**、**frontier（前线）**、**the map（地图）** —— 而不是在其上再叠一个生造术语。
  - **以目的地为引导词**。寻路找的是通往目的地的**路**；它不会一头冲向建造。命名目的地是绘图的第一个动作 —— 它锁定范围、并塑造每一张工单 —— 因此地图多了一个 `## Destination` 字段作为每次会话的定向锚点，分诊在尚无任何工单之前就钉死它。
  - **只规划，不动手**。地图产出**决策而非交付物**；当构建者动手前已无任何决策待定时，它就完成了。一项工作可以在 Notes 里覆盖这一点。
  - **地图是索引，不是仓库**。一条决策只在一处存在 —— 它自己的工单 —— 因此地图只做摘要与链接，绝不复述；把迷雾升级为工单，就把那一块迷雾清除，使没有任何东西同时存在于两个地方。
  - **默认协作**。地图从一份本地 Markdown 文件搬到仓库的 issue tracker 上：一张单独的 `wayfinder:map` issue，它的工单是它的子 issue —— 一个团队可以盯着的共享 URL。会话以低分辨率加载地图，并按需放大到工单。Wayfinder 仍是 tracker-无关的（GitHub、GitLab、local-markdown），背后由 `docs/agents/issue-tracker.md` 中的指针承担，`setup-matt-pocock-skills` 会植入 "Wayfinding operations" 一节。
  - **认领靠分配，而非标签**。一次会话通过把工单分派给"在驾驭的 dev"来认领 —— assignee **就是**认领 —— 从而把标签词汇腾给单一的 `wayfinder:<type>`。
  - **原生阻塞**。阻塞优先使用 tracker 的原生依赖关系，它会在 tracker 自身的 UI 中把 frontier 视觉化呈现，让人无需打开地图就能看到"哪些是可领的"。GitHub 与 GitLab 模板详细给出原生写法，并以正文中的约定为兜底。
  - **迷雾 vs. 范围外，分开**。两个直白命名的地图小节 —— `## Not yet specified`（范围**内**的迷雾，随 frontier 推进升级）与 `## Out of scope`（已被裁定超出目的地的工作，关闭、永远不升级） —— 让"超出目的地"的工作不再读起来像可领取的 frontier。
  - **第四种 `task` 工单类型**。用于阻塞某条决策的纯人工工作（provisioning 访问权限、迁移数据、注册服务） —— 唯一一种**做事**而非做决策的类型，凭"为决策解封"赢得一席之地。
  - **HITL / AFK 工单分类**。每种工单类型都是 **HITL**（人在回路 —— grilling、prototype）或 **AFK**（agent 单干 —— research；task 两者皆是）。HITL 工单只能通过现场交互解决，于是"等人"自然从标签里拿掉 —— 一个自己抢答问题的 grilling agent，**按定义**就违反了 HITL。（这修复了学生们报告的 `/wayfinder` 拷问**自己**而非拷问人的问题。）
  - **无雾提前退出**。如果开局广度优先的访谈没有冒出迷雾，那这条路小到一次会话就能跑完 —— 于是它停下来、问你想怎么处理，而不是建一张没人需要的地图。

### Patch Changes

- [#464](https://github.com/mattpocock/skills/pull/464) [`639df6e`](https://github.com/mattpocock/skills/commit/639df6e7386dfddc739b2aecdeff37a876f2483b) 致谢 [@mattpocock](https://github.com/mattpocock)! - 把 **`tdd`** 改造成一份"仅作参考"的 skill，并补上一个缺失的反模式。

  **仅作参考**。红 → 绿 → 重构循环由模型已经掌握的引导词锚定，因此"逐步骤 Workflow"很大程度上只是在复述这个循环。删掉 Workflow 与每周期 checklist；把它们唯一耐用的想法 —— 垂直切片 / 示踪弹 —— 折进 Anti-patterns 一节和一份简短的 Rules-of-the-loop 清单。引入 **seam（接缝）** 作为"测试落在哪里"的引导词：只在预先约定的接缝上做测试，并在写任何测试前先与用户确认。同时删掉重构阶段 —— TDD 现在是红 → 绿；重构属于评审阶段，于是"重构规则"和 `refactoring.md` 也随之搬出（它的归宿在 `code-review`）。

  **循环论证式的测试**。新增"循环论证式测试"反模式：一种断言按代码自身的计算方式重新算出来的测试，它在构造上就能通过，给出零信心 —— 区别于已经在覆盖的"实现耦合"反模式。在同等位置添加为同级条目：一条 Philosophy 原则（期望值必须来自独立的事实来源）、一道 checklist 闸口、以及 `tests.md` 中一对 BAD/GOOD 示例。

- [`e00eadb`](https://github.com/mattpocock/skills/commit/e00eadb4bb32c3d5a631ead1a5ed5d6a7c5f74e2) 致谢 [@mattpocock](https://github.com/mattpocock)! - 把 **`triage`** skill 扩展为：分诊外部 PR，把 PR 当作"带着代码的 issue"跑过同样的角色与状态机。PR 与 issue 并列内联流动（由每个仓库 setup 时的一个开关守门），发现流程只暴露外部 PR，仅限 bug 的"reproduce"步骤被泛化为一个统一的 "verify the claim" 步骤，一道冗余检查会把"已经实现"的请求归结为 `wontfix`，而不污染"范围外"知识库。`setup-matt-pocock-skills` 新增一个 GitHub/GitLab 的"PR 作为请求面"开关。

- [#472](https://github.com/mattpocock/skills/pull/472) [`d869d45`](https://github.com/mattpocock/skills/commit/d869d45afc32beab1c2d1350f8de5e81589512cd) 致谢 [@mattpocock](https://github.com/mattpocock)! - 修复 **`wayfinder`** 把 issue-tracker 文档路径硬编码的问题 —— 这破坏了整套 skill 所依赖的间接寻址。

  `to-issues`、`to-prd`、`triage` 从不指名任何路径 —— 它们通过 `setup-matt-pocock-skills` 写入 `CLAUDE.md` / `AGENTS.md` 的 `### Issue tracker` 块来解析 tracker，该块指向 tracker 文档所在的位置。Wayfinder 反而钉死了字面量 `docs/agents/issue-tracker.md`，因此在一个把 agent 文档放在别处的仓库里，它会悄悄回退到 local-markdown tracker —— 即便那个仓库的 `CLAUDE.md` 明明写着 GitHub issues。它现在改用同样的指针来解析文档，并按节名读取它的 "Wayfinding operations" 一节，使整套间接寻址保持一致。

## 1.0.1

### Patch Changes

- [`d20ee26`](https://github.com/mattpocock/skills/commit/d20ee2684e2a9442698ac3c1e0f2c5b68c4cf296) 致谢 [@mattpocock](https://github.com/mattpocock)! - 让 **`teach`** skill 优先复用。课程现在从 `./assets/` 下的可复用**组件**搭建 —— 样式表、测验 widget、模拟器、图表助手。复用是默认：agent 先读 `./assets/`，再从中搭建，并把任何新出现且值得复用的东西抽成组件，而非内联。

## 1.0.0

### Major Changes

- [`47bde84`](https://github.com/mattpocock/skills/commit/47bde84da032afb2e5058f997f3bbca47d321dbd) 致谢 [@mattpocock](https://github.com/mattpocock)! - 新增 **`ask-matt`** skill —— 一份用户调用的路由，根据你的情境指向正确的 skill 或流程。

  **破坏性变更**：`ask-matt` 在本仓库的其他用户调用 skill 上做路由，因此它假定它们都已安装。

- [`47bde84`](https://github.com/mattpocock/skills/commit/47bde84da032afb2e5058f997f3bbca47d321dbd) 致谢 [@mattpocock](https://github.com/mattpocock)! - 引入共享的设计 skill，并把既有 skill 接到它们身上。

  - 新增 **`codebase-design`** skill —— 深模块词汇（module、interface、depth、seam、adapter）以及"把小接口藏到大量行为背后"的原则。原本嵌在 `improve-codebase-architecture/LANGUAGE.md` 中的语言，现在搬到这里，泛化给各个 skill 复用。
  - 新增 **`domain-modeling`** skill —— 主动构建并打磨项目的领域模型，用词表压测术语、并保持 `CONTEXT.md` 与 ADR 时效。
  - `improve-codebase-architecture` 现在从 `/codebase-design` 取它的架构词汇，从 `/domain-modeling` 取它的领域模型。
  - `tdd` 现在借助 `/codebase-design` 来获取接口设计指引 —— 它内联的 `deep-modules.md` / `interface-design.md` 笔记被移除、改用共享 skill。
  - `grill-with-docs` 现在通过 `/domain-modeling` 在线构建领域模型。

  **破坏性变更**：这些 skill 现在依赖新增的 `codebase-design` / `domain-modeling`，因此你也必须安装它们。

- [`47bde84`](https://github.com/mattpocock/skills/commit/47bde84da032afb2e5058f997f3bbca47d321dbd) 致谢 [@mattpocock](https://github.com/mattpocock)! - 移除 **`caveman`** 与 **`zoom-out`** skill。

  - `caveman` 是我当时正在测试的另一份 skill 的副本、从未打算公开。
  - `zoom-out` 实际上从未被使用过，因此已从仓库移除。

  **破坏性变更**：这两个 skill 都已移除。

- [`47bde84`](https://github.com/mattpocock/skills/commit/47bde84da032afb2e5058f997f3bbca47d321dbd) 致谢 [@mattpocock](https://github.com/mattpocock)! - 把 **`diagnose`** skill 重命名为 **`diagnosing-bugs`**。

  **破坏性变更**：以 `/diagnosing-bugs` 调用 —— 旧的 `/diagnose` 名字已不存在。

- [`47bde84`](https://github.com/mattpocock/skills/commit/47bde84da032afb2e5058f997f3bbca47d321dbd) 致谢 [@mattpocock](https://github.com/mattpocock)! - 用 **`writing-great-skills`** 替换 **`write-a-skill`**。

  - 移除 `write-a-skill`。
  - 新增 `writing-great-skills`（加上它的 `GLOSSARY.md`） —— 一份关于"如何把 skill 写好、改好"的参考：词汇与原则，让 skill 可预期，逐句追杀 no-op。
  - 把 `grilling` 暴露为一份模型调用的 skill —— `grill-me` 与 `grill-with-docs` 背后的可复用访谈循环。

  **破坏性变更**：`write-a-skill` 已被移除；请改用 `writing-great-skills`。

### Minor Changes

- [`47bde84`](https://github.com/mattpocock/skills/commit/47bde84da032afb2e5058f997f3bbca47d321dbd) 致谢 [@mattpocock](https://github.com/mattpocock)! - 新增 **`resolving-merge-conflicts`** skill —— 一个用于解决进行中 git merge 或 rebase 冲突的循环。独立可用，不依赖其他 skill。

- [`47bde84`](https://github.com/mattpocock/skills/commit/47bde84da032afb2e5058f997f3bbca47d321dbd) 致谢 [@mattpocock](https://github.com/mattpocock)! - 把 skill 的分类法从 **Commands / Skills** 改为 **User-invoked / Model-invoked**，贯穿各文档，并新增 `docs/invocation.md` 来定义这一划分：用户调用的 skill 只有键入时才可达，它们的工作是"编排"；模型调用的 skill 也会在任务契合时被自动抓取。用户调用的 skill 可以调用模型调用的 skill，但不能再调用其他用户调用的 skill。

### Patch Changes

- [`47bde84`](https://github.com/mattpocock/skills/commit/47bde84da032afb2e5058f997f3bbca47d321dbd) 致谢 [@mattpocock](https://github.com/mattpocock)! - 收紧 **`review`** skill：fail-fast 的 ref 检查、单一来源的规则、以及删去 no-op。
