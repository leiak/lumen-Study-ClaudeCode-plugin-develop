# 权威安装说明块

一套安装故事，一套措辞。`README.md`、`.changeset/*` 以及 `docs/` 下每一页都必须**按此**说明，其他一概不可。先在这里修改，再向外传播。

`mattpocock-skills` 已被收录在 **Claude Code 官方市场**（配置名 `claude-plugins-official`，源仓库 `anthropics/claude-plugins-official`），每一份 Claude Code 安装都自带该市场。无需先添加任何市场。Anthropic 官方市场默认开启自动更新（见 [discover-plugins](https://code.claude.com/docs/en/discover-plugins)），因此"更新会自动到达"是事实，而不是期望。

## Claude Code：插件

<canonical-block name="claude-code">

```bash
claude plugins install mattpocock-skills
```

或者，在一个会话内：

```
/plugin install mattpocock-skills
```

它已在 Claude Code 官方市场中，无需先添加任何源，更新会自动到达。

</canonical-block>

## Codex 以及其他 agent：skills.sh

该插件仅限 Claude Code。其他场合，[skills.sh](https://skills.sh/mattpocock/skills) 会把可编辑的 skill 文件复制到项目里。在 `README.md` 中使用"整套"形式：

<canonical-block name="skills-sh-whole-set">

```bash
npx skills@latest add mattpocock/skills
```

挑选你想要的 skill，以及要安装到哪些编码 agent 上。**安装器允许你选择要拿哪些 skill：请确保其中包含 `setup-matt-pocock-skills`。**

</canonical-block>

…而在单独提到某个 skill 的地方，使用"单个"形式。注意 **`docs/` 下的页面并不是这段说明的消费方**：ai-hero 会在正文上方渲染安装小部件，于是页面再写一遍命令就成了重复。详见 [writing-docs.md](./writing-docs.md)。

<canonical-block name="skills-sh-one-skill">

```bash
npx skills@latest add mattpocock/skills --skill=<name>
```

```bash
npx skills@latest update <name>
```

</canonical-block>

`skills@latest` 在这三处都是固定写法。`docs/` 下的页面过去各自保留一份这些命令；现在这些块被直接删除而非纠正，因为站点会自己渲染安装命令。

## 两条路径互斥

插件是你订阅的、托管的、只读的 bundle；skills.sh 写入的文件归你所有、可以编辑。两个都装会让每条 skill 都装两遍：始终说"选一个"。

## 不属于安装故事的部分

`.claude-plugin/marketplace.json` 让本仓库自己成为单一插件市场（`/plugin marketplace add mattpocock/skills`，再 `/plugin install mattpocock-skills@mattpocock`）。官方市场已涵盖它。它被保留下来作为"直接安装本仓库"的兜底（未发布的 commit，或 fork），**不会**出现在用户文档里。
