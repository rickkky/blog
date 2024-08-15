---
id: interview-browser

title: Interview Browser
---

# 浏览器安全

## XSS 攻击

跨站脚本攻击（Cross Site Scripting，XSS）是一种代码注入攻击。攻击者在目标网站注入恶意脚本，当用户访问网站时，恶意脚本会被执行，从而达到攻击目的。

攻击者可以通过 XSS 攻击实现以下目的：

- 获取页面数据，如 DOM、Cookie、LocalStorage 等。
- DOS 攻击，频繁向服务器发送合理请求，占用服务器资源。
- 破坏页面结构。
- 流量劫持，跳转至其他网站。

分类：

- 存储型。恶意脚本存储在目标网站的数据库中，浏览器请求数据时，恶意脚本会被返回并执行。常用于带有保存用户输入数据的功能，如论坛发帖、评论、私信等。
- 反射型。诱使用户点击包含恶意脚本的链接，将恶意脚本作为输入数据的一部分提交给服务器，如果服务器在返回数据给用户浏览器时未对这些输入进行处理，用户的浏览器就会执行这些恶意脚本。常用于通过 URL 传递参数的功能，如搜索、跳转等。
- DOM 型。主要发生在前端处理用户输入或 URL 参数时，若没有对这些输入进行处理，将恶意脚本插入到页面中或作为动态生成的代码执行。

防范措施：

- 输入验证。对所有用户输入进行严格验证，确保只接收符合预期格式的数据。
- 输出转义。将用户输入的数据输出到页面时，使用适当的转义函数（如 HTML 实体编码）对数据进行转义。
- 内容安全策略（Content Security Policy，CSP）。通过设置 CSP，限制页面加载资源的来源，防止恶意脚本的注入。本质是建立白名单，规定哪些外部资源可以加载和执行。
- HttpOnly。设置 Cookie 时添加 HttpOnly 属性，防止 JavaScript 通过 `document.cookie` 获取 Cookie。

## CSRF 攻击

跨站请求伪造（Cross Site Request Forgery，CSRF）是一种利用用户已登录状态的身份信息发起恶意请求的攻击方式。攻击者诱使用户在已登录网站上点击包含恶意请求的链接，当用户点击链接进入第三方网站时，第三方网站（通过隐藏表单、图片或其他手段）能够携带用户在已登录网站的 Cookie 发起跨站请求，冒充用户发起恶意请求。

第三方网站无法获取被攻击网站的 Cookie，只能利用 Cookie 在同源请求中会自动携带的特性。

常见类型：

- GET 类型。例如在网站的 `img` 标签中构建一个请求，当用户访问第三方网站时会自动发起请求；或者诱使用户点击包含恶意请求的链接。
- POST 类型。例如在网站中构建一个隐藏表单，当用户访问第三方网站时自动提交表单。

防范措施：

- 检查 Referer 头。验证请求 HTTP 头部中的 Referer 值，确保请求来自合法的页面。缺点在于 Referer 头可能被篡改；以及可能会屏蔽来自搜索引擎的流量。
- CSRF Token。服务端为用户会话生成一个唯一的随机 Token，要求客户端请求时带上这个 Token 进行验证，以解决用 Cookie 单一验证的问题。缺点在于，需要为所有请求都添加 Token，增加了开发成本；以及服务端分布式环境下需要考虑 Token 的同步问题。
- 双重 Cookie 验证。要求客户端请求时在请求参数中带上 Cookie 中的某些信息，服务端验证 Cookie 和请求参数是否匹配。
- SameSite Cookie。设置 Cookie 的 SameSite 属性，限制第三方网站请求时携带 Cookie。`Strict` 模式下，完全禁止第三方请求携带 Cookie；`Lax` 模式下，只有 GET 类型的请求可以携带 Cookie。

## 网络劫持

# 浏览器缓存

## 强缓存

