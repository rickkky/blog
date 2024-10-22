---
id: interview-performance

title: Interview Performance
---

# 页面加载性能

- [web.dev](https://web.dev/)

指标：

- 最大内容绘制（Largest Contentful Paint，LCP）
- 首次输入延迟（First Input Delay，FID）

性能分析：

- Chrome DevTools Performance 面板
- Vue 浏览器插件

## 关键渲染路径

- DOM。通过 HTML 构建文档对象模型。
- CSSOM。通过 CSS 构建 CSS 对象模型。
- JavaScript。执行 JavaScript 代码（可能会修改 DOM 和 CSSOM）。
- 渲染树。通过 DOM 和 CSSOM 构建渲染树。
- 布局。计算渲染树中每个元素的尺寸和位置。
- 绘制。在内存中分层绘制元素的像素。
- 合成。将绘制的多个图层合成最终的屏幕图像。合成步骤可以利用 GPU 进行加速。

## 关键渲染路径上的资源

浏览器需要等待一些关键资源下载完成，然后才能完成初始渲染。包括：

- HTML 的一部分。
- `<head>` 中阻塞渲染的样式表。
- `<head>` 中阻塞渲染的脚本。

浏览器以流式方式处理 HTML，一旦获取网页 HTML 的任何部分，就会开始对其进行处理。然后浏览器可以先呈现网页，然后再处理其余部分的 HTML。

- 同步脚本会阻塞 DOM 构建过程。
- 样式表不会阻塞 DOM 构建，但会阻塞渲染树的构建。
- 脚本会等待样式表加载和解析完成后执行。

在初始渲染时，浏览器通常不会等待：

- 整个 HTML。
- 字体。
- 图片。
- `<head>` 元素外的非阻塞渲染的脚本。例如，位于 `<body>` 末尾的 `<script>` 元素。
- `<head>` 元素外或 `media` 属性不适用于当前视口的样式表，不会阻塞渲染。

优化关键渲染路径的方法：

- 减少关键资源的数量和大小。减少 HTML、CSS 和 JavaScript 文件的大小，可以减少下载时间；减少关键资源的数量，可以减少解析和构建的时间。
- 减少关键资源的加载时间。使用合适的资源压缩和合并技术，减少关键资源的加载时间。
- 优化 CSS 和 JavaScript 的加载和执行顺序。将 CSS 文件放在页面头部，JavaScript 文件放在页面底部，避免阻塞页面渲染。
- 延迟加载非关键资源。将非关键的 CSS 和 JavaScript 文件延迟加载，让浏览器先渲染可见内容。
- 使用浏览器缓存。合理利用浏览器缓存，减少资源的重复下载。
- 减少重排和重绘。避免频繁的 DOM 操作和样式修改，减少页面的重排和重绘。

## 性能指标

### Window: load 事件

- `load` 事件在整个页面及所有依赖资源（如样式表、图片）都已完成加载时触发。

### Document: DOMContentLoaded 事件

- `DOMContentLoaded` 事件会在 HTML 文档完全解析，并且所有同步脚本和延迟脚本（`<script defer>` 或 `<script type="module">`）下载和执行完毕后触发。它不会等待图片、`<iframe>`、异步脚本（`<script async>`）等其他内容完成加载。
- `DOMContentLoaded` 事件不会等待样式表加载，但由于脚本会等待样式表，因此在有脚本的情况下该事件也会在样式表加载解析完成后触发。

### First Paint

- FP 表示首次渲染时间。

## 性能优化

### 渲染方案

- SPA（Single Page Application）
- SSR（Server Side Rendering）
- SSG（Static Site Generation）

### Tree-Shaking

- 采用构建步骤。
  - 当使用了构建步骤时，模板会被预编译，因此我们无须在浏览器中载入 Vue 编译器。这在同样最小化加上 gzip 优化下会相对缩小 14kb 并避免运行时的编译开销。
  - 如果使用的是相对现代的打包工具，许多 Vue 的 API 都是可以被 tree-shake 的。
- 尽量选择提供 ES 模块格式的依赖，对于 Tree-Shaking 更友好。例如，选择 `lodash-es` 比 `lodash` 更好。

### 代码分割

代码分割是指构建工具将构建后的 JavaScript 包拆分为多个较小的，可以按需或并行加载的文件。通过适当的代码分割，页面加载时需要的功能可以立即下载，而额外的文件只在需要时才加载，从而提高性能。

### 缓存

### 文件压缩
