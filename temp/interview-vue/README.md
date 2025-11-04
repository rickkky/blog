---
id: interview-vue

title: Interview Vue
---

# Vue3 与 Vue2 的区别

- 响应式系统。Vue3 使用 `Proxy` 代替 `Object.defineProperty` 实现响应式，提供更好的性能和更多的特性。支持对数组/对象深层属性的监听，性能更高且无需 `Vue.set`/`Vue.delete` 手动处理。
- Composition API。Vue3 提供了 Composition API，逻辑按功能聚合，使得组件逻辑更加清晰，复用性更高。在单文件组件中支持 `<script setup>` 语法糖。
- 编译优化。通过静态提升、更新类型标记和树结构打平等优化，
- 生命周期。Vue3 中 `beforeDestroy` 和 `destroyed` 生命周期被重命名为 `beforeUnmount` 和 `unmounted`。新增了两个调试相关的生命周期 `renderTracked` 和 `renderTriggered`。在 Composition API 中，没有 `beforeCreate` 和 `created` 生命周期，可以使用 `setup` 替代，其他生命周期钩子以 `on` 开头。
- Fragment。Vue3 的 `template` 模板支持多个根节点。
- Teleport。`<Teleport>` 是一个内置组件，它可以将一个组件内部的一部分模板“传送”到该组件的 DOM 结构外层的位置去。
- Suspense。`<Suspense>` 是一个内置组件，用来在组件树中协调对异步依赖的处理。它让我们可以在组件树上层等待下层的多个嵌套异步依赖项解析完成，并可以在等待时渲染一个加载状态。
- TypeScript 支持。Vue3 使用 TypeScript 重写，提供了更好的类型支持。
- 双向绑定。Vue3 支持多个 `v-model` 属性，默认使用 `modelValue` 和 `update:modelValue` 作为双向绑定的属性和事件。
- `v-if` 和 `v-for`。Vue2 中 `v-if` 优先级高于 `v-for`，Vue3 中 `v-for` 优先级高于 `v-if`。
- API 设计。Vue2 中提供了很多全局 API，Vue3 中将这类 API 挂载在 `createApp` 创建的应用实例上。

# 响应式原理

## Vue2 响应式原理

### observe

初始化过程中，会调用 `observe` 方法将 `data` 和 `props` 转换为响应式对象。

`observe` 函数的主要工作流程如下：

- 如果对象存在 `__ob__` 属性且是 `Observer` 实例，说明该对象已经是响应式对象，返回其 `__ob__` 属性。
- 否则，在满足一定条件的情况下，创建一个 `Observer` 实例并返回。

可以通过给对象添加 `__v_skip: true` 属性来避免对象被转换为响应式对象。

### Observer

`Observer` 的 `constructor` 主要进行了以下操作：

- 创建一个 `Dep` 实例作为当前 `observer` 的 `dep` 属性的值。
- 给对象添加 `__ob__` 属性，值为当前 `observer`。
- 判断对象是否为数组，如果是数组，调用 `observeArray` 方法，将数组中的每一项转换为响应式对象，并修改数组的原型链使得可能修改原数组的方法也能触发响应式更新。
- 否则遍历对象的属性，对每个属性调用 `defineReactive` 方法。

### defineReactive

`defineReactive` 将对象的属性转换为响应式属性。其主要工作原理如下：

- 创建一个 `Dep` 实例，以闭包的形式供属性的 `getter` 和 `setter` 方法使用。
- 获取属性的值，如果属性的值是对象，调用 `observe` 方法将其转换为响应式对象（`childOb`）。
- 通过 `Object.defineProperty` 为属性添加 `getter` 和 `setter`。

### 响应式对象属性 getter

响应式对象属性的 `getter` 方法在返回当前对象属性值的同时，会判断当前是否存在 `Dep.target`，如果存在，则进行依赖收集阶段。

- 调用 `dep.depend` 方法。
- 如果 `childOb` 存在，调用 `childOb.dep.depend` 方法。
- 如果属性值是数组，调用 `dependArray` 方法。该方法会遍历数组中的每一项元素，如果数组中的元素是是响应式对象，会调用该对象的 `__ob__` 属性的 `dep.depend` 方法。
- 返回属性值。

### 响应式对象属性 setter

响应式对象属性的 `setter` 方法在设置对象属性值的同时，会通知依赖于当前 `dep` 的 `DepTarget` 进行更新，进行派发更新阶段。

- 设置属性值。
- 更新 `childOb`。
- 调用 `dep.notify` 方法。

### Dep

`Dep` 类表示一个“依赖”，它的实例是一个可观察对象以供订阅。