- Expires：设置了一个日期/时间，告诉浏览器在这个时间点之前可以直接从缓存读取数据。缺点在于服务器时间和客户端时间可能不一致。
- Cache-Control：优先级更高。
  - `no-cache`：指示浏览器在获取资源前必须先与服务器确认资源是否已更新。
  - `no-store`：禁止浏览器和代理服务器存储任何版本的资源。
  - `public`：表示响应可以被任何中间缓存存储。
  - `private`：表示响应是为单个用户准备的，不能被共享。
  - `max-age=<seconds>`：指示资源能够被缓存多久（以秒为单位）。
  - `s-maxage=<seconds>`：覆盖 `max-age`，只生效于共享缓存，如 CDN。
  - `max-stale=<seconds>`：指示浏览器可以接收过期时间超过 `max-age` 的资源。

## 协商缓存

- Last-Modified / If-Modified-Since
  - Last-Modified：响应头，表示资源最后一次修改的时间。
  - If-Modified-Since：请求头，表示上次响应的 Last-Modified 时间。
- ETag / If-None-Match：优先级更高。
  - ETag：响应头，表示资源的唯一标识，通常是资源内容的哈希值。
  - If-None-Match：请求头，表示上次响应的 ETag 值。

## Service Worker

## Push Cache

Push Cache，也称为 HTTP/2 Push，是 HTTP/2 协议的一个特性，允许服务器在客户端请求之前主动推送资源到客户端的缓存中。这种机制可以减少页面加载时间，因为客户端可以在接收到 HTML 文档之前就开始下载页面所需的资源。

工作原理：

- 服务器在发送 HTML 响应时，可以附带多个 PUSH_PROMISE 帧，这些帧包含了将要推送的资源的信息。
- 客户端接收到 PUSH_PROMISE 帧后，会将推送的资源缓存起来，即使客户端还没有请求这些资源。
- 当 HTML 文档中的资源被请求时，如果已经在缓存中，浏览器可以直接使用缓存的资源，而不需要再次发起网络请求。

## 浏览器刷新

- 点击刷新按钮（F5）：浏览器请求会带上 `Cache-Control: max-age=0`，表示不使用强缓存。刷新只影响请求的 URL 本身，不会影响其他资源。
- 强制刷新（Ctrl + F5）：浏览器请求会带上 `Cache-Control: no-cache`，且不会带上协商缓存的请求头，表示不使用缓存。刷新会影响所有资源。

# 浏览器的同源策略

## CORS

CORS（Cross-Origin Resource Sharing，跨源资源共享）是一种安全协议，它允许或限制 Web 页面对不同源（域名、协议、端口）的资源进行访问。这是浏览器的一种同源安全策略，用于防止恶意网站访问敏感数据。

工作原理：

- 预检请求。对于非简单请求，浏览器会首先发送一个预检请求（OPTIONS 请求），询问服务器是否允许实际的请求。
- 简单请求。满足以下条件的请求是简单请求，不会触发 CORS 预检请求。
  - 请求方法是 GET、HEAD、POST。
  - 请求头只包含以下字段：Accept、Accept-Language、Content-Language、Content-Type（仅限于 `application/x-www-form-urlencoded`、`multipart/form-data`、`text/plain`）、Range（仅限于 `bytes=`）。
  - 若请求又 `XMLHttpRequest` 发起，在返回的 `XMLHttpRequest.upload` 对象上没有注册任何事件监听器。
  - 请求中没有使用 `ReadableStream` 对象。
- 服务器响应。Access-Control-Allow-X 系列响应头用于告诉浏览器是否允许跨域请求。
  - Access-Control-Allow-Origin：指定允许访问的源。判断请求头中的 `Origin` 是否在许可范围内。
  - Access-Control-Allow-Methods：指定允许的请求方法。对应预检请求头中的 Access-Control-Request-Method，指定 CORS 请求会用到哪些 HTTP 方法。
  - Access-Control-Allow-Headers：指定允许的请求头字段。对应预检请求头中的 Access-Control-Request-Headers，指定 CORS 请求会额外发送的头信息字段。
  - Access-Control-Allow-Credentials：指定是否允许发送 Cookie。须在请求中设置 `withCredentials=true`。
  - Access-Control-Max-Age：指定预检请求的有效期。

