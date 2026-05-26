---
name: ppt-slide-builder
description: >-
  PPT-style web page builder with unified theming, slide management,
  element animations, and slide transitions. Creates self-contained
  HTML presentations with custom theme support. Enforces strict
  modification rules for project stability.
  Triggered by: "制作PPT", "PPT网页", "幻灯片", "演示页面",
  "presentation", "slide deck", "slide builder", "PPT风格",
  "添加动画", "页面过渡", "翻页动画", "新建幻灯片",
  "修改幻灯片", "删除幻灯片", "演示文稿", "slides",
  "ppt builder", "slide animation", "slide transition".
version: 1.1.0
allowed-tools: Read Write Edit Glob Bash
---

# PPT Slide Builder

## Purpose

创建并维护**PPT 风格的全屏网页演示文稿**。每个演示项目使用统一样式（由用户指定的配色、字体、间距等主题参数），支持多页面管理、元素入场动画和页面过渡动画。所有操作（创建/修改/删除/动画）通过本 skill 严格控制，确保长期编辑后项目文件的稳定性和一致性。输出为纯静态 HTML，双击即可在浏览器中全屏播放。

## When to Use

- 用户要求"制作 PPT 网页"、"做一个幻灯片网页"、"演示文稿"
- 用户需要全屏翻页的网页演示，而非普通滚动页面
- 用户需要为已有演示添加/修改/删除页面
- 用户需要为页面元素添加入场动画
- 用户需要设置或修改页面之间的过渡效果
- 用户需要更改整个演示的主题样式

## When NOT to Use

- 用户需要可滚动的普通网页或 Landing Page —— 应使用 web-ui-builder
- 用户需要导出为 PowerPoint (.pptx) 文件 —— 本 skill 只生成 HTML
- 用户需要在线分享且需要多人协作编辑 —— 建议使用 Google Slides 等专业工具
- 用户需要复杂的图表动画或时间线动画 —— 建议使用 reveal.js 等框架

## Workflow

### Step 1: 初始化 PPT 项目（含用户提问流程）

在开始任何操作前，先确定用户的操作目标。如果是创建新项目，执行以下流程：

#### 1a. 解析用户已提供的信息

解析用户输入，提取已明确给出的信息，逐项标记"已提供"或"缺失"：

| 信息项 | 必需程度 | 说明 |
|--------|---------|------|
| 演示标题 | ★★★ 必需 | "做一个某某主题的PPT"中的主题即为标题 |
| 视觉风格 | ★★★ 必需 | 科技蓝、极简黑、温暖橙、商务风、自然绿、复古风等 |
| 配色细节 | ★★☆ 建议 | 深色/浅色背景、主色调方向 |
| 幻灯片页数 | ★★★ 必需 | 用户没说时至少要知道 1-2 页的内容方向 |
| 各页内容 | ★★★ 必需 | 每页要展示的核心内容（标题+要点） |
| 字体偏好 | ★☆☆ 可选 | 衬线体/无衬线体，不指定则根据风格自动匹配 |

#### 1b. 逐轮提问收集缺失信息

**提问原则（必须遵守）：**
- **一次只问 1-2 个问题**，等用户回答后再问下一轮。绝不一次性抛出一整份问卷
- **每个问题附带具体选项示例**："想要什么风格？比如科技蓝、极简黑、温暖橙、商务风？或者你有其他想法？"
- **对模糊回答追问细化**：用户说"科技风格" → 追问"深色背景还是浅色背景？主色调用蓝色还是青色？"
- **逐轮推进**：先问标题和风格 → 再问页数和内容 → 最后问细节
- **如果用户明确说"你定就好"**：使用合理的默认值并告知用户你的选择

**提问流程（逐轮执行，每轮等用户回答）：**

