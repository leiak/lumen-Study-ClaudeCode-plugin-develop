# 模型调用 vs 用户调用

本仓库的每一份 `SKILL.md` 就是一个 skill。划分它们的唯一维度是 **调用** —— 谁可以触达它：

- **用户调用（User-invoked）**：**只能由人键入其名字**来触发。在 frontmatter 中设置 `disable-model-invocation: true`（Claude Code），并在 `agents/openai.yaml` 中设置 `policy.allow_implicit_invocation: false`（Codex）。`description` 是**面向人的**：是一行总结，供浏览斜杠命令的人阅读。要把触发列表（"Use when the user says…"）剥掉。
- **模型调用（Model-invoked）**：**模型或人都可以**触发。这是默认：在 `agents/openai.yaml` 中省略 `disable-model-invocation` 和 `policy` 块。`description` **面向模型**，保留丰富的触发措辞（"Use when the user wants…, mentions…, asks for…"），以便自动调用能命中。"一份 skill 是否应保持模型调用"的检验标准是：_模型能否在自主情况下、有效地伸手抓取它？_（复用是抽离一份 skill 的理由，不是检验标准。）

每个 harness 都用自己的方式把"用户调用"的 skill 排除出模型触达范围，于是除人之外的任何东西都不能触发它：其他 skill 也不行。一份用户调用的 skill 可以调用模型调用的 skill，但不能再触达另一份用户调用的 skill。

每个 skill 在其 `SKILL.md` 旁边还各有一份 `agents/openai.yaml`。它承载 Codex UI 元数据：`interface.display_name` 与 `interface.short_description` 用于 skill 选择器，以及 —— 对用户调用的 skill —— 与 `disable-model-invocation` 配对的 `policy.allow_implicit_invocation: false`。让两者保持同步：一份 skill 在两个 harness 中要么都是用户调用，要么都不是。

桶的 `README.md` 与顶层 `README.md` 把条目按 **User-invoked** 和 **Model-invoked** 分组。

## 它们之间的依赖

依赖通过**显式调用 Skill 工具**配合具名 skill（`Call the Skill tool with "grilling"`）来表达，而不是深层的 `../other-skill/FILE.md` 交叉引用，也不是留给模型自行解读的裸 `/skill` 风格提及。点出工具名才是触发它的关键：大多数 harness 把 skill 调用暴露为模型可调用的工具，把这一点讲明白比把一个 `/name` 放进散文里、指望它被读成命令，命中率更高。去掉开头的 `/` 也让这段表述对 harness 中立：`skill-name` 这种纯名字不预设它属于哪个 harness 的触发语法。共享的参考文档住在拥有它们的那份 skill 内部；其他 skill 通过调用 Skill 工具来取那份材料，而不是跨文件夹做链接。

这是关于**操作性的**指令：一份 skill 自己的步骤告诉 agent "立刻去跑另一份 skill"。仅在路由散文中、为了让人挑选而列举 skill 名字（`ask-matt`、桶的 `README.md`），并不触发任何东西，所以它们保留 `/skill` 风格的名字作为普通标签。

Skill 工具一次只接一个 skill。一个步骤需要两份 skill 时就是两次调用，而不是一次调用传两个名字：直接说（`Call the Skill tool twice, for "grilling" and "domain-modeling"`），而不要写"call it with X and Y" —— 后者会被读成"一次调用同时接两者"。

这条惯例只在被命名的 skill 是**模型调用**时成立。一份用户调用的 skill 永远不能被这种方式触发，没得商量：根据上面那条不变式，没有其他 skill 能调用它，包括把它的名字扔给 Skill 工具。当某一步的前置条件是一份用户调用的 skill（例如 `setup-matt-pocock-skills`）时，把它写成给"人"的指令："告诉用户运行 `/setup-matt-pocock-skills`"，永远不要写成 Skill 工具调用。

## 被动 vs 主动的领域工作

仅仅为了拿词汇而**读** `CONTEXT.md` 是一句散文指针，不是 `domain-modeling` skill。只有"主动构建/打磨"的那套纪律（挑战术语、边界场景、写 ADR、就地更新 `CONTEXT.md`）才是 `domain-modeling`。
