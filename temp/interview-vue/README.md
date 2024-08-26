---
id: interview-vue

title: Interview Vue
---

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
- 判断对象是否为数组，如果是数组，调用 `observeArray` 方法，将数组中的每一项转换为响应式对象。
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