**第 1 轮：标题与整体风格**
```
检测到"演示标题"缺失 → 问："演示的主题/标题是什么？"
检测到"视觉风格"缺失 → 问："想要什么视觉风格？比如：
   · 科技蓝 —— 深蓝背景 + 亮色文字，适合技术/产品发布
   · 极简黑 —— 黑白配色 + 简洁排版，适合高端/设计感
   · 温暖橙 —— 暖色系 + 亲和力，适合分享/教育
   · 商务风 —— 浅色背景 + 蓝色点缀，适合正式汇报
   · 自然绿 —— 绿色调 + 柔和质感，适合环保/健康
   或者你有其他想法？"
```
(两个都缺失时一并问，否则只问缺失的)

**第 2 轮：配色与字体细节**
```
已有风格但未指定配色 → 问："倾向于深色背景还是浅色背景？主色调有偏好吗？"
已有风格但未指定字体 → 问："标题和正文的字体风格有偏好吗？
   比如正式的衬线字体（有装饰脚），或者现代的无衬线字体（简洁现代）？"
```

**第 3 轮：页数与内容**
```
检测到页数/内容缺失 → 问："大概需要几页？每页想展示什么内容？
   可以简单说一下各页的主题，我来帮你组织。"
   如果用户回答模糊 → 追问："第一页通常是标题页，从第二页开始
   想放什么内容？比如：介绍、数据、方案对比、团队信息等？"
```

**第 4 轮：补充细节**
```
根据前三轮收集到的信息，判断是否还需要其他细节。
如果用户已提供足够信息，跳过此轮直接进入生成。
```

#### 1c. 生成项目文件

1. 读取模板文件 `ppt-slide-builder/templates/base.html`，用收集的参数填充占位符：
   - `{{PRESENTATION_TITLE}}` → 演示标题
   - `{{TITLE}}` / `{{SUBTITLE}}` → 第一页标题/副标题
   - `{{SLIDE2_TITLE}}` / `{{SLIDE2_CONTENT}}` → 第二页内容（如果只有一页则删除）
   - `{{SLIDE_COUNT}}` → 幻灯片总数
   - `{{PROGRESS_PERCENT}}` → 初始进度百分比（1/总数 × 100）
   - **主题变量**：修改 `:root` 中的 CSS 变量（`--bg`、`--text`、`--accent`、`--font-display`、`--font-body` 等）

2. 创建项目文件：使用 `Write` 在 `./ppt-slide-builder/projects/<project-name>/index.html` 生成完整的演示文件。

3. 打开浏览器预览：`start "" "./ppt-slide-builder/projects/<project-name>/index.html"`


### Step 2: 添加新幻灯片

1. **确认幻灯片内容**：如果用户只说"加一页"但没有说明内容，必须追问：
   ```
   "新幻灯片想展示什么内容？比如：
    · 标题 + 正文（适合章节过渡页）
    · 标题 + 列表（适合要点列举）
    · 标题 + 图片（适合展示页）
    · 双栏布局（适合对比内容）
    · 引用/引文页（适合名人名言）
    你想要哪种布局？内容是什么？"
   ```
   如果用户只给了内容但没给布局，根据内容类型自动选择最合适的布局并告知用户。

2. 读取目标项目的 `index.html`，定位到 `</div>`（`.presentation` 的关闭标签）之前的最后一条 `<!-- /SLIDE -->` 注释。

3. 生成新幻灯片 HTML，包含：
   - 唯一的 `id="slide-N"`（N 为当前最大编号 + 1）
   - 正确的 `data-slide="N"` 属性
   - 递增的 `aria-label="Slide N"` 和 `role="region"`
   - 初始 `class="slide"`（不含 `active`）
   - `aria-hidden="true"`

4. 在最后一个 `<!-- /SLIDE -->` 之后、`</div>` 之前插入新幻灯片。

5. 更新导航逻辑中的 `#slideTotal` 值（将旧总数 +1）。

6. 确认 `.presentation` 中 `.slide` 元素的总数与导航显示一致。

