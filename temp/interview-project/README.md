---
id: interview-project

title: Interview Project
---

# 地图优化

## 通用优化措施

- 避免采用 DOM 的方式渲染点位。地图上的点位数量较多（200+），采用 DOM 的方式渲染点位会严重影响地图交互性能。应该使用地图引擎提供的矢量图层直接在 Canvas 上渲染展示点位。
- 动态 SVG。避免使用字体图标方案，因为字体图标会引入额外的字体文件加载与解析的流程。点位样式统一采用动态生成 SVG DataURI 的方式，前端根据图标匹配规则获取对应的 SVG 模板资源，根据点位样式拼接 SVG 字符串，对 SVG 字符串进行编码，最终进行 Base64 编码并生成 DataURI（`data:image/svg+xml;base64,`）。
- 函数级缓存。对于 SVG 模板资源获取、拼接、编码等过程，通过函数级缓存，避免重复计算。
- 共享样式。地图上每个点位都要创建一个样式对象，当地图上点位数量较多时，会导致内存占用过高，同时也会影响渲染性能。建立一个样式池，通过对点位属性 Hash 的方式，使有相同 Hash 值的点位共享一个样式对象。
- 点位事件绑定优化。地图引擎只提供了单个点位点击事件的监听方法。实际应用中，当点位数量较多时，每个点位都绑定点击事件会导致内存占用过高；并且点位移除以后，事件监听回调函数持有的闭包可能还包含了对已经移除的点位对象的引用，导致内存泄漏。统一封装针对图层的点击事件监听，通过类似事件委托的方式，减少事件监听函数的数量，避免内存泄漏。
- 地图引擎相关对象被 Vue 转换为响应式对象，由于地图引擎对象属性较多，并且如图层对象可能包含大量点位数据，转换为响应式对象会导致不必要的依赖收集和派发更新过程，影响性能。提供统一的封装，在创建地图相关对象时为对象设置 `_isVue`（Vue 2.6）或 `__v_skip`（Vue 2.7）属性，避免被转换为响应式对象。

## 后端聚合展示方案优化

现状：

- 后端对数据进行预处理，划分 20 个层级，每个层级代表一个分辨率。地图在每个层级划分为相同大小的网格，计算点位在每个层级所处的行列号，扩展点位字段存储行列号信息，通过 es 进行聚合查询。
- 系统内建点位采用后端聚合的方式。地图范围变化时，会去请求该范围内的点位数据并渲染到地图上。
- 在省级项目中，需要支持数据量在 10W 级别的点位展示，即便做了后端聚合，在点位较多的层级下，也会出现卡顿。

优化：

- 在每次渲染时对点位进行一次 Diff，移除已经不在当前范围内的点位，只添加新增的点位。
- 采用节流的方式避免频繁请求数据。
- 通过合理的聚合层级配置，尽量控制屏幕范围内点位展示数量不超过 800（200ms）。
- 通过 `requestIdleCallback` 对数据分批进行处理并添加到图层中，避免长时间占用主线程导致交互卡顿。

# 前端点位聚合

# 点位可视域渲染

# 前端换肤方案

# 跨图轨迹

问题：

- 需求：展示目标在多个平面地图之间的移动轨迹。
- 需要同时展示多个平面地图。这种场景下由于同时存在的地图对象较多，导致内存占用过高，同时也会影响渲染性能。

解决：

- 通过计算平面图内轨迹点范围，获取最佳展示层级及范围，换算需要展示的平面图瓦片，避免创建额外的地图对象。

# GPS 上报

- 项目现场反馈在地图上展示 GPS 点位时，页面卡顿严重。
- 通过 WebSocket 接收 GPS 点位上报消息。GPS 点位多，上报频率高，每次上报以后触发地图点位更新动画，导致卡顿。
- 缓存及批量处理。将一段时间内的上报消息缓存，定时批量处理。

# 电子地图应用大模型版本

# Himap 地图组件库

# 远程组件

问题：

- 业务组件库发布为 NPM 包，应用通过 NPM 安装业务组件库。
- 在项目上，如果某个业务组件需要定制，所有依赖于该组件的应用都需要走定制流程、更新依赖、重新构建和部署，导致复杂的定制流程和高昂的定制费用。

解决：

- 实现一套远程组件加载方案。
- 业务组件库构建时，组件主体代码构建为独立的 UMD 模块，并生成一个代理组件作为 NPM 包的 ESM 入口。应用本地安装业务组件 NPM 包，引入的组件实际上是空壳的代理组件。当代理组件渲染时，会首先渲染后备内容，并通过模块加载器加载远程组件，加载完成后将后备内容替换为业务组件。
- 代理组件的作用：降低应用的改造成本；在远程组件加载的过程中，提供后备内容；提供 typescript 类型。
- 模块加载器。
- 按需加载，减少应用构建体积，优化应用首屏加载性能。
- 支持资源预获取，通过 `prefetch` 提前下载远程组件资源，减少首次加载时的延迟。
- 远程组件通过一个远程组件服务独立部署，业务组件更新以后，所有依赖该业务组件的应用不需要重新构建和部署。并且远程组件服务可以根据请求参数中的应用上下文，让不同的应用加载不同版本的远程组件。
- 采用这套方案可以显著减少定制流程和工作量，减少项目定制费用。

# 组件库测试

# 自动构建与发布