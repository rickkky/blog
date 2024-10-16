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

## 渲染方案

- SPA（Single Page Application）
- SSR（Server Side Rendering）
- SSG（Static Site Generation）

## Tree-Shaking

- 采用构建步骤。
  - 当使用了构建步骤时，模板会被预编译，因此我们无须在浏览器中载入 Vue 编译器。这在同样最小化加上 gzip 优化下会相对缩小 14kb 并避免运行时的编译开销。
  - 如果使用的是相对现代的打包工具，许多 Vue 的 API 都是可以被 tree-shake 的。
- 尽量选择提供 ES 模块格式的依赖，对于 Tree-Shaking 更友好。例如，选择 `lodash-es` 比 `lodash` 更好。

## 代码分割

代码分割是指构建工具将构建后的 JavaScript 包拆分为多个较小的，可以按需或并行加载的文件。通过适当的代码分割，页面加载时需要的功能可以立即下载，而额外的文件只在需要时才加载，从而提高性能。