在 `Dep` 类中定义了一个静态属性 `target`，表示当前全局正在处理的“依赖目标”，其类型为 `DepTarget`。模块中维护了一个 `targetStack` 栈，可以通过 `pushTarget` 和 `popTarget` 函数来操作，而 `Dep.target` 的值就是栈顶元素。

`Dep` 类包含以下属性或方法：

- `subs` 表示依赖于当前 `dep` 的 `DepTarget` 列表。
- `depend` 方法会判断当前是否存在 `Dep.target`，则执行 `Dep.target.addDep(this)` 将当前 `dep` 添加到 `Dep.target` 的依赖列表中。
- `notify` 方法遍历 `subs` 并调用 `DepthTarget` 的 `update` 方法。

### DepTarget

`DepTarget` 接口表示一个“依赖目标”，包含以下属性或方法：

- `id`：表示一个数字标识，在进行派发更新的时候用于进行排序。
- `addDep`：向当前目标添加依赖。
- `update`：更新当前目标。

### Watcher

`Watcher` 类实现了 `DepTarget` 接口，表示一个可以收集依赖的目标。

`watcher` 主要的属性包括：

- `vm`：与当前 `watcher` 相关联的 `component`。当构造函数参数中的 `isRenderWatcher` 为 `true` 时，会将 `vm._watcher` 设置为当前 `watcher`。
- `getter`：一个函数，用于获取 `watcher` 的值。当构造函数参数中的 `expOrFn` 为函数时，会将其赋值给 `getter`；当 `expOrFn` 为字符串时，会解析字符串表示的访问路径，生成一个函数赋值给 `getter`。
- `deps`、`newDeps`、`depIds`、`newDepIds`：用于存储依赖的 `dep` 和 `id` 集合。

### Watcher.prototype.get

在 `watcher` 构造的最后，会调用 `get` 方法初始化 `watcher` 的 `value` 属性。`get` 方法的主要作用是调用 `getter` 函数获取值，并重新进行依赖收集，其主要工作流程如下：

- 调用 `dep` 模块中的 `pushTarget` 函数将当前 `watcher` 设置为 `Dep.target`。
- 将 `vm` 作为上下文和参数调用 `getter` 函数，获取其返回值赋值给 `value` 属性。
- 如果 `deep` 属性值为 `true`，则调用 `traverse` 函数，遍历访问 `value` 所有的属性，在遍历过程中会收集所有深层属性的依赖。
- 调用 `dep` 模块中的 `popTarget` 函数将当前 `watcher` 出栈。
- 调用 `this.cleanupDeps` 方法清理依赖。

### Watcher 依赖管理

`Watcher` 的 `addDep` 方法主要工作流程如下：

- 将参数 `dep` 添加到新的依赖集合中。
- 如果 `dep` 不在原本的依赖集合中，调用 `dep.addSub(this)`，令当前 `watcher` 例订阅 `dep` 的通知。

`Watcher` 的 `cleanupDeps` 方法主要工作流程如下：

- 遍历当前依赖集合，如果某个依赖不在新的依赖集合中，调用 `dep.removeSub(this)`，取消当前 `watcher` 对该依赖的订阅。
- 设置新的依赖集合为当前依赖集合，并重置新的依赖集合。

`cleanupDeps` 通过判断新旧依赖集合的差异，保证在组件条件渲染等情况下，当依赖目标前后依赖的数据发生变化时，旧的依赖变化不会触发不必要的更新。

### 依赖收集过程

以渲染组件为例：

- 组件 `mount` 过程中，会创建一个与组件关联的渲染 `watcher`，其 `getter` 为一个 `updateComponent` 函数。
- 创建 `watcher` 之后其 `getter` 被调用，当前实例被设置为 `Dep.target`，并执行 `updateComponent` 函数。
- `updateComponent` 函数执行 `vm._update(vm._render())`。
- `vm._render()` 会生成渲染 `VNode`，在这个过程中会对 `vm` 的 `data` 或 `props` 数据属性进行访问，即触发响应式对象属性的 `getter` 方法。
- 响应式对象属性的 `getter` 方法内会调用 `dep.depend`，即会执行 `Dep.target.addDep(this)`，将当前 `dep` 添加到 `Dep.target` 的依赖列表中。
- `updateComponent` 函数执行完毕以后当前 `Dep.target` 出栈，并调用 `cleanupDeps` 方法清理依赖。

### Watcher 更新过程

`Watcher` 的 `update` 方法主要工作流程如下：

- 如果 `lazy` 属性为 `true`，则将 `dirty` 属性设置为 `true`。
- 否则，如果 `sync` 属性为 `true`，则调用 `run` 方法。
- 否则，调用 `queueWatcher` 函数将当前 `watcher` 添加到更新队列中，等待合适的时机执行 `watcher` 的 `run` 方法以及相关回调。

