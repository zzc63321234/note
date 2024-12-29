## `AbortController`

`AbortController` 接口表示一个控制器对象，可以根据需要终止一个或多个 `Web` 请求。

使用 `new AbortController()` 创建一个新的 `AbortController` 实例。

实例属性：

- `signal`

  只读属性。

  返回一个 `AbortSignal` 对象实例。

- `abort()`

  调用 `abort()` 可以将 `signal.aborted` 转变为 `true`，并触发它的 `abort` 事件 。

使用示例：
~~~javascript
let controller;
const url = "video.mp4";

const downloadBtn = document.querySelector(".download");
const abortBtn = document.querySelector(".abort");

downloadBtn.addEventListener("click", fetchVideo);

abortBtn.addEventListener("click", () => {
  if (controller) {
    controller.abort();
    console.log("中止下载");
  }
});

function fetchVideo() {
  controller = new AbortController();
  const signal = controller.signal;
  fetch(url, { signal })
    .then((response) => {
      console.log("下载完成", response);
    })
    .catch((err) => {
      console.error(`下载错误：${err.message}`);
    });
}
~~~

## `AbortSignal`

`AbortSignal` 接口表示一个信号对象（`signal object`）。继承自 `EventTarget` 接口，可以触发 `abort` 事件。

得到 `AbortSignal` 实例的方式：

- `AbortController` 实例的 `signal` 属性。
- 通过 `AbortController.abort()` 静态方法返回一个已经被设置为中止的 `AbortSignal` 实例
- 通过 `AbortController.timeout()` 静态方法返回一个在指定事件后将自动终止的 `AbortSignal` 实例。

实例属性：

- `aborted`

  只读属性。

  返回一个布尔值，表示当前 `signal` 是否处于 `aborted` 状态。

- `reason`

  只读属性。

  一个使用 `JavaScript` 值表示的中止原因，如果没有被中止，则是 `undefined`。

- `throwIfAborted()`

  如果 `signal` 已经被中止，则 `throwIfAborted()` 方法抛出中止的 `reason`，否则什么也不做。

  