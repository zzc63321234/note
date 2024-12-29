## `Mutation Observer API`

### `MutationObserver`

`MutationObserver` 接口提供了监视对 `DOM` 树所做更改的能力。

#### 使用示例

~~~javascript
// 选择需要观察变动的节点
const targetNode = document.getElementById("some-id");

// 观察器的配置（需要观察什么变动）
const config = { attributes: true, childList: true, subtree: true };

// 当观察到变动时执行的回调函数
const callback = function (mutationsList, observer) {
  // Use traditional 'for loops' for IE 11
  for (let mutation of mutationsList) {
    if (mutation.type === "childList") {
      console.log("A child node has been added or removed.");
    } else if (mutation.type === "attributes") {
      console.log("The " + mutation.attributeName + " attribute was modified.");
    }
  }
};

// 创建一个观察器实例并传入回调函数
const observer = new MutationObserver(callback);

// 以上述配置开始观察目标节点
observer.observe(targetNode, config);

// 之后，可停止观察
observer.disconnect();
~~~

#### 构造函数

使用 `new MutationOvserver(callback)` 创建一个 `MutationObserver` 实例。

接受一个 `callback`：

- 每当观测的 `DOM` 发生变动时，`callback` 的调用会被加入到微任务队列。

- 调用时会被传入两个参数：

  - `mutationsList`

    一个由用于描述发生改动的 `MutationRecord` 对象组成的数组。

  - 调用该 `callback` 的 `MutationObserver` 实例。

#### 实例属性

##### `observe()`

调用后会开启观察。

参数：

- `target`

  要观察的 `DOM`。

- `options`（可选）

  一个配置对象，用于描述 `DOM` 的哪些变化需要触发 `callback`。

  配置项的 `childList`、`attributes` 和 `characterData` 必须开启其中一个。否则会抛出 `TypeError` 异常。

  配置项如下：

  > - `subtree`
  >
  >   当为 `true` 时，将会监听以 `target` 为根节点的整个子树。包括子树中所有节点的属性，而不仅仅是针对 `target`。
  >
  >   默认值为 `false`。
  >
  > - `childList`
  >
  >   当为 `true` 时，监听 `target` 节点中发生的节点的新增与删除（同时，如果 `subtree` 为 `true`，会针对整个子树生效）。
  >
  >   默认值为 `false`。
  >
  > - `attributes`
  >
  >   当为 `true` 时观察所有监听的节点属性值的变化。
  >
  >   默认值为 `true`，当声明了 `attributeFilter` 或 `attributeOldValue`，默认值则为 `false`。
  >
  > - `attributeFilter`
  >
  >   一个用于声明哪些属性名会被监听的数组。
  >
  >   如果不声明该属性，所有属性的变化都将触发通知（即 `attributes` 为 `true`）。
  >
  > - `attributeOldValue`
  >
  >   当为 `true` 时，记录上一次被监听的节点的属性变化。
  >
  >   默认值为 `false`。
  >
  > - `characterData`
  >
  >   当为 `true` 时，监听声明的 `target` 节点上所有字符的变化。
  >
  >   默认值为 `true`，如果声明了 `characterDataOldValue`，默认值则为 `false`。
  >
  > - `characterDataOldValue`
  >
  >   当为 `true` 时，记录前一个被监听的节点中发生的文本变化。
  >
  >   默认值为 `false`。

##### `disconnect()`

调用后会停止观察。可以重新调用 `observer()` 来开启。

##### `takeRecords()`

返回已检测到但尚未由观察者的回调函数处理的所有匹配 `DOM` 更改的列表，使变更队列保持为空。此方法最常见的使用场景是在断开观察者之前立即获取所有未处理的更改记录，以便在停止观察者时可以处理任何未处理的更改。

示例：

~~~javascript
var targetNode = document.querySelector("#someElement");
var observerOptions = {
  childList: true,
  attributes: true,
};

var observer = new MutationObserver(callback);
observer.observe(targetNode, observerOptions);

/* ...later, when it's time to stop observing... */

/* handle any still-pending mutations */

var mutations = observer.takeRecords();

if (mutations) {
  callback(mutations);
}

observer.disconnect();
~~~

### `MutationRecord`

每个 `MutationRecord` 都代表一个独立的 `DOM` 变化。

见 [MutationRecord - Web API 接口参考 | MDN (mozilla.org)](https://developer.mozilla.org/zh-CN/docs/Web/API/MutationRecord)。

## `Resize Observer API`

`Resize Observer API` 提供了一种高性能的机制，通过该机制，代码可以监视元素的大小更改，并且每次大小更改时都会向观察者传递通知。

### `ResizeObserver`

`ResizeObserver` 构造函数创建一个新的 `ResizeObserver` 对象，它可以用于监听 `Element` 内容盒或边框盒或者 `SVGElement` 边界尺寸的大小。

#### 构造函数

使用 `new ResizeObserver(callback)` 创建一个 `ResizeObserver` 实例。

接收一个 `callback` 作为参数：

- 每当观测的元素调整大小时，调用该函数。
- `callback` 会被传入两个参数：
  - `entries`：一个 `ResizeObserverEntry` 对象数组（因为一个 `ResizeObserver` 实例可以监听多个目标），可以用于获取每个元素改变后的新尺寸。
  - `observer`：对 `ResizeObserver` 自身的引用，因此需要它的时候，你要从回调函数的内部访问。例如，这可用于在达到特定的情况时，自动取消对观察者的监听。

`callback` 通常遵循以下模式：

~~~javascript
function callback(entries, observer) {
  for (const entry of entries) {
    // Do something to each entry
    // and possibly something to the observer itself
  }
}
~~~

#### 实例属性

##### `observe()`

用于监听指定的目标元素。

接收两个参数：

- `target`：

  对要监听的元素的引用。

- `options`：

  一个可选对象，允许为监听的对象设置参数。

  目前只有 `box` 一个参数，用于设置 `observer` 将要监听的盒模型，可能的值有：

  > `content-box`（默认）
  > CSS 中定义的内容区域的大小。
  >
  > `border-box`
  > CSS 中定义的边框区域的大小。
  >
  > `device-pixel-content-box`
  > 在对元素或其祖先应用任何 CSS 转换之前，CSS 中定义的内容区域的大小，以设备像素为单位。

##### `unobserve()`

用于取消对指定目标元素的监听。

接收 `target` 作为参数。

##### `disconnect()`

调用后会取消对所有目标元素的监听。

### `ResizeObserverEntry`

`ResizeObserverEntry` 允许获取正在被观察的目标元素的最新大小。

`ResizeObserver` 构造函数接收的 `callback` 参数中，接收的第一个参数就是 `ResizeObserverEntry` 实例组成的数组。

#### 实例属性

##### `target`

正在被监听的目标元素。

##### `contentBoxSize`、`borderBoxSize`、`devicePixelContentBoxSize`

它们都是是一个数组，因为有些元素（例如使用了 `CSS` 多列布局的元素）拥有多个视觉片段。

这些数组中的对象都包含以下两个属性：

- `blockSize`：

  被监听的元素在块方向上的长度。

- `inlineSize`：

  被监听的元素在内联方向上的长度。

##### `contentRect`

一个 `DOMRectReadOnly` 对象，属于较早的 `API`，目前相对于 `contentBoxSize` 或 `borderBoxSize` 具有更高的兼容性，在未来版本中可能会被弃用。

## `Intersection Observer API`

https://developer.mozilla.org/zh-CN/docs/Web/API/IntersectionObserver