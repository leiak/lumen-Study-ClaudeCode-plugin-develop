## What it does

`wizard` 生成一个交互式 bash 脚本,一步一步地带领一个人走完一个手工流程:接入第三方服务、跑一次性迁移、把项目从状态 A 移动到状态 B。它打开每个 URL,告诉你该点哪、复制什么,捕获返回的内容,并把它们写入 `.env` 文件和 GitHub Actions secrets。

[agent](https://www.aihero.dev/ai-coding-dictionary/agent) 写这个脚本;它从不跑它。你,在你自己的机器上,跑它。所以 wizard 不是一份你照着做的指令清单;它是一段驱动流程并持有状态的程序,而你的部分是点击、粘贴和按 Enter。

## When to reach for it

你可以输入 `/wizard`,agent 也可以自己调用它。当它撞上一个你必须亲自做的步骤(它不能铸造的一个 key,它不能点的一个 dashboard),它会为你搭一个 wizard,而不是把指令写进聊天,那里它们会滚走。

当下一步阻塞你的是一趟 dashboard 时,调用它:

| 处境 | wizard 做什么 |
| --- | --- |
| 一位新 dev 需要配置六个服务才能启动应用 | 依次打开每个 dashboard,捕获 keys,把它们写入 `.env` 和 CI |
| 一次一次性迁移需要按特定顺序切换开关 | 把不可逆的步骤按确认门串起来 |
| 一个项目需要从状态 A 移到状态 B,只做一次 | 走完这次过渡,并报告它做不了的事 |
| 你正要把那些步骤写进 README | 改写成一个可执行版本,它不会那么安静地腐烂 |

不要用 wizard 来 *决定* 要构建什么;为此用 [grill-with-docs](https://aihero.dev/skills-grill-with-docs) 和 [to-spec](https://aihero.dev/skills-to-spec)。

## Prerequisites

生成一个不需要任何东西。Wizard 写的脚本跑在 bash 上,当某个阶段设置 GitHub secret 或 variable 时用 `gh`。如果 `gh` 缺失或未认证,该阶段会变成一个警告,收尾总结告诉你手工要设什么,而不是让 run 失败。

## Stages

一个 **stage** 是屏幕上一个聚焦的任务。脚本在 stage 之间清空终端,所以一个溢出屏幕的 stage 会丢掉滚走的那部分。你按依赖顺序写 stages 并设置 `TOTAL_STAGES`,它驱动进度显示。

范围界定先于任何一行被写下。该 [技能](https://www.aihero.dev/ai-coding-dictionary/skill) 读仓库而不是冷启动去问:`.env*`、`docker-compose*`、框架配置、以及 `.github/workflows/` 里每个 `secrets.*` / `vars.*` 引用:每一项都是 wizard 必须产出的值。然后它向你展示排序好的 stage 列表以确认,只有在那之后才把每个 stage 映射到一个人走的精确路径("Dashboard → Developers → API keys → Reveal test key → copy")。在它不知道当前 UI 的地方,它会问你或查文档,而不是凭空发明点击。

对每个被捕获的值,范围界定会确定它落在哪里:

| 目的地 | 何时 |
| --- | --- |
| 仅 `.env` | 本地 dev 需要它,CI 不需要 |
| GitHub secret | CI 读它,且它是敏感的 |
| GitHub variable | CI 读它,但它是公开的 |
| `.env` 和 secret 都要 | 本地 dev 和 CI 都需要它 |
| 都不写 | 这个 stage 是一个纯动作:切换一个开关,升级一个 plan |

## The template already solves the UX

[模板](https://github.com/mattpocock/skills/blob/main/skills/engineering/wizard/template.sh) 承载整个体验:进度加剩余时间、确认门、跨平台 URL 打开(含 WSL)、secret 的隐藏输入、幂等的 `.env` upserts、`gh secret` / `gh variable` 写入,以及一份它不得不跳过的所有东西的收尾总结。`STAGES` 标记之上的全部都是固定库,在每个 wizard 中都一样,从不手工编辑。一致性就是要点。你要做的只是范围界定流程并写它的 stages。

写 wizard 的那个 agent 从头到尾都不跑它,因为它开浏览器并等人工输入。它改为静态验证:`bash -n`、能用的话 `shellcheck`,以及一条 trace:每个值都落在范围界定说它该落的地方,每个 `set_secret` 名字匹配 CI 中一个真实的 `secrets.*` 引用。把期望放低:第一次 run 是你的,而那次 run 才是测试。

## Ephemeral by default

| 你拥有的 | 对脚本怎么做 |
| --- | --- |
| 一次性迁移、个人设置、一次性的过渡 | 存到 scratch 或 `scripts/` 路径,跑,删 |
| 仓库里下一个人也会需要这条 setup 路径 | 提交它,并从 README 链它,这样他们跑脚本而不是再问 agent |

## Common questions

**我的 API keys 会进模型的上下文吗?**
不会。Agent 写脚本;它不跑它。你自己跑脚本,它用隐藏的终端输入捕获 key,并直接写入 `.env` 或 `gh secret`。Wizard 是个 CLI,而模型没和它连。一个 caveat:这只对 wizard 在运行时捕获的值成立。如果你在范围界定的过程中把 key 粘进聊天,它就和任何其他粘贴的文本一样进了[上下文](https://www.aihero.dev/ai-coding-dictionary/context)。

**我能不能回去修一个我打错的值?**
运行中不能。没有 back 按钮:stages 向前跑,stage 3 上一个错的回答意味着 Ctrl-C 并重跑。重跑被设计为便宜:任何已经写到 `.env` 的值会作为默认值提供回来,所以你一路按 Enter 走已经答对的 stages,只重打那个错的。这件事在发布周被提出,之后没被关闭:"loved it! One thing though, is there a way to go back and correct what you've entered?"

有一个相关的 open bug。一个 `ask` prompt 中的箭头键插入 `^[[D` / `^[[C` 而不是移动光标,因为这个 prompt 用 `read -r` 而非 Readline ([issue #741](https://github.com/mattpocock/skills/issues/741))。Backspace work;箭头键不 work。删回到错误处,而不是把光标移进去。

**它知道我已设了什么吗?**
部分能,且比发布时的反应所暗示的少。它在问之前先读仓库(你的 `.env` 文件、`docker-compose`、框架配置、CI 里的 `secrets.*` 引用),所以它范围界定到真正缺失的值,而不是像 README 那样从零开始。它不做的是检查第三方服务。如果一个 key 已在你的 `.env` 里,wizard 会把它作为默认提供,Enter 就保留;如果你已经创建 Stripe 账户但从未保存 key,wizard 仍然把你送到 dashboard 去取。

**它在工作流里的什么位置,在 grilling 和 spec 之后?**
没什么特别的位置。它是一个独立工具,不是链上的一步。常见的猜测是 `/grill-with-docs → /to-spec → /wizard`,这条顺序没问题,但触发点是出现了一个手工流程,这可以在任何时刻发生:开始前、构建中、或上线很久之后。它也作为发现工具 work:范围界定把一项任务的隐藏先决条件(比如你没想到的三个 API keys)摆到台面上,在你承诺工作之前。

**它在 Claude Code 之外能用吗?**
工件可以,无条件:它是一段普通 bash 脚本,不 care 哪个 [harness](https://www.aihero.dev/ai-coding-dictionary/harness) 生成的它。该技能本身是模型调用的,所以它在各处都列出来:在 Claude Code 中输入 `/wizard`,或在 Codex 中输入 `$wizard`,或者直接描述你卡住的 setup。模型调用也让它避开了 [#693](https://github.com/mattpocock/skills/issues/693):在那里 Claude 的桌面和 web 端面把 *用户调用* 的技能从 [模型](https://www.aihero.dev/ai-coding-dictionary/model) 的列表里丢掉,并报告为未安装。

**它以前不是用户调用的吗?**
是的。现在它是模型调用的,所以 agent 在撞上一个你必须亲自做的步骤时主动调用它。你以前能做的事没一件被废掉:模型调用只是 *加上* agent 的主动触达,它从不拿走你的,所以 `/wizard` 仍然完全按以前那样 work。变的是它淘汰的失败模式:agent 在构建中途撞上一面凭据墙,然后把六条编号步骤倒进聊天让你手工照着做。

**它以前在 `in-progress/`:现在在哪?**
`engineering/`,自 v1.2 起。它从 beta 桶毕业,现在随插件一起发布,所以它跟其他已晋升的集合一起到达,而不是需要单独安装。它毕业时行为没变。

## It's working if

- 你看到一份有序的 stage 列表,以及每个 stage 产出的值,并被要求确认,然后才有任何脚本存在。
- 每个 URL 在你被要求粘贴来自该页的值之前就被打开。你永远不会被要求粘贴某个你没被派去取的东西。
- Secrets 盲打。任何敏感的东西都不会回显到你的滚动回显里。
- 每个 stage 装进一块屏幕。你还需要的东西没有滚走。
- Ctrl-C 并重跑从你上次停下的地方继续,把已经保存的值作为默认提供。
- 最后一个屏幕列出它写了什么,并单独列出它做不到而你得手工收尾的事。

## Where it fits

`wizard` 是一个随时可用的独立工具,坐在自动化停下、一个人必须点击的分界线上。它最近的邻居是 [setup-matt-pocock-skills](https://aihero.dev/skills-setup-matt-pocock-skills),因为两者都是为了把一个仓库带到可用状态:那一个配置这套技能,而 `wizard` 为其他所有东西生成一条 setup 路径。它也跟 [implement](https://aihero.dev/skills-implement) 配对:当一次构建落地了一个需要凭据或手工切换的特性时,wizard 是人的那一半如何被完成的方式。当你不确定哪个技能适合此刻,[ask-matt](https://aihero.dev/skills-ask-matt) 来路由。
