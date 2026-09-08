---
name: writing-beats
description: 写作的 exploit 阶段；将原始素材组装成一段由 beats 串联的旅程，在某个 beat 依赖一个术语之前先将其 grounding。
disable-model-invocation: true
---

<what-to-do>

用户已传入（或将会传入）一份 markdown 原始素材文件。这是 **exploit**：探索已完成，素材池已固定。选定一条贯穿其中的路径，并从素材池中挖掘内容来填充每个 beat。

如果用户没有说明文章保存到哪里，请询问一次并记住该路径。

然后以 choose-your-own-adventure 的方式运行一段 beat-by-beat 旅程：

1. **确定 prerequisites（前置条件）**。在任何 beats 之前，与用户敲定读者走进文章时已经知道的内容：那些从一开始就是**grounded**（已锚定）的概念。其他一切都必须先由一个 beat grounding，然后才能被后续的 beat 依赖。参见 [Grounding](#grounding)。
2. 从原始素材中构思 2–3 个候选**起始 beats**。每个起始 beat 都是切入文章的不同入口。每个起始 beat 只能依赖已经 grounded 的概念；记下每个 beat grounding 了哪些新概念。在写入文章文件之前先向用户展示这些 beats。用户从中选择一个。预览这个选择会解锁哪些后续 beats，仿佛用户在顺着小路看到前面不远的地方。
3. 一旦用户选定起始 beat，将**只有这个 beat**写入文章文件。一个 beat 可以是一句话，也可以是几段文字，取决于这个 beat 本身自然需要多少。写完就停。
4. 重新从磁盘读取文章文件。然后提供 2–3 个候选**下一 beats**：从文章当前所在位置可以转向的不同方向。每个都必须从当前 grounded 集合可达；并记下每个 grounding 了什么。
5. 循环步骤 3–5，直到文章到达自然的结尾。

</what-to-do>

<supporting-info>

## Grounding

每个**概念**都必须在某个 beat 依赖它之前先被 **grounded**：读者要么走进文章时就已经知道它，要么在更早的 beat 中遇到过它。一个 beat 如果触及未 grounding 的概念，就会失去读者；这是整段旅程唯一无法做出的移动。衡量的单位是概念本身，而非它的词：即便行文中没有任何术语，一个 beat 仍可能依赖读者并不具备的想法。当一个概念有一个名字（一个**术语**）时，grounding 它意味着把这个想法和这个术语一起落到位。

一个概念通过以下两种方式之一被 grounding：

- **Prerequisite**：在第一个 beat 之前就已经 grounded。读者一开始就具备。在文章开头固定下来。
- **Introduced**：由某个 beat 确立，从此之后对所有后续 beat 都是 grounded 的。

因此每个 beat 承担两项工作：**要求**已经 grounded 的概念，并**grounding** 新的概念。维护一份当前已 grounding 内容的清单，并在每个 beat 落地时更新它。

这正是 choose-your-own-adventure 的形状。一个候选 beat 只有在它所要求的一切都已经 grounded 时才可达；选择 grounding 了概念 X 的 beat 会解锁所有一直在等待 X 的 beats。当你提供下一 beats 时，它们必须全部从当前 grounded 集合可达，并说明每个 grounding 了什么，这样用户就能看到它开启了哪些路径。

最大的杠杆在于把哪些概念设为 prerequisite、哪些概念在文章内部 grounding。起点要求太多，会把不具备它们的读者挡在门外；文章内 grounding 太多，则早段 beats 会淹没在定义中。在确定 prerequisites 时与用户敲定这一点，并在每当你心仪的某个 beat 实际上需要某个尚未 grounding 的概念时重新审视：要么在它之前加一个 grounding beat，要么把这个概念提升为 prerequisite。

## 什么是 beat

beat 是旅程中的一步。它只做一件事：铺陈场景、落下一个观点、提出一个问题、插入一段旁白、转换视角。然后它停下，把读者留在一个可以让下一个 beat 转向的位置。

beat 的大小由它所需决定：

- 如果只需要这一下，用一句话（"然后三周什么也没发生。"）。
- 如果这步需要铺垫，用一个短段落。
- 如果 beat 本身是一个完整的小故事、论证或示例，可以用多个段落。

如果一个"beat"需要五段加三个子标题，那它就不是一个 beat；它是两个 beat 粘在一起。把它拆开。

## 从素材池中取材

从原始素材池中抽取材料来填充每个 beat。你可以改写、拆分、重组或直接引用。素材池是一座 quarry。

## 旅程的结束

文章在旅程完成时结束，而不是在素材池被掏空时结束。大多数素材池会剩下一些没能入选的片段。这很正常；这正是手握比所需更多原始素材的意义所在。

## 写作节奏

- 一次只写一个 beat。永远不要超前写。
- 每次写入前重新从磁盘读取文章文件。绝对保留用户的编辑。
- 如果用户对前一个 beat 做了实质性修改，让它影响接下来的内容。
- 如果用户说 "rewrite that beat" 或 "go back and try a different beat 3"，照做：就地编辑，其他内容不动。

</supporting-info>