7. **规范要求：**
   - 新幻灯片必须放在最后（不允许插入到中间，避免破坏编号连续性）
   - 幻灯片 ID 必须连续递增，不允许出现跳号
   - 新幻灯片内容根据用户需求填充，使用真实内容而非占位文本
   - 每次添加后立即更新导航栏的页码总数

### Step 3: 修改幻灯片内容

1. 用户指定要修改的幻灯片编号（如"修改第 3 页"）和新的内容。

2. 读取 `index.html`，使用 `<!-- SLIDE: slide-N -->` 和 `<!-- /SLIDE -->` 标记精确定位目标幻灯片区域。

3. 使用 `Edit` 工具替换该幻灯片 `<section>...</section>` 内的内容，**不修改标记行本身**。

4. **规范要求：**
   - 不得修改 `<!-- SLIDE: slide-N -->` 和 `<!-- /SLIDE -->` 标记
   - 不得修改幻灯片的 `id`、`data-slide`、`role` 属性
   - 不得修改其他幻灯片的内容
   - 修改后确认 HTML 结构完整（标签匹配）
   - 如果用户只要求修改内容，不要在修改过程中改变动画 class

### Step 4: 为元素添加入场动画

1. 用户指定目标幻灯片编号和要添加动画的元素（如"第 3 页的标题加动画"）。

2. 读取 `index.html`，根据 `<!-- SLIDE: slide-N -->` 标记定位目标幻灯片。

3. 根据 `references/animation-reference.md` 选择动画类型，在目标元素的 `class` 属性中添加：
   - 一个动画类名（如 `anim-fade-in`、`anim-slide-up` 等）
   - 按需添加 `anim-delay-N` 控制延迟

4. **规范要求：**
   - 只修改目标元素的 `class` 属性，不改变元素内容或结构
   - 如果目标元素已有其他动画类，先移除再添加新类（不叠加多个动画）
   - 延迟值 `anim-delay-N` 中的 N 不超过 10
   - 一个幻灯片中动画元素建议不超过 5 个
   - 不得修改 `<!-- SLIDE: -->` 标记或幻灯片容器属性

### Step 5: 修改页面过渡效果

1. 用户指定要使用的过渡类型（如"改成缩放过渡"）。

2. 读取 `index.html`，定位到 `<div class="presentation" id="presentation">` 行。

3. 从 `references/animation-reference.md` 中选择过渡类名（`trans-fade`、`trans-slide-left`、`trans-zoom` 等）。

4. 使用 `Edit` 替换 `presentation` 容器上的过渡类名：
   ```
   旧: <div class="presentation trans-slide-left" id="presentation">
   新: <div class="presentation trans-zoom" id="presentation">
   ```

5. **规范要求：**
   - 只修改 `presentation` 容器类名中的 `trans-*` 部分
   - 不得移除 `presentation` 类或 `id="presentation"`
   - 每次只设置一种过渡类型（不叠加多个 `trans-*`）

### Step 6: 修改主题样式

1. 用户指定新的主题要求（如"改成深色模式"、"换蓝色主题"）。

2. **如果用户要求模糊**（如只说了"换个主题"或"换个颜色"），必须追问具体方向：
   ```
   "想换成什么风格？比如：
    · 科技蓝 —— 专业、冷静、适合技术类内容
    · 极简黑 —— 高端、简洁、适合设计感强的内容
    · 温暖橙 —— 亲和、活力、适合分享类内容
    · 商务风 —— 正式、清晰、适合工作汇报
    · 自然绿 —— 清新、健康、适合环保/生活类内容
    · 自定义 —— 你指定主色和背景色
    或者你有其他想法？"
   ```
   如果用户说"自定义"，追问具体的主色色值和背景色偏好。

3. 读取 `index.html`，定位到 `/* ========== THEME ========== */` 注释标记。

