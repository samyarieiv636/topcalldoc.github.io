# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 仓库用途

单页双语（中文 / 英文）文档站，介绍老虎机 "CALL"（自动派彩 / 手动派彩）功能。通过 GitHub Pages 部署在 `topcalldoc.github.io`。读者是运营商 / 代理，直接在浏览器中阅读。

## 构建 / 运行 / 测试

本仓库没有构建系统、包管理器，也没有测试套件。整个站点就是一份 `index.html`，由 GitHub Pages 原样提供。本地预览直接用浏览器打开 `index.html` 即可，或在仓库根目录起一个静态服务（例如 `python -m http.server`）。

部署 = 推送到 `main`。GitHub Pages 从仓库根目录读取。

## 架构

全部内容都在 `index.html` 内：CSS 在 `<style>` 块里，正文是内联 HTML，行为在内联 `<script>` 块里。两张 PNG 是文档正文中引用的截图。新增内容请继续保持内联，不要拆成独立的 CSS / JS 文件，除非用户明确要求。

### 双语内容机制

两种机制并存——按要翻译的对象选用：

1. **整块内容**：用成对的 `<div data-lang="zh">` 和 `<div data-lang="en">` 兄弟节点。CSS 根据 `body` 是否带 `lang-en` 类来隐藏另一种语言（默认显示中文；加上 `lang-en` 切换为英文）。新增段落时**两个语言块都要写**——没有兜底渲染。
2. **单一元素上的行内文本**（例如 `<title>`、顶栏 `<h1>`、侧边栏条目）：在同一个元素上写 `data-zh="…" data-en="…"`。`updateTextContent()` 在每次 `switchLang()` 触发时根据当前语言重写 `textContent`。

`switchLang()` 切换 `<body>` 上的 `lang-en` 类并刷新按钮高亮；`updateTextContent()` 重新应用 `data-zh` / `data-en` 文本。两个函数都在 `DOMContentLoaded` 里挂上。

### 导航

侧边栏的 `.nav-link` 锚点指向 `<section class="doc-section">` 上的 `#section-*` id。`initNav()` 在点击时做平滑滚动，并通过滚动监听比较各 section 的 `getBoundingClientRect().top` 与 120px 的顶部偏移量来高亮当前所在条目。新增一个顶级章节时必须做三件事：(a) 加一个 `<section id="section-…" class="doc-section">`；(b) 加一个对应 `href="#section-…"` 的侧边栏 `<a>`，并写好 `data-zh` / `data-en`；(c) 中英两份内容都放进 section 里。

### 图片放大模态框

`initImageModal()` 给页面上**每一个 `<img>`** 都挂上点击事件，打开 `#imageModal`。新内联的图片会自动获得放大功能——无需额外接线，但要注意 CSS `img { cursor: pointer }` 是全局生效的。

### 访问密码（重要提示）

末尾那段 `<script>` 调用 `prompt("请输入访问密码")`，如果输入值不是 `"1234567811"` 就把 `document.body.innerHTML` 替换为 `"Access Denied"`。这是纯前端的软门禁——密码以明文保存在已下发的 HTML 中，不论输入什么，全部正文都在页面源代码里。**不要把它当真正的安全机制**。如果用户要求"加密 / 保护"页面，应当指出这一点，而不是换一个新的硬编码密码。

## 编辑约定

- 任何用户可见的字符串都要保持中英文成对。如果只有一种语言，先确认再提交。
- 章节锚点（`#section-overview`、`#section-risk`、`#section-auto-call`、`#section-manual-call`）被侧边栏引用——重命名 id 时务必同步更新 `<a href>`。
- 配色全部内联，零散使用（`#7c3aed` 主题紫、`#e67e22` 二级标题、`#2980b9` 三级标题、`.note` 黄色框、`.important` 红色字）。优先复用已有的 `.note`、`.example`、`.important`、`.btn-blue`、`.btn-red` 等类，不要随意新增。
