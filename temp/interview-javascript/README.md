---
id: interview-javascript

title: Interview JavaScript
---

# 数据类型类型检测

## typeof

- 返回表示检测目标数据类型的字符串。
- `typeof null` 返回 `'object'`，这是一个历史遗留问题。

## instanceof

- 用于检测构造函数的 `prototype` 属性是否出现在某个对象的原型链上。
- 如果检查的目标不是对象类型，会返回 `false`。
- 当原型链较深时，检查的效率较低。

## Object.prototype.toString

- 通过 `Object.prototype.toString.call(value)` 可以获取表示目标（包装）对象类型的字符串。

```javascript
Object.prototype.toString.call(undefined); // "[object Undefined]"
Object.prototype.toString.call(null); // "[object Null]"
Object.prototype.toString.call(true); // "[object Boolean]"
Object.prototype.toString.call(123); // "[object Number]"
Object.prototype.toString.call("abc"); // "[object String]"
Object.prototype.toString.call(Symbol("foo")); // "[object Symbol]"
Object.prototype.toString.call({}); // "[object Object]"
Object.prototype.toString.call(function () {}); // "[object Function]"
Object.prototype.toString.call([]); // "[object Array]"
Object.prototype.toString.call(new Date()); // "[object Date]"
Object.prototype.toString.call(/abc/); // "[object RegExp]"
```

## 其他

- `Array.isArray`
- `Number.isNaN`、`isNaN`
- `Number.isFinite`、`isFinite`

# 原型链

- 原型链是 JavaScript 实现继承的一种机制。
- 每个函数都有一个 `prototype` 属性，指向一个对象，称为原型。其 `constructor` 属性指向该函数本身。
- 每个对象都有一个内部属性 `[[Prototype]]`，指向其构造函数原型 `prototype`，称为原型对象。原型对象之间构成了原型链，直到某个对象的原型对象为 `null`。
- `new` 操作符将函数作为构造函数进行调用。首先创建一个新对象，将新对象的 `[[Prototype]]` 指向构造函数的 `prototype`；构造函数内部的 `this` 指向新对象；执行构造函数逻辑；若构造函数没有显示返回一个对象，则返回该新对象，否则按返回值返回。
- 在查找对象属性时，如果对象本身没有该属性，会沿着原型链向上查找，直到找到该属性或到达原型链末尾。
- 当原型链较长时，在原型链上查找属性的效率较低；当试图访问不存在的属性时会遍历整个原型链。
- `Object.create` 方法以指定对象为原型创建新对象。
- `Object.getPrototypeOf` 方法返回指定对象的原型对象。
- `Object.setPrototypeOf` 方法设置指定对象的原型对象。
- `Object.prototype.isPrototypeOf` 方法判断一个构造函数的 `prototype` 属性是否出现在指定对象的原型链中。

# 作用域

- 作用域指在程序中定义变量的可访问范围。
- 全局作用域：在代码中任何地方都可以访问，在整个应用程序中都是可访问的。
- 函数作用域：在函数内部定义的变量只能在函数内部访问，函数外部无法访问。
- 块级作用域：在代码块内部定义的变量只能在代码块内部访问，代码块外部无法访问。使用 `let` 和 `const` 声明的变量具有块级作用域。
- 作用域链：当查找变量时，JavaScript 引擎会沿着作用域链向上查找，直到找到该变量或到达全局作用域。

# 闭包

- 闭包闭包是指有权访问另一个函数作用域（或执行上下文）中变量的函数。当一个函数执行完毕并返回一个内部定义的函数时，其内部定义的变量仍然被保留在内存中，不会被自动回收。
- 优点：私有化数据，从而避免命名冲突和变量污染。
- 缺点：使用不当可能导致内存泄漏，在不需要使用的时候需要及时置为 `null`。
- 使用场景：保存内部状态，私有化变量；封装类和模块；常用于事件处理、回调函数等；函数式编程（柯里化等）；实现函数缓存、记忆化等功能。
- 如何避免闭包引起的内存泄漏：避免创建闭包；及时释放闭包，将闭包持有的变量置为 `null` 或将闭包函数置为 `null`。

# this 指向

- 通过 `call`、`apply`、`bind` 可以改变函数执行时的 `this` 指向。非严格模式下，若 `thisArg` 为 `null` 或 `undefined`，则指向全局对象；若为其他基本数据类型，则会自动转换为对应的包装对象。严格模式下，不会进行转换。
- 多次调用 `bind` 时，只会在第一次调用时改变 `this` 指向。
- 直接调用函数时，等同于将 `undefined` 作为 `thisArg`。
- 作为对象方法调用时，`this` 指向调用该方法的对象。
- 作为构造函数调用时，`this` 指向新创建的实例对象。
- 箭头函数没有自己的 `this`，其 `this` 指向函数定义时所在的作用域的 `this`。

# 箭头函数

- 没有 `this`，其 `this` 指向函数定义时所在的作用域的 `this`。
- 没有 `arguments` 对象，可以使用剩余参数 `...args` 替代。
- 不能作为构造函数，不能使用 `new` 关键字调用。
- 优点：箭头函数语法简洁，创建开销较小。

# 事件流

- 捕获阶段：事件从根节点向目标节点传播。
- 目标阶段：事件到达目标节点。
- 冒泡阶段：事件从目标节点向根节点传播。
- 可以通过 `addEventListener` 的第三个参数指定事件处理函数在捕获阶段还是冒泡阶段执行。
- 可以通过 `event.stopPropagation` 阻止事件继续冒泡。
- 事件委托是一种优化事件处理的方法，通过将事件处理程序添加到父元素上，利用事件冒泡机制，通过事件对象的 `target` 属性判断触发事件的目标元素，减少事件处理程序的数量，提高性能。

# 浮点数

- 在 JavaScript 中，浮点数是以 IEEE 754 标准的二进制浮点数表示的，它采用二进制的形式来表示实数，有 64 位精度。
- 而二进制无法精确地表示某些十进制小数，例如 0.1 和 0.2，因为它们在二进制下是无限循环的小数，它们会被转换为最接近的二进制表示，导致微小的舍入误差。
- 可以使用 toFixed() 方法将结果四舍五入到指定小数位数。该方法返回一个字符串，需要注意类型转换。

# Symbol

- `Symbol` 是 ES6 新增的一种基本数据类型，表示独一无二的值。
- `Symbol` 值可以作为对象属性名，避免属性名冲突。或者作为常量、枚举值使用。
- `Symbol` 作为属性名时，不会被 `for...in`、`Object.keys`、`Object.getOwnPropertyNames` 等方法遍历，但可以通过 `Object.getOwnPropertySymbols` 方法获取。

# 函数柯里化

- 柯里化是指将接受多个参数的函数转换为接受一个参数的函数，并返回接受余下参数的函数。
- 柯里化的优点是可以延迟执行，可以复用子函数。

```javascript
function curry(fn) {
  return function curried(...args) {
    if (args.length >= fn.length) {
      return fn.apply(this, args);
    } else {
      return function (...rest) {
        return curried.apply(this, args.concat(rest));
      };
    }
  };
}
```

# 循环异步

TODO