4. 修改 `:root` 中的 CSS 变量：
   - `--bg` — 背景色
   - `--text` — 主文字色
   - `--text-secondary` — 次要文字色
   - `--accent` — 强调色
   - `--font-display` — 展示字体
   - `--font-body` — 正文字体
   - 其他变量按需调整

5. **规范要求：**
   - 只修改 `/* ========== THEME ========== */` 区块内的变量值
   - 不删除或重命名 CSS 变量（只改值）
   - 不修改 `/* ========== SLIDE TRANSITIONS ========== */` 或 `/* ========== ELEMENT ANIMATIONS ========== */` 区块
   - 确保修改后所有幻灯片立即可用（变量名统一）

### Step 7: 删除幻灯片

1. 用户指定要删除的幻灯片编号。

2. 读取 `index.html`，定位到 `<!-- SLIDE: slide-N -->` 到 `<!-- /SLIDE -->` 之间的完整区块。

3. 使用 `Edit` 工具**精确匹配**从 `<!-- SLIDE: slide-N -->` 到 `<!-- /SLIDE -->` 的全部内容，替换为空字符串。

4. 删除后**重新编号所有后续幻灯片**：
   - 将 `slide-N` → `slide-(N-1)` (包括 `id`, `data-slide`, `aria-label`, `<!-- SLIDE: -->` 注释)
   - 更新页码显示
   - 更新导航 `nav-dot`

5. 更新进度条宽度（重新计算百分比）。

6. **规范要求：**
   - 删除前向用户确认幻灯片编号和内容
   - 删除后必须重新编号（不允许出现编号空洞）
   - 重新编号时，只改编号数字，不改内容和 class

### Step 8: 预览演示

1. 使用 Bash 执行 Windows 命令在默认浏览器中打开：
   ```
   start "" "./ppt-slide-builder/projects/<project-name>/index.html"
   ```

2. 提醒用户快捷键：左右方向键翻页、Home/End 跳转首尾、空格键下一页。

## Constraints

### 项目管理规则
- **Always** 所有 PPT 项目文件保存在 `./ppt-slide-builder/projects/<project-name>/index.html`
- **Always** 每个项目是一个独立的 HTML 目录（`projects/<project-name>/`），后续项目使用不同目录名
- **Always** 每个 slide 必须包含 `id="slide-N"`、`data-slide="N"`、`role="region"`、`aria-label="Slide N"` 属性
- **Always** 每次操作前先使用 `Read` 读取当前项目文件，确认文件结构和现有 slide 列表
- **Always** 操作完成后使用 `Read` 验证结果，确认 HTML 结构完整

### 幻灯片编号规则
- **Always** slide ID 从 1 开始递增，不允许跳号或重复
- **Always** 添加 slide 时追加到末尾（最大 ID + 1）
- **Always** 删除 slide 后重新编号所有后续 slide
- **Always** 使用 `<!-- SLIDE: slide-N -->` 和 `<!-- /SLIDE -->` 注释标记每个 slide 的精确范围
- **Never** 在删除 slide 时修改其他 slide 的内容（只改编号属性）

### 主题与样式规则
- **Always** 主题统一通过 `:root` CSS 变量控制，不内联样式覆盖主题（除非用户明确要求）
- **Always** 修改主题只改 `/* ========== THEME ========== */` 区块，不动动画和过渡区块
- **Never** 在单个 slide 中硬编码颜色/字体值（始终使用 CSS 变量引用）
- **Never** 为单个 slide 单独设置不同的过渡效果（过渡是全局的）

### 动画规则
- **Always** 动画通过 CSS class 实现，不写内联 `animation` 属性
- **Always** 元素动画类名从 `references/animation-reference.md` 中选择
- **Always** 每个元素最多一个动画类 + 一个延迟类
- **Always** 延迟值从 `anim-delay-1` 到 `anim-delay-10`（对应 0.1s 到 1.0s）
- **Never** 在 `<!-- ========== ELEMENT ANIMATIONS ========== -->` 区块之外定义动画 keyframes