`Watcher` 的 `run` 方法主要工作流程如下：

- 判断当前 `watcher` 是否处于激活状态，如果不是则直接退出。
- 调用 `get` 方法获取新的值。
- 判断是否需要执行 `cb` 回调。（判断条件：前后值不相等、值为对象、深度监听）。

### queueWatcher

在 `scheduler` 模块中维护了一个待执行的 `watcher` 队列以及一系列状态，会在下一个 `tick` 中统一执行。

`queueWatcher` 的主要工作流程如下：

- 如果更新队列中已经包含了当前 `watcher`，则直接退出。
- 如果当前 `watcher` 是正在进行依赖收集的 `Dep.target`，或当前 `watcher` 的 `noRecurse` 属性为 `true`，则直接退出。
- 如果 `flushing` 为 `false`，则将当前 `watcher` 添加到更新队列中。
- 否则根据 `id` 的大小将当前 `watcher` 插入到更新队列中。
- 之后判断是否进入 `waiting` 状态。如果没有，则将 `waiting` 置为 `true`，并调用 `nextTick(flushSchedulerQueue)`。

### flushSchedulerQueue

`flushSchedulerQueue` 主要工作流程如下：

- 设置状态，将 `flushing` 置为 `true`，表示当前正在执行更新队列中的 `watcher`。
- 对更新队列中的 `watcher` 进行排序。
- 遍历更新队列。若 `watcher` 存在 `before` 钩子函数，则先执行 `before` 钩子函数。之后调用 `watcher` 的 `run` 方法。
- 在开发环境下，会计算当前更新轮次中 `watcher` 执行的次数来检测是否存在死循环更新的情况。
- 所有 `watcher` 执行完成以后，执行 `keep-alive` 组件和 `updated` 声明周期相关的逻辑，并重置状态。

在更新之前先进行队列排序（根据 `id` 和 `post` 属性），是为了保证：

- 父组件先于子组件更新。
- 用户自定义的 `watcher` 先于渲染 `watcher` 执行。
- 如果一个组件在其父组件 `watcher` 执行期间被销毁，那么可以跳过执行该组件的 `watcher`。

### 派发更新过程

以组件渲染为例：

- 组件渲染依赖的数据发生变化时，会触发响应式对象属性的 `setter` 方法。
- 响应式对象属性的 `setter` 方法内会调用 `dep.notify` 方法，通知依赖于当前 `dep` 的 `watcher` 执行更新。
- `dep.notify` 会遍历 `subs` 列表，调用 `watcher.update` 方法。
- `watcher.update` 将当前 `watcher` 添加到更新队列中，等待下一个 `tick` 执行。
- 在下一个 `tick` 中，会执行 `flushSchedulerQueue` 函数，执行更新队列中所有的 `watcher`。
- `watcher.run` 会调用 `watcher.get` 方法获取新的值，对于组件渲染，即调用 `updateComponent` 方法重新渲染并更新组件。

### nextTick

`nextTick` 会依次尝试使用 `Promise.then`、`MutationObserver`、`setImmediate` 和 `setTimeout(..., 0)` 来实现异步执行。

### 检测变化的注意事项

无法检测变化的情况：

- 对象属性的添加或删除。
- 通过索引设置数组项或修改数组长度。

除了以上情况外，Vue 不允许动态添加根级响应式属性，必须在初始化时声明。

### 计算属性原理

- 创建 `computed` 属性对应的 `watcher` 时，会将 `lazy` 属性设置为 `true`，表示只在访问该属性时才会计算值。
- 当计算属性的依赖更新时，其 `watcher` 不会被添加到更新队列中，而是标记 `dirty` 属性为 `true`，表示计算属性的值需要重新计算。
- 当访问计算属性时，会判断其 `dirty` 属性，如果为 `true`，则调用 `watcher.evaluate` 方法计算新的值。该方法会调用 `watcher.get` 方法并重置 `dirty` 属性。
- 收集依赖时，会调用 `watcher.depend` 方法。该方法会遍历计算属性依赖的所有 `dep`，分别调用 `dep.depend` 方法。

## Vue3 响应式原理

使用 `Object.defineProperty` 实现响应式的缺点：

- 无法监听数组基于索引的修改。对于会原地修改数组的方法，需要通过覆盖原型链的方式实现。
- 无法监听对象属性的添加和删除。
- 不支持对 Map、Set、WeakMap 和 WeakSet 等数据结构的监听。
- 需要遍历对象所有属性，对每个属性分别设置 `getter` 和 `setter`。

