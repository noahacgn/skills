# HTML 报告格式规范 (HTML Report Format)

架构审查结果在操作系统的临时目录下渲染为单个自包含的 HTML 文件。Tailwind 和 Mermaid 均通过 CDN 引入。Mermaid 用于稳定绘制图结构关系；手写的 div 和内联 SVG 则用于更具视觉表现力的图表（体量图、剖面图）。将二者结合使用，切勿全盘依赖 Mermaid，否则整体视觉容易千篇一律。

## 脚手架模板 (Scaffold)

```html
<!doctype html>
<html lang="zh-CN">
  <head>
    <meta charset="utf-8" />
    <title>{{repo name}} 架构审查报告</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script type="module">
      import mermaid from "https://cdn.jsdelivr.net/npm/mermaid@11/dist/mermaid.esm.min.mjs";
      mermaid.initialize({ startOnLoad: true, theme: "neutral", securityLevel: "loose" });
    </script>
    <style>
      /* 定制辅助样式，覆盖 Tailwind 未能直接覆盖的微小细节：
         虚线缝隙、手绘感箭头等 */
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

## 页头 (Header)

仓库名、日期以及紧凑的图例：实线框 = 模块，虚线 = 缝隙，红色箭头 = 抽象泄漏，深色加粗框 = 深模块。无需客套引言段落，直奔候选方案。

## 候选方案卡片 (Candidate card)

图表承载核心表达。正文精炼克制，自然使用来自 `/codebase-design` 的专业术语。

每个候选方案对应一个 `<article>` 容器：

- **标题 (Title)**：简短，直接命名深化操作（如“收敛 Order 受理管道”）。
- **徽章栏 (Badge row)**：推荐力度（`强烈推荐 (Strong)` = 翡翠绿 emerald，`值得探索 (Worth exploring)` = 琥珀黄 amber，`前瞻设想 (Speculative)` = 石板灰 slate），外加依赖分类标签（`进程内依赖 (in-process)`、`本地可替代依赖 (local-substitutable)`、`端口与适配器 (ports & adapters)`、`Mock (mock)`）。
- **涉及文件 (Files)**：等宽字体列表，`font-mono text-sm`。
- **改造前/后架构图 (Before / After diagram)**：核心焦点。两列并排。参见下文图表模式。
- **当前痛点 (Problem)**：一句话阐述。
- **改进方案 (Solution)**：一句话阐明改动。
- **预期收益 (Wins)**：要点列表，每条不超过 10 个字。例如“测试统一命中单接口”、“定价逻辑不再泄漏”、“删除 4 个浅层透传包装”。
- **ADR 关联提示 (ADR callout)**（若适用）：琥珀色提示框中的单行提示。

无需大段解释性文字。如果图表需要一整个段落才能看懂，重新绘制该图表。

## 图表模式 (Diagram patterns)

选用契合候选方案特征的图表模式，多种模式结合呈现：

### Mermaid 拓扑图（适用于依赖/调用流）

当核心意图是呈现“A 调用 B 调用 C，且调用关系混乱”时使用 Mermaid `flowchart` 或 `graph`。将其包裹在带有 Tailwind 样式的卡片中，避免显得突兀。通过 classDef 将泄漏边标为红色，将深模块标为深色背景。时序图非常适合表达“改造前：6 次往返调用；改造后：1 次”。

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

### 手绘框线与箭头（当 Mermaid 自动排版不尽如人意时）

使用带有边框和标签的 `<div>` 表示模块。箭头使用绝对定位在相对容器之上的内联 SVG `<line>` 或 `<path>`。当你希望“改造后”图表呈现为一个带有加粗边框、内部细节淡化灰显的深模块时选用此方式，Mermaid 难以渲染出这种视觉分量。

### 剖面层级图（适用于多层浅薄封装）

堆叠水平色带（`h-12 border-l-4`）展示调用穿越的层级。改造前：6 个无实质作为的单薄层级。改造后：1 个标记有整合职责的厚实色块。

### 体量占比图（适用于“接口与实现一样庞大”的浅模块）

每个模块包含两个矩形，一个代表接口表面积，一个代表实现体量。改造前：接口矩形与实现矩形几乎等高（浅模块）。改造后：接口矩形极矮，实现矩形高耸厚实（深模块）。

### 调用图收敛折叠 (Call-graph collapse)

改造前：渲染为嵌套盒模型的函数调用树。改造后：同一棵树收敛为一个整体大盒，原先的内部调用在内部以淡灰色示意呈现。

## 视觉风格指引

- 偏向技术社论（Editorial）风格，而非枯燥的企业 Dashboard。留白充裕。
- 克制用色：单主色调（翡翠绿或靛青蓝）+ 红色（用于泄漏）+ 琥珀黄（用于告警）。
- 图表高度保持在约 320px，以便改造前/后图表能舒适地并排呈现而无需横向滚动。
- 架构图内的模块标签采用 `text-xs uppercase tracking-wider`，读起来像原理图，而非业务 UI。
- 唯一的脚本依赖是 Tailwind CDN 和 Mermaid ESM 引入。报告本身保持静态，无复杂业务逻辑，无除 Mermaid 自带渲染外的其他动态交互。

## 首要推荐小节 (Top recommendation section)

单张醒目大卡片。候选方案名称、一句话推荐理由、跳转至该方案卡片的锚点链接。仅此而已。

## 语气与规范

简明精确，架构名词与动词严格来自 `/codebase-design` 技能：

**严格使用：** 模块 (module)、接口 (interface)、实现 (implementation)、深度 (depth)、深模块 (deep)、浅模块 (shallow)、缝隙 (seam)、适配器 (adapter)、杠杆率 (leverage)、局部性 (locality)。

**切勿混用：** 组件、服务、单元（指代模块时）· API、签名（指代接口时）· 边界（指代缝隙时）· 层、包装器（指代模块时）。

**收益要点**直接使用术语指明收获：*“局部性：Bug 集中在单一模块内”*、*“杠杆率：一个接口，N 处复用”*、*“接口收敛，实现吸收包装层”*。不要写“更易维护”或“代码更干净”这类模糊空洞的词汇。
