`window.requestAnimationFrame()` 告诉浏览器——你希望执行一个动画，并且要求浏览器在下次重绘之前调用指定的回调函数更新动画。该方法需要传入一个回调函数作为参数，该回调函数会在浏览器下一次重绘之前执行。

`requestAnimationFrame()` 接收一个 `callback`，会在下一次重绘时调用。通常它每秒执行的次数与浏览器屏幕刷新率相同。`callback` 中接收一个 `DOMHighResTimeStamp` 参数，它与 `performance.now()` 的返回值类型相同，指示当前被 `requestAnimationFrame()` 排序的回调函数被触发的时间戳，该时间戳是一个十进制数，单位为毫秒。

`requestAnimationFrame()` 返回一个整数，表示请求 `ID`，可以将返回值传递给 `window.cancelAnimationFrame()` 以取消已提交到计划中的动画帧请求。

示例：

~~~javascript
const element = document.getElementById("some-element-you-want-to-animate");
let start, previousTimeStamp;
let done = false;

function step(timestamp) {
  if (start === undefined) {
    start = timestamp;
  }
  const elapsed = timestamp - start;

  if (previousTimeStamp !== timestamp) {
    // 这里使用 Math.min() 确保元素在恰好位于 200px 时停止运动
    const count = Math.min(0.1 * elapsed, 200);
    element.style.transform = `translateX(${count}px)`;
    if (count === 200) done = true;
  }

  if (elapsed < 2000) {
    // 2 秒之后停止动画
    previousTimeStamp = timestamp;
    if (!done) {
      window.requestAnimationFrame(step);
    }
  }
}

window.requestAnimationFrame(step);
~~~

~~~javascript
const requestAnimationFrame =
  window.requestAnimationFrame ||
  window.mozRequestAnimationFrame ||
  window.webkitRequestAnimationFrame ||
  window.msRequestAnimationFrame;

const cancelAnimationFrame =
  window.cancelAnimationFrame || window.mozCancelAnimationFrame;

const start = Date.now();

let myReq;

function step(timestamp) {
  const progress = timestamp - start;
  d.style.left = `${Math.min(progress / 10, 200)}px`;
  if (progress < 2000) {
    // 在每次调用 requestAnimationFrame 时，注意更新 requestId
    myReq = requestAnimationFrame(step);
  }
}
myReq = requestAnimationFrame(step);
// 取消操作使用的是最后一个 requestId
cancelAnimationFrame(myReq);
~~~