Vue3 使用 `Proxy` 实现响应式。`Proxy` 对象表示一个目标对象的代理，从而实现对目标对象操作的拦截（如访问属性、赋值、枚举、函数调用等）。

# Diff 算法

## Vue2 Diff 过程

- 生成新旧虚拟 DOM 树。重新渲染组件时，会生成新的虚拟 DOM 树，与上一次渲染的虚拟 DOM 树进行比较。
- 深度优先遍历。通过深度优先遍历（前序遍历），采用同层比较的方式，不考虑节点跨层级移动的情况，将两颗树 Diff 的时间复杂度由 $O(n^3)$ 降低到 $O(n)$。
- Diff 过程。在遍历的过程中，比对新旧节点的类型和属性，判断是否需要更新实际 DOM。
  - 通过双指针从头尾开始对节点进行比较，两个指针向中间逼近，目的是尽量保证不发生 DOM 移动。
  - 若旧树指针重合，新树指针未重合，则说明新树未遍历的都为新增节点。
  - 若新树指针重合，旧树指针未重合，则说明旧树未遍历的都为删除节点。
  - 若新旧树指针都未重合：
    - 则首先遍历旧树，将剩余节点存在一个 `key-index` 映射表中。
    - 然后遍历新树，创建一个数组，记录节点在旧树中的位置。若节点在旧树中不存在，则标记为新增节点（`index` 最小）；遍历结束以后，旧树中未遍历的节点即为删除节点。
    - 根据遍历后的数组，对旧树中的节点进行移动。
    - 优化：找到遍历后的数组中的最长递增子序列，保持子序列中的节点不变，移动其余节点。
- 具体的对比逻辑：
  - 如果新旧节点相同（类型相同且 key 相同），则复用该节点，只需要更新属性。
  - 如果新旧节点不同，则替换整个节点及其子节点。
  - 如果节点存在子节点，则递归地对子节点进行 Diff。
  - 如果节点在旧虚拟 DOM 树中存在但在新虚拟 DOM 树中不存在，则删除该节点。
- 更新实际 DOM。在 Diff 过程中，会记录需要更新的节点，最后统一对需要更新的部分进行批量更新。

## Vue3 Diff 的优化

- 编译优化。Vue3 编译器通过带编译时信息的虚拟 DOM 方案，使得编译器可以静态分析模板并在生成的代码中留下可用于运行时优化的标记，同时保留了边界情况时用户想要使用底层渲染函数的能力。
  - 静态提升。在编译阶段，Vue2 通过标记静态根节点优化 Diff 过程；Vue3 则标记和提升所有的静态节点，Diff 过程中只需要对比动态节点。未参与更新的元素（如静态模板片段）会被提升，只在初始化时创建一次，后续复用，减少重复渲染开销。
  - 更新类型标记。在编译阶段为含有动态绑定的元素生成渲染函数时，在 vnode 创建调用中直接编码了该元素所需的更新类型。一个元素可以有多个更新类型标记，会被合并成一个数字。运行时渲染器会使用位运算来检查这些标记，确定相应的更新操作，在更新带有动态绑定的元素时做最少的操作。
  - 树结构打平。模板中内部结构稳定（没有 `v-if` 或 `v-for`）的部分称为一个“区块”，每一个块都会追踪其所有带更新类型标记的后代节点 (不只是直接子节点)。编译的结果会被打平为一个数组，仅包含所有动态的后代节点。当这个组件需要重渲染时，只需要遍历这个打平的树而非整棵树。这大大减少了更新时需要遍历的节点数量。一个子区块会在父区块的动态子节点数组中被追踪，这为他们的父区块保留了一个稳定的结构。
- 最长递增子序列算法优化 Diff 过程中的节点移动操作。

# Vue 优化

- 避免将很大的对象转换为响应式对象。
- 避免节点跨层级移动。

# Vue Router

## Hash 模式

- Hash 模式基于 `location.hash` 实现。`location.hash` 表示 URL 中的 `#` 号及其后面的 URL 片段标识符。
- 改变 `location.hash` 不会导致页面刷新，可以通过 `hashchange` 事件监听 `location.hash` 的变化。

## History 模式

- History 模式基于 HTML5 History API 中的 `history.pushState` 和 `history.replaceState` 实现。这两个方法可以在不刷新页面的情况下操作浏览器历史记录。
- 浏览器前进、后退时会触发 `popstate` 事件。监听 `popstate` 事件，在回调中获取当前访问路径，根据路径渲染对应的组件。
- `history.pushState` 和 `history.replaceState` 不会触发 `popstate` 事件。
- History 模式需要服务器端配置支持，以避免刷新页面时出现 404 错误。
