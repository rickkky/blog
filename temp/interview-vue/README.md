---
id: interview-vue

title: Interview Vue
---

# 响应式

## Vue2 响应式原理

### 响应式对象

初始化过程中，会调用 `observe` 方法将 `data` 和 `props` 转换为响应式对象。

#### observe

`observe` 方法返回一个 `Observer` 实例。

- 如果对象存在 `__ob__` 属性且是 `Observer` 实例，说明该对象已经是响应式对象，返回其 `__ob__` 属性。
- 否则，在满足一定条件的情况下，创建一个 `Observer` 实例。

可以通过给对象添加 `__v_skip: true` 属性来避免对象被转换为响应式对象。

#### Observer

`Observer` 的构造函数主要进行了以下操作：

- 创建一个 `Dep` 实例，用于收集依赖。
- 给对象添加 `__ob__` 属性，值为当前 `Observer` 实例。
- 判断对象是否为数组，如果是数组，调用 `observeArray` 方法，将数组中的每一项转换为响应式对象。
- 否则遍历对象的属性，对每个属性调用 `defineReactive` 方法。

#### defineReactive

`defineReactive` 将对象的属性转换为响应式属性。其主要工作原理如下：

- 创建一个 `Dep` 实例，用于收集依赖。
- 获取属性的值，如果属性的值是对象，调用 `observe` 方法将其转换为响应式对象，并获取子 `Observer` 实例。
- 通过 `Object.defineProperty` 为属性添加 `getter` 和 `setter`。
