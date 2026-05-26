# HTML-PPT

> **PPT 风格全屏网页演示文稿生成工具集** — 将演示文稿制作带回到 HTML 的纯粹与灵活。

---

## 项目简介

HTML-PPT 是一个专注于创建 PPT 风格全屏网页演示文稿的项目。它不依赖任何第三方框架或构建工具，生成的是纯静态 HTML 文件，双击即可在浏览器中全屏播放，支持键盘方向键、鼠标滚轮、点击翻页、触摸滑动等多种导航方式。

> ⚠️ **早期实验阶段声明**
>
> 本项目目前处于早期实验阶段，代码和功能可能存在各种各样的 BUG 与不完善之处。如果你在使用过程中遇到任何问题，欢迎提交 Issue 反馈。作者会在时间允许的情况下尽力修复和改进（虽然作者平时比较忙，不一定能及时响应，但每一条反馈都会被看到）。感谢你的理解与包容！

项目包含两个核心部分：

| 模块 | 说明 |
|------|------|
| [ppt-slide-builder](ppt-slide-builder/) | 面向 Claude Code 的 AI Skill，用于**交互式创建和管理** HTML 演示文稿 |
| [class-score-analysis](class-score-analysis/) | 使用 ppt-slide-builder 生成的**示例项目** — 班级成绩分析报告 |

---

## ppt-slide-builder

> 一个完整的 Claude Code Skill，用于通过自然语言对话创建、管理和维护 PPT 风格的 HTML 演示文稿。

### 核心特性

- **🎨 统一样式管理** — 通过 CSS 变量在 `:root` 中统一控制配色、字体、间距，修改主题全局自动生效
- **📄 多页面管理** — 创建、添加、修改、删除幻灯片，严格维护编号连续性
- **✨ 元素入场动画** — 内置 12 种动画类型（淡入、滑入、缩放、模糊、翻转、弹跳等），支持错落延迟
- **🔄 页面过渡效果** — 7 种翻页过渡（淡入淡出、滑动、缩放、3D 翻转等）
- **🖼️ 图片支持** — 本地图片自动复制到项目目录，网络图床链接自动验证可用性
- **⚡ 纯静态输出** — 单文件 HTML，无外部依赖，完全离线可用
- **♿ 可访问性** — 所有动画尊重 `prefers-reduced-motion` 系统设置

### 安装方式

将 `ppt-slide-builder/` 目录复制到 Claude Code 项目的 `.claude/skills/` 目录下，重启 Claude Code 即可使用。

### 使用方式

通过斜杠命令或自然语言触发：

```
/ppt-slide-builder 创建一个科技主题的 PPT，标题为"2025 年度技术回顾"
/ppt-slide-builder 给第 1 页的标题添加淡入动画
/ppt-slide-builder 把翻页过渡改成缩放效果
```

详细文档请参见 [ppt-slide-builder/README.md](ppt-slide-builder/README.md) 和 [ppt-slide-builder/SKILL.md](ppt-slide-builder/SKILL.md)。

---

## class-score-analysis

使用 ppt-slide-builder 生成的示例演示项目，展示了一份完整的班级成绩分析报告，包含：

- 班级核心指标（平均分、最高分、优秀率、及格率）
- 各科平均分对比（柱状图）
- 各科优秀率排行
- 分数段分布
- 进退步概况
- 重点关注学生
- 改进建议

该文件可直接在浏览器中打开：[class-score-analysis/index.html](class-score-analysis/index.html)

---

## 项目结构

```
HTML-PPT/
├── LICENSE                         # Apache 2.0 开源许可证
├── .gitattributes                  # Git 属性配置
├── README.md                       # 本文件
│
├── ppt-slide-builder/              # AI Skill：PPT 网页演示文稿生成器
│   ├── SKILL.md                    # Skill 主文件（含完整工作流与约束）
│   ├── README.md                   # ppt-slide-builder 使用文档
│   ├── templates/
│   │   └── base.html               # 演示文稿基础模板
│   └── references/
│       └── animation-reference.md  # 动画与过渡效果完整参考
│
└── class-score-analysis/           # 示例：班级成绩分析报告
    └── index.html                  # 生成的演示文件
```

---

## 技术架构

### 主题系统

通过 CSS 自定义属性实现全局主题控制：

```css
:root {
  --bg: #ffffff;                /* 背景色 */
  --text: #1a1a1a;              /* 主文字色 */
  --accent: #3b82f6;            /* 强调色 */
  --font-display: 'Georgia', serif;  /* 展示字体 */
  --font-body: system-ui, sans-serif; /* 正文字体 */
}
```

### 导航方式

生成的演示文稿支持五种翻页方式：

| 方式 | 操作 |
|------|------|
| 键盘 | `←` / `↑` 上一页 · `→` / `↓` / `Space` 下一页 · `Home` / `End` 首尾页 |
| 鼠标滚轮 | 向下滚下一页，向上滚上一页 |
| 点击空白 | 点击幻灯片非交互区域翻到下一页 |
| 触摸滑动 | 移动端左右滑动翻页 |
| URL Hash | `index.html#slide-3` 直接定位到指定页 |

---

## 开源许可

本项目基于 **Apache License, Version 2.0** 开源。

```
Copyright 2025 Zurker

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```

完整的许可证文本请参见项目根目录下的 [LICENSE](LICENSE) 文件。

### Apache 2.0 要点说明

- ✅ **商用自由** — 你可以将本项目用于任何商业用途
- ✅ **修改与分发** — 你可以修改代码并重新分发，但需保留原始版权声明和许可证副本
- ✅ **专利授权** — 贡献者自动授予专利许可
- ✅ **免责声明** — 代码按"原样"提供，无任何明示或暗示的保证
- ⚠️ **修改说明** — 修改过的文件需标注变更
- ⚠️ **保留通知** — 不得删除或修改源代码中的版权、专利、商标和归属通知

---

*由 [ppt-slide-builder](ppt-slide-builder/SKILL.md) 驱动 · 基于纯静态 HTML 构建*
