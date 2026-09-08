---
name: writing-fragments
description: "写作的 explore 阶段：挖掘原始 fragments，尚未确定结构。"
disable-model-invocation: true
---

<what-to-do>

这是纯粹的 **explore**：在尚未承诺任何结构的情况下，拓宽可以写什么的空间。承诺是 _exploit_，是另一个 skill 的工作。运行一次 grilling 会话以产出 fragments，毫不留情地就用户希望写的任何内容采访他们。强加阶段、大纲或文章结构不属于本 skill 的范围。

无论 fragments 来自对话的哪一方，都将它们追加到同一个 markdown 文件中。

如果用户没有传入路径，请询问一次将文档保存到哪里，然后在本会话剩余时间内记住它。

从用户说的第一句话开始就捕捉 fragments，包括最初的 prompt。

在首次写入时，在文件顶部放一个 H1 作为 working title（之后可以修改），除标题之外不写其他任何内容：不放元数据，不放目录，不放日期。

</what-to-do>

<supporting-info>

## 什么是 fragment

fragment 是任何可能存活到最终文章中的文字。它必须对作者**可读**（作者能明白它的意思），但它不需要定义自己的术语，也不必对冷启动的读者来说清楚明白。这里的标准是"这是一段好文字吗？"，而不是"这是一个自洽的论证吗？"

fragments 故意是异质的。可能成为 fragment 的例子：

- 一句尖锐的话，你想把它部署到某处但还不知道用在哪里。
- 一个主张，附一行理由。
- 一段小插曲：发生过的一件事、一段代码、一个场景、一则类比。
- 一个未成形的想法："有点关于 X 像 Y 这件事，之后再展开。"
- 一句引文、一段对话、一句无意听到的话。
- 一组由感觉彼此相连的相关观察。
- 一声抱怨、一段独白、一个梗。
- 一个 **leading word**：一个紧凑的隐喻或新造的词，整篇文章可以挂在它身上（就像 _tracer bullets_ 或 _fog of war_ 命名了一整个模式那样的一个词）。

在这些之中，leading word 是最值得落下的 fragment。它是承重的：在 explore 阶段为它取一个合适的名字，会塑造结构、过渡以及之后的标题，在整个 exploit 阶段持续带来回报。当对话围绕某个反复出现的想法打转时，努力为它造一个词。

小说家的日记是范例：经年累月未经整理的 noticings，之后被开采为原材料。Fragments 就是 noticings。

## 文件格式

```markdown
# Working title

A first fragment lives here.

It can be multiple paragraphs. It can include lists, code, quotes: whatever
shape the fragment naturally takes.

---

A second fragment.

---

> A quoted line that the user wants to keep around.

A reaction to it.

---

- A cluster of related observations
- That hang together by feel
- And want to be near each other
```

Fragments 之间用一个 horizontal rule（`\n---\n`）分隔。正文中没有标题。没有标签。除了它们被加入的顺序之外，没有顺序。

## 写作节奏

静默地追加。不要为每个 fragment 询问许可。顺带提一句你刚加的内容（"adding that"），但不要用保存对话框打断对话。

每次写入之前：从磁盘重新读取该文件。用户可能在两轮之间编辑、重新排序或删除过 fragments，所以要保留他们的更改。永远不要覆盖该文件；只能追加（或者在用户要求时，就地编辑某个特定的 fragment）。

用户可以随时说"cut the last one"、"rewrite that one sharper"、"merge those two"。把这些视为一等指令。

</supporting-info>
