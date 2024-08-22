---
id: interview-vue

title: Interview Vue
---

# 响应式

## Vue2 响应式原理

### 响应式对象

初始化过程中，会调用 `observe` 方法将 `data` 和 `props` 转换为响应式对象。

### observe

`observe` 方法返回一个 `Observer` 实例。

- 如果对象存在 `__ob__` 属性且是 `Observer` 实例，说明该对象已经是响应式对象，返回其 `__ob__` 属性。
- 否则，在满足一定条件的情况下，创建一个 `Observer` 实例。

可以通过给对象添加 `__v_skip: true` 属性来避免对象被转换为响应式对象。

### Observer

`Observer` 的构造函数主要进行了以下操作：

- 创建一个 `Dep` 实例。
- 给对象添加 `__ob__` 属性，值为当前 `Observer` 实例。
- 判断对象是否为数组，如果是数组，调用 `observeArray` 方法，将数组中的每一项转换为响应式对象。
- 否则遍历对象的属性，对每个属性调用 `defineReactive` 方法。

### defineReactive

`defineReactive` 将对象的属性转换为响应式属性。其主要工作原理如下：

- 创建一个 `Dep` 实例。
- 获取属性的值，如果属性的值是对象，调用 `observe` 方法将其转换为响应式对象，并获取子 `Observer` 实例 `childOb`。
- 通过 `Object.defineProperty` 为属性添加 `getter` 和 `setter`。

### getter

对象属性的 `getter` 方法在返回当前对象属性值的同时，会判断当前是否存在 `Dep.target`，如果存在，则进行依赖收集阶段：

- 调用 `dep.depend` 方法。
- 如果 `childOb` 存在，调用 `childOb.dep.depend` 方法。
- 如果属性值是数组，调用 `dependArray` 方法。该方法会遍历数组中的每一项元素，如果数组中的元素是是响应式对象，会调用该对象的 `__ob__` 属性的 `dep.depend` 方法。

### Dep

`Dep` 类表示一个“依赖”，它的实例是一个可观察对象以供订阅。

在 `Dep` 类中定义了一个静态属性 `target`，表示当前全局正在处理的“依赖目标”，其类型为 `DepTarget`。模块中维护了一个 `targetStack` 栈，可以通过 `pushTarget` 和 `popTarget` 方法来操作，而 `Dep.target` 的值就是栈顶元素。

`Dep` 类包含以下属性或方法：

- `subs` 表示依赖于当前实例的 `DepTarget` 列表。
- `depend` 方法会判断当前是否存在 `Dep.target`，则执行 `Dep.target.addDep(this)` 将当前 `dep` 添加到 `Dep.target` 的依赖列表中。
- `notify` 方法遍历 `subs` 并调用 `DepthTarget` 的 `update` 方法。

### DepTarget

`DepTarget` 接口表示一个“依赖目标”，包含以下属性或方法：

- `id`：表示一个数字标识，在进行派发更新的时候用于进行排序。
- `addDep`：向当前目标添加依赖。
- `update`：更新当前目标。

### Watcher

`Watcher` 类实现了 `DepTarget` 接口，表示一个可以收集依赖的目标。
