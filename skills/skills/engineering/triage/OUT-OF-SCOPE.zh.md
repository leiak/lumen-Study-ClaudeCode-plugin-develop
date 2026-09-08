# Out-of-Scope 知识库

仓库里的 `.out-of-scope/` 目录用来持久保存"被否决的功能请求"的记录。它有两个用途：

1. **机构记忆**：功能为什么被否决，让推理过程不会在 issue 关闭后消失
2. **去重**：当一条新 issue 进入、与某次历史否决匹配时，skill 可以把过去的决策摆出来，而不必重新吵一遍

## 目录结构

```
.out-of-scope/
├── dark-mode.md
├── plugin-system.md
└── graphql-api.md
```

按**概念**一个文件，不要按 issue 一个文件。多次请求同一件事的 issue 都汇总在同一个文件下。

## 文件格式

文件应当用一种放松的、可读的写法来写，更像一份简短的设计文档，而不是一条数据库记录。使用段落、代码片段与示例，让推理对第一次碰到它的人清晰、有用。

```markdown
# Dark Mode

This project does not support dark mode or user-facing theming.

## Why this is out of scope

The rendering pipeline assumes a single color palette defined in
`ThemeConfig`. Supporting multiple themes would require:

- A theme context provider wrapping the entire component tree
- Per-component theme-aware style resolution
- A persistence layer for user theme preferences

This is a significant architectural change that doesn't align with the
project's focus on content authoring. Theming is a concern for downstream
consumers who embed or redistribute the output.

```ts
// The current ThemeConfig interface is not designed for runtime switching:
interface ThemeConfig {
  colors: ColorPalette; // single palette, resolved at build time
  fonts: FontStack;
}
```

## Prior requests

- #42: "Add dark mode support"
- #87: "Night theme for accessibility"
- #134: "Dark theme option"
```

### 文件的命名

使用简短、具描述性的 kebab-case 来命名概念：`dark-mode.md`、`plugin-system.md`、`graphql-api.md`。名字应当足够有辨识度，让浏览这个目录的人不用打开文件也能知道否决了什么。

### 写明理由

理由应当有内容：不是"我们不想要这个"，而是为什么。好的理由会引用：

- 项目范围或理念（"本项目聚焦 X；主题化是下游需要关心的事"）
- 技术约束（"要支持这个就得做 Y，那与我们的 Z 架构冲突"）
- 战略决策（"我们选择用 A 而不是 B，是因为……"）

理由应当是持久的。避免引用临时情况（"我们现在太忙"）—— 那不是真正的否决，只是延后。

## 何时去查 `.out-of-scope/`

在 triage 阶段（第 1 步：收集上下文），读 `.out-of-scope/` 下的全部文件。在评估一条新 issue 时：

- 看这条请求是否与某条现有的 out-of-scope 概念匹配
- 匹配按"概念相似度"而非关键词："night theme" 匹配 `dark-mode.md`
- 若有匹配，把它摆给维护者："This is similar to `.out-of-scope/dark-mode.md`. We rejected this before because [reason]. Do you still feel the same way?"

维护者可能：

- **确认**：新 issue 被追加到现有文件的"Prior requests"列表，然后关闭
- **重新考虑**：删掉或更新那份 out-of-scope 文件，新 issue 进入正常 triage
- **不同意**：两条 issue 有关但不同，进入正常 triage

## 何时去写 `.out-of-scope/`

只有**增强（enhancement）**（不是 bug）被否决为 `wontfix` 时才写。这条规则对 PR 同样适用：被否决的 PR 也要记到这里，以免同一请求换身代码又回来。

不要在"由于**已实现**而以 `wontfix` 关闭"的情况下去写这里。那是一项已构建的特性，而不是被否决的；把它记下来会用"伪否决"污染去重检查。关闭的评论应改为指向"它已经住在哪里"。

流程：

1. 维护者决定某项功能请求 out of scope
2. 看是否已存在一份匹配的 `.out-of-scope/` 文件
3. 有：把新 issue 追加到 "Prior requests" 列表
4. 没有：以概念名新建一份文件，写入决策、理由和第一份历史请求
5. 在 issue 上发一条评论解释决策，并提到 `.out-of-scope/` 文件
6. 以 `wontfix` 标签关闭 issue

## 更新或移除 out-of-scope 文件

如果维护者改变了主意、对过去否决的概念有了新看法：

- 删掉那份 `.out-of-scope/` 文件
- skill 不必重新打开旧 issue；它们是历史记录
- 触发重新考虑的那条新 issue 走正常 triage