### 操作安全规则
- **Always** 任何删除操作前向用户确认目标和影响范围
- **Always** 使用 `Edit` 工具时通过唯一标记（注释、id）匹配，不用行号
- **Always** 如果修改涉及多个位置（如删除 + 重新编号），按顺序逐个编辑
- **Never** 使用 `Write` 重写整个项目文件（应使用 `Edit` 精确修改）
- **Never** 修改 `ppt-slide-builder/templates/base.html` 模板文件

### 用户提问规则
- **Always** 在 Step 1（初始化项目）中，检查用户是否提供了所有必需信息（标题、风格、页数、内容）。缺失任何一项，必须逐轮提问，一次只问 1-2 个问题
- **Always** 提出的每个问题必须附带具体选项示例（如"科技蓝、极简黑、温暖橙"），帮助用户快速决策，而非只问开放式问题
- **Always** 用户回答模糊时，追问细化（如"科技风格"→追问"深色背景还是浅色背景？"）
- **Always** Step 2（添加幻灯片）中用户未指定内容时，追问新幻灯片的内容和布局
- **Always** Step 6（修改主题）中用户要求模糊时，追问具体风格方向
- **Never** 一次性提问超过 2 个问题（避免信息过载，让用户不知所措）
- **Never** 在用户未给出明确指示时，自行假设并直接生成项目（必须先问清楚再动手）

## Examples

### ✅ Do This — 用户输入模糊时主动提问

**输入**："帮我做个 PPT"

**正确做法 — 逐轮提问收集信息**：

**第 1 轮**（检测到标题和风格都缺失）：
→ "演示的主题/标题是什么？想要什么视觉风格？比如：
   · 科技蓝 —— 深蓝背景 + 亮色文字，适合技术/产品发布
   · 极简黑 —— 黑白配色 + 简洁排版，适合高端/设计感
   · 温暖橙 —— 暖色系 + 亲和力，适合分享/教育
   · 商务风 —— 浅色背景 + 蓝色点缀，适合正式汇报
   或者你有其他想法？"

**用户回答**："做一个产品发布的演示，科技蓝风格"

**第 2 轮**（风格已确定，追问配色细节）：
→ "好的，科技蓝风格。倾向于深色背景还是浅色背景？主色调用亮蓝色还是偏深蓝？"

**用户回答**："深色背景，亮蓝色"

**第 3 轮**（追问页数和内容）：
→ "大概需要几页？我先创建一个框架。比如：
   · 3 页：标题页 + 产品介绍 + 核心优势
   · 5 页：标题页 + 市场分析 + 产品介绍 + 核心优势 + 团队信息
   或者你可以告诉我每页想展示什么内容？"

**用户回答**："先做 3 页：标题、产品介绍、核心优势"

**第 4 轮**（补充细节）：
→ "产品名称是什么？第一页副标题写什么？产品和核心优势的具体要点是什么？"

**收集完所有信息后，生成项目文件**。

### ✅ Do This — 初始化项目

**输入**："创建一个科技风格的 PPT，标题是'2025 年度技术回顾'，蓝色主题"

**操作**：
1. 读取 `templates/base.html` 模板
2. 修改 `:root` 主题变量：
   - `--bg: #0a0e1a`（深色背景）
   - `--text: #e8edf5`（亮色文字）
   - `--accent: #3b82f6`（科技蓝）
   - `--font-display: 'Inter', sans-serif`
   - `--font-body: 'Inter', sans-serif`
3. 替换占位符：标题 → "2025 年度技术回顾"
4. 创建文件 `./ppt-slide-builder/projects/tech-review-2025/index.html`
5. 浏览器中打开

**结果**：一个深蓝色科技风格的双页演示，第一页标题 + 副标题，第二页内容区。

### ✅ Do This — 添加幻灯片

