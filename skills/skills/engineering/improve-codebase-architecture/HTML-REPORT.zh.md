# HTML 报告格式

架构评审以 OS 临时目录中的单个自包含 HTML 文件形式呈现。Tailwind 和 Mermaid 都来自 CDN。Mermaid 可靠地处理图状图表；手工构建的 div 和内联 SVG 处理更具编辑性的视觉（质量图、横截面）。将两者混合：不要一切都依赖 Mermaid，否则会开始看起来很通用。

## 脚手架

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <title>Architecture review for {{repo name}}</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script type="module">
      import mermaid from "https://cdn.jsdelivr.net/npm/mermaid@11/dist/mermaid.esm.min.mjs";
      mermaid.initialize({ startOnLoad: true, theme: "neutral", securityLevel: "loose" });
    </script>
    <style>
      /* Tailwind 不能干净覆盖的小型自定义层：
         虚线 seam 线、手绘感的箭头头部，等等。 */
      .seam { stroke-dasharray: 4 4; }
      .leak { stroke: #dc2626; }
      .deep { background: linear-gradient(135deg, #0f172a, #1e293b); }
    </style>
  </head>
  <body class="bg-stone-50 text-slate-900 font-sans">
    <main class="max-w-5xl mx-auto px-6 py-12 space-y-12">
      <header>...</header>
      <section id="candidates" class="space-y-10">...</section>
      <section id="top-recommendation">...</section>
    </main>
  </body>
</html>
```

## Header

仓库名称、日期和紧凑的图例：实心框 = module，虚线 = seam，红色箭头 = 泄漏，粗暗框 = deep module。没有介绍段落。直接进入候选。

## 候选卡片

图表承载重点。散文稀疏、平实，并毫不犹豫地使用词汇表中的术语（来自 `/codebase-design` skill）。

每个候选是一个 `<article>`：

- **Title**：简短，命名该加深（例如 "Collapse the Order intake pipeline"）。
- **Badge row**：建议强度（`Strong` = emerald，`Worth exploring` = amber，`Speculative` = slate），加上一个依赖类别标签（`in-process`、`local-substitutable`、`ports & adapters`、`mock`）。
- **Files**：等宽列表，`font-mono text-sm`。
- **Before / After diagram**：核心。左右两列，并排。参见下面的模式。
- **Problem**：一句话。痛在哪里。
- **Solution**：一句话。要改变什么。
- **Wins**：项目符号，每个 ≤6 个词。例如 "Tests hit one interface"、"Pricing logic stops leaking"、"Delete 4 shallow wrappers"。
- **ADR callout**（如果适用）：琥珀色调框中的一行。

没有段落解释。如果图表需要一段话来理解，重画图表。

## 图表模式

选择适合该候选的模式。混合它们。不要让每个图表看起来一样。多样性本身就是重点。

### Mermaid graph（依赖 / 调用流的主力）

当重点是 "X 调用 Y 调用 Z，看看这一团糟" 时，使用 Mermaid `flowchart` 或 `graph`。将其包装在 Tailwind 样式化的卡片中，使其不显得突如其来。使用 classDef 为泄漏边着色为红色、为 deep module 着色为暗色。序列图很适合 "before：6 次往返；after：1 次。"

```html
<div class="rounded-lg border border-slate-200 bg-white p-4">
  <pre class="mermaid">
    flowchart LR
      A[OrderHandler] --> B[OrderValidator]
      B --> C[OrderRepo]
      C -.leak.-> D[PricingClient]
      classDef leak stroke:#dc2626,stroke-width:2px;
      class C,D leak
  </pre>
</div>
```

### 手工构建的 box-and-arrows（当 Mermaid 的布局与你作对时）

将 module 作为带边框和标签的 `<div>`。箭头作为内联 SVG `<line>` 或 `<path>` 元素，相对于容器绝对定位。当你希望 "after" 图表感觉像一个带粗边框的 deep module，内部的元素被灰化时使用此选项，因为 Mermaid 不会以正确的粗细渲染它。

### Cross-section（适合分层浅层）

堆叠水平条带（`h-12 border-l-4`）以显示调用穿过的层。Before：6 个薄层，每层什么都不做。After：1 个粗条带，标签为合并后的职责。

### Mass diagram（适合 "接口与实现一样宽"）

每个 module 两个矩形：一个用于接口表面积，一个用于实现。Before：接口矩形几乎和实现矩形一样高（浅层）。After：接口矩形矮，实现矩形高（deep）。

### Call-graph collapse

Before：作为嵌套框呈现的函数调用树。After：同一棵树被折叠成一个框，原本内部的调用在其中以淡色显示。

## 样式指导

- 偏向编辑性，而不是企业 dashboard。宽松的留白。标题可选衬线（`font-serif` 与 stone / slate 配合得很好）。
- 颜色节制使用：一种强调色（emerald 或 indigo）加上红色的泄漏和琥珀色的警告。
- 将图表保持在约 320px 高，以便 before / after 舒适地并排坐下，无需滚动。
- 对图表内的 module 标签使用 `text-xs uppercase tracking-wider`，以便它们读起来像示意，而不是 UI。
- 唯一的脚本是 Tailwind CDN 和 Mermaid ESM import。报告在其他方面是静态的：除了 Mermaid 自己的渲染之外没有应用代码、没有交互性。

## Top recommendation 部分

一个更大的卡片。候选名称、一句话说明原因、指向其卡片的锚链接。仅此而已。

## Tone

简洁的普通英语，但架构名词和动词直接来自 `/codebase-design` skill。简洁不是漂移的借口。

**精确使用：** module、interface、implementation、depth、deep、shallow、seam、adapter、leverage、locality。

**绝不替换：** component、service、unit（指 module 时）· API、signature（指 interface 时）· boundary（指 seam 时）· layer、wrapper（指 module 时，当你的意思是 module 时）。

**适合该风格的措辞：**

- "Order intake module is shallow: interface nearly matches the implementation."
- "Pricing leaks across the seam."
- "Deepen: one interface, one place to test."
- "Two adapters justify the seam: HTTP in prod, in-memory in tests."

**Wins bullets** 用词汇表术语命名收益：*"locality: bugs concentrate in one module"*、*"leverage: one interface, N call sites"*、*"interface shrinks; implementation absorbs the wrappers"*。不要写 *"easier to maintain"* 或 *"cleaner code"*，因为这些术语不在词汇表中，不值得占有一席之地。

不要含糊、不要开场白、不要 "it's worth noting that…"。如果一句话可以是一个项目符号，就让它成为项目符号。如果一个项目符号可以被删除，就删除它。如果一个术语不在 `/codebase-design` 词汇表中，在发明新术语之前先找一个已有的。