## JSONP

JSONP（JSON with Padding）是一种跨域请求的方法，通过动态创建 `script` 标签，实现跨域请求数据。JSONP 的原理是利用 `script` 标签的跨域特性，可以加载其他域下的 JavaScript 脚本。

假设客户端有一段代码：

```javascript
var script = document.createElement("script");
script.type = "text/javascript";
script.src = "http://example.com/data?callback=globalJsonpCallback";
document.head.appendChild(script);
function globalJsonpCallback(data) {
  console.log(data);
}
```

服务端返回：

```javascript
globalJsonpCallback("Hello, JSONP!");
```

- 仅支持 GET 请求。
- 安全性问题。JSONP 是通过动态创建 `script` 标签实现的，因此无法保证返回的数据是否安全，可能会被注入恶意代码。

# 性能优化

## 关键渲染路径

关键渲染路径（Critical Rendering Path，简称 CRP）是指浏览器从获取 HTML、CSS 和 JavaScript 文件到渲染出屏幕上可见内容的整个过程。优化关键渲染路径可以显著提高页面的加载速度和用户体验。以下是关键渲染路径的主要步骤：

- 解析 HTML：解析 HTML 文档，构建 DOM 树。
- 构建 CSSOM：解析 CSS 文件，构建 CSSOM 树，这是页面的样式信息。
- 构建渲染树：将 DOM 树和 CSSOM 结合起来，生成渲染树。渲染树包含了页面上所有可见元素的列表，以及它们的样式信息。
- 布局（Layout）：计算渲染树中每个元素的尺寸和位置。这一步确定了每个元素在页面上的确切位置。
- 绘制（Painting）：遍历渲染树，将每个元素绘制到缓冲区。这一步涉及到实际的像素输出。
- 合成（Compositing）：如果页面中有多个层，会将这些层合成在一起，形成最终的屏幕图像。这一步可能涉及到 GPU 加速。

优化关键渲染路径的方法：

- 减少关键资源的数量和大小。减少 HTML、CSS 和 JavaScript 文件的大小，可以减少下载时间；减少关键资源的数量，可以减少解析和构建的时间。
- 减少关键资源的加载时间。使用合适的资源压缩和合并技术，减少关键资源的加载时间。
- 优化 CSS 和 JavaScript 的加载和执行顺序。将 CSS 文件放在页面头部，JavaScript 文件放在页面底部，避免阻塞页面渲染。
- 延迟加载非关键资源。将非关键的 CSS 和 JavaScript 文件延迟加载，让浏览器先渲染可见内容。
- 使用浏览器缓存。合理利用浏览器缓存，减少资源的重复下载。
- 减少重排和重绘。避免频繁的 DOM 操作和样式修改，减少页面的重排和重绘。

## 性能指标

- 加载时间（Load Time）：页面从加载到完全可用所需的时间。
- 首次内容绘制（First Contentful Paint，FCP）：页面首次绘制任何内容到屏幕上的时间。
- 首次有意义绘制（First Meaningful Paint，FMP）：页面主要内容可见的时间，通常指页面上主要内容已经渲染完成。
- 交互时间（Time to Interactive，TTI）：页面从开始加载到足够稳定，可以进行复杂交互的时间。

...

## 浏览器事件

## 事件模型

- DOM0 级事件模型。直接在 HTML 元素上或 DOM 对象上添加事件处理程序，如 `onclick` 等。不会传播，所以没有事件流的概念。但是现代浏览器支持以冒泡方式实现。
- IE 事件模型。过时了，懒得记了。
- DOM2 级事件模型。事件捕获阶段，事件从根元素向下传播到目标元素；事件处理阶段，执行目标元素绑定的事件监听函数；事件冒泡阶段，事件从目标元素向上传播到根元素。可以通过 `addEventListener` 和 `removeEventListener` 方法添加和移除事件监听器。

## 事件委托