**输入**："在第 2 页后面加一页，展示技术趋势列表"

**操作**：
1. 读取 `projects/tech-review-2025/index.html`
2. 在 `<!-- /SLIDE -->`（slide-2）之后插入新 slide：
   ```html
   <!-- SLIDE: slide-3 -->
   <section id="slide-3" class="slide" data-slide="3" role="region" aria-label="Slide 3" aria-hidden="true">
     <div class="content-left">
       <h2 class="anim-slide-up">2025 技术趋势</h2>
       <ul>
         <li class="anim-slide-up anim-delay-1">AI Agent 全面进入生产环境</li>
         <li class="anim-slide-up anim-delay-2">边缘计算与云原生深度融合</li>
         <li class="anim-slide-up anim-delay-3">量子计算在 NLP 领域的突破</li>
       </ul>
     </div>
   </section>
   <!-- /SLIDE -->
   ```
3. 更新 `#slideTotal` 和进度条

### ✅ Do This — 修改幻灯片过渡

**输入**："把翻页过渡改成缩放效果"

**操作**：
1. 定位 `<div class="presentation" id="presentation">`
2. 使用 Edit：
   ```
   旧: <div class="presentation" id="presentation">
   新: <div class="presentation trans-zoom" id="presentation">
   ```

### ✅ Do This — 删除幻灯片

**输入**："删除第 2 页"

**操作**：
1. 定位 `<!-- SLIDE: slide-2 -->` 到 `<!-- /SLIDE -->`
2. 移除该区块
3. 重新编号：slide-3 → slide-2（只改编号，不改内容）
4. 更新导航和页码

### ❌ Not This

- 不使用 slide 标记注释直接搜索 `<section>` 标签来定位 slide（容易匹配错误）
- 添加 slide 时插入到中间位置（破坏编号连续性）
- 删除 slide 后不重新编号（后续 slide 留下空洞编号）
- 在单个 slide 的 style 属性中写颜色值（覆盖全局主题，导致不一致）
- 同时使用多个 `trans-*` 类（如 `trans-fade trans-zoom` 造成冲突）
- 用 `Write` 重写整个项目文件只为了修改一个动画 class
- 修改模板文件 `ppt-slide-builder/templates/base.html`（影响未来所有新项目）
- 使用内联 `style="animation: ..."` 代替 class
- 用户说"做个PPT"后直接生成默认模板而不问风格/内容（用户拿到的不符合需求）
- 一次性问 5-6 个问题"标题是什么？风格是什么？多少页？每页内容？字体偏好？配色？"让用户不知所措
- 用户只说了"换个主题"就自行改成蓝色（应先问清楚具体方向）
- 用户说"加一页"就直接加空白页（应先问新幻灯片的内容和布局）

## Notes

- 每个演示项目是一个单文件 HTML，可独立复制到任何位置双击打开
- 演示支持键盘（← → ↑ ↓ Home End Space）、鼠标点击导航按钮、触摸滑动三种翻页方式
- 所有动画默认尊重 `prefers-reduced-motion` 系统设置
- 修改主题后所有现有 slide 自动生效（因为使用 CSS 变量）
- 如需更复杂的动画（时间线动画、SVG 路径动画等），需手动编辑 CSS
- 本 skill 不依赖任何外部库或 CDN，生成的文件完全离线可用
- **用户提问始终逐轮推进**：一次只问 1-2 个问题，等回答后再问下一轮。不要一次性抛出一整份需求问卷
- 如果用户已有幻灯片内容（文字、图片列表等），在创建时直接填入，不使用占位文本
- 如果用户需要图片，使用 `<img src="https://via.placeholder.com/800x400" alt="描述">` 作为占位，标注用户需替换
- 每个项目目录名使用 kebab-case（如 `tech-review-2025`、`product-launch`）
- 详细动画和过渡参考见 `references/animation-reference.md`
