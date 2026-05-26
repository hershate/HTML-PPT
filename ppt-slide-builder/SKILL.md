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
version: 1.0.0
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

### Step 1: 初始化 PPT 项目

在开始任何操作前，先确定用户的操作目标。如果是创建新项目，执行以下步骤：

1. 收集用户需求：
   - **演示标题** — 整个演示的标题
   - **整体风格描述** — 用户想要的视觉风格（如"科技蓝"、"极简黑"、"温暖橙"、"商务风"等）
   - **主题参数**：根据风格确定配色（主色、背景色、文字色）、字体（展示字体、正文字体）

2. 读取模板文件 `ppt-slide-builder/templates/base.html`，用收集的主题参数填充占位符：
   - `{{PRESENTATION_TITLE}}` → 演示标题
   - `{{TITLE}}` / `{{SUBTITLE}}` → 第一页标题/副标题
   - `{{SLIDE2_TITLE}}` / `{{SLIDE2_CONTENT}}` → 第二页内容
   - `{{SLIDE_COUNT}}` → 初始页面数（默认为 2）
   - `{{PROGRESS_PERCENT}}` → 初始进度百分比
   - **主题变量**：修改 `:root` 中的 CSS 变量（`--bg`、`--text`、`--accent`、`--font-display`、`--font-body` 等）

3. 创建项目文件：使用 `Write` 在 `./ppt-slide-builder/projects/<project-name>/index.html` 生成完整的演示文件。

4. 打开浏览器预览：`start "" "./ppt-slide-builder/projects/<project-name>/index.html"`

### Step 2: 添加新幻灯片

1. 读取目标项目的 `index.html`，定位到 `</div>`（`.presentation` 的关闭标签）之前的最后一条 `<!-- /SLIDE -->` 注释。

2. 生成新幻灯片 HTML，包含：
   - 唯一的 `id="slide-N"`（N 为当前最大编号 + 1）
   - 正确的 `data-slide="N"` 属性
   - 递增的 `aria-label="Slide N"` 和 `role="region"`
   - 初始 `class="slide"`（不含 `active`）
   - `aria-hidden="true"`

3. 在最后一个 `<!-- /SLIDE -->` 之后、`</div>` 之前插入新幻灯片。

4. 更新 JavaScript 段落的导航逻辑（仅当导航依赖硬编码的页码时才需要更新）。

5. 确认 `.presentation` 中 `.slide` 元素的总数与导航显示一致。

6. **规范要求：**
   - 新幻灯片必须放在最后（不允许插入到中间，避免破坏编号连续性）
   - 幻灯片 ID 必须连续递增，不允许出现跳号
   - 新幻灯片内容根据用户需求填充，使用占位数据确保可见效果
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

2. 读取 `index.html`，定位到 `/* ========== THEME ========== */` 注释标记。

3. 修改 `:root` 中的 CSS 变量：
   - `--bg` — 背景色
   - `--text` — 主文字色
   - `--text-secondary` — 次要文字色
   - `--accent` — 强调色
   - `--font-display` — 展示字体
   - `--font-body` — 正文字体
   - 其他变量按需调整

4. **规范要求：**
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

## Examples

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

## Notes

- 每个演示项目是一个单文件 HTML，可独立复制到任何位置双击打开
- 演示支持键盘（← → ↑ ↓ Home End Space）、鼠标点击导航按钮、触摸滑动三种翻页方式
- 所有动画默认尊重 `prefers-reduced-motion` 系统设置
- 修改主题后所有现有 slide 自动生效（因为使用 CSS 变量）
- 如需更复杂的动画（时间线动画、SVG 路径动画等），需手动编辑 CSS
- 本 skill 不依赖任何外部库或 CDN，生成的文件完全离线可用
- 如果用户已有幻灯片内容（文字、图片列表等），在创建时直接填入，不使用占位文本
- 如果用户需要图片，使用 `<img src="https://via.placeholder.com/800x400" alt="描述">` 作为占位，标注用户需替换
- 每个项目目录名使用 kebab-case（如 `tech-review-2025`、`product-launch`）
- 详细动画和过渡参考见 `references/animation-reference.md`