事件委托（Event Delegation）是一种优化页面事件处理的方法，通过将事件处理程序添加到父元素上，利用事件冒泡机制，将事件处理委托给父元素处理。这样可以减少事件处理程序的数量，提高性能。

# 事件循环

## Node 事件循环

Node.js 的事件循环分为 6 个阶段，它们会按照顺序反复运行。每当进入某一阶段的时候，都会从对应的回调队列中取出函数执行。当队列为空或者执行的回调函数达到系统设定的阈值时，事件循环会进入下一个阶段。

- Timers 阶段。初次进入事件循环，会从 Timers 阶段开始。这个阶段会判断是否存在已到期的计时器回调（`setTimeout`、`setInterval`），如果存在则执行所有到期的回调。执行完毕后，如果回调中触发了微任务，会接着执行所有微任务，之后进入 Pending Callbacks 阶段。
- Pending Callbacks 阶段。执行推迟到下一个循环迭代的 I/O 回调（系统调用相关的回调）。
- Idle/Prepare 阶段。仅在内部使用。
- Poll（轮询）阶段。
  - 当回调队列不为空时，会执行回调，若回调中触发了微任务，在每一个回调执行完毕后都会执行所有微任务。执行完所有回调以后，进入 Poll 阶段的另一种情况。
  - 当回调队列为空时，若存在计时器回调，会进入 Check 阶段；否则会阻塞并等待任何正在执行的 I/O 操作完成，立即执行相应的回调。
- Check（检查）阶段。执行 `setImmediate` 的回调。如果回调中触发了微任务，会接着执行所有微任务，之后进入 Close Callbacks 阶段。
- Close Callbacks 阶段。执行一些关闭相关的回调，如 `socket.on('close', ...)` 等。

Node.js 中的 `process.nextTick` 是独立于事件循环的，它有一个自己的队列，当每个阶段完成后，如果 `nextTick` 队列不为空，会清空队列中的所有回调函数，且优先于微任务队列执行。

# 内存模型

## V8 垃圾回收机制

垃圾回收是自动内存管理的一部分，它负责识别和释放不再使用的内存。V8 引擎的 GC 采用了分代回收的策略，将堆内存分为新生代和老生代两部分。分代回收是基于一个假设：大部分对象的生命周期很短，很快就会被销毁，而只有少部分对象的生命周期很长。

### 新生代算法

新生代空间中的对象一般存活时间较短，采用 Scavenge 算法。

新生代空间中分为两个区域：From 空间和 To 空间。这两个空间必定有一个空间处于空闲状态，另一个空间处于使用状态。新分配的对象会被放入 From 空间，当 From 空间被占满时，会触发 GC 过程。算法会检查 From 空间中的存活对象，将存活对象复制到 To 空间中，并销毁失活对象。完成复制后，将 From 空间和 To 空间互换。

### 老生代算法

经过多次 Scavenge 回收仍然存活的对象会被移动到老生代空间。或者当 To 空间的对象占比超过 25%，为了避免影响内存分配，会将对象从新生代空间移动到老生代空间。老生代空间中的对象一般存活时间较长且数量较多。

- 标记清除（Mark-Sweep）。用于识别哪些对象是“活”的（仍在使用），哪些是“死”的（不再使用）。在标记阶段遍历对象图，标记所有可达对象；在清除阶段清除未标记的对象。
- 标记压缩（Mark-Compact）。清除对象会导致内存碎片。在标记清除的基础上，将存活对象向一端移动，清理出一块连续的内存空间，避免内存碎片。
- 增量标记。将标记过程分解为多个小步骤，可以在执行 JavaScript 的间隙执行垃圾回收，减少单次回收的停顿时间。
- 并发标记。可以让 GC 扫描标记对象的同时运行 JavaScript 运行。

## 内存泄漏

- 全局变量。全局变量会一直存在于内存中，直到页面关闭。使用未声明的变量可能会错误地创建全局变量。
- 未清除的 `setInterval`。
- 保留了对已被移除的 DOM 元素的引用。
- 不合理使用闭包。
