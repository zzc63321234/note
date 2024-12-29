`RxJS` 是一个**使用可观察序列**编写**异步和基于事件的程序**的库。

`Rx` 的含义为 `ReactiveX`、 `Reactive Extensions`。

`RxJS` 中处理异步事件管理的基本概念有：

- **`Observable`（可观察者）：**表示一个可订阅的异步的值或事件的集合的概念。
- **`Observer`（观察者）：**是一个回调函数的集合，它知道如何监听 `Observable` 传来的值。
- **`Subscription`（订阅）：**表示 `Observable` 的执行，主要用于取消 `Observable` 的执行。
- **`Operator`（操作符）：**采用函数式编程风格的纯函数，可以使用 `map`、`filter`、`concat`、`reduce` 等操作符来处理集合。
- **`Subject`（主体）：**相当于 `EventEmitter`，它将一个值或事件推送给多个 `Observers` 的唯一方式。
- **`Scheduler`（调度器）：**用来控制并发并且是中央集权的调度员，允许我们在发生计算时进行协调，例如 `setTimeout` 或 `requestAnimationFrame` 或其它。

## `Observables` 和 `Observer`

`Observable` 可创建一个数据流，这个流可以异步发出多个值，且可以被订阅。

示例：

~~~javascript
import { Observable } from 'rxjs';

// 创建一个 Observable，它在订阅时立即（同步）推送值 1、2、3，并且在 subscribe 调用过一秒之后推送值 4，然后完成
const observable = new Observable((subscriber) => {
  subscriber.next(1);
  subscriber.next(2);
  subscriber.next(3);
  setTimeout(() => {
    subscriber.next(4);
    subscriber.complete();
  }, 1000);
});

// 可以订阅 Observable 以查看这些值
console.log('just before subscribe');
observable.subscribe({
  next(x) {
    console.log('got value ' + x);
  },
  error(err) {
    console.error('something wrong occurred: ' + err);
  },
  complete() {
    console.log('done');
  },
});
console.log('just after subscribe');

// 控制台执行结果如下：
// just before subscribe
// got value 1
// got value 2
// got value 3
// just after subscribe
// got value 4
// done
~~~

### 拉取与推送

拉取（`Pull`）和推送（`Push`）是两种不同的协议，用于描述数据生产者（`Producer`）如何与数据消费者（`Consumer`）通信。

以下说法中，**单值** 指的是 `0` 或 `1` 个值，**多值** 指的是 `0` 到无限多个值。

#### 拉取

在拉取体系中，由 `Consumer` 决定何时从 `Producer` 接收数据，而 `Produer` 本身不知道数据何时交付给 `Consumer`。

例如 `JavaScript` 函数是一个拉取体系，函数是数据的 `Producer`，通过调用函数来从函数中**拉取**出**单个**返回值来使用它。

例如 `ES6` 中的 `Iterator` 对象也是一个拉取体系，`Iterator` 对象是数据的 `Producer`，通过多次调用其 `next()` 方法来从 `Iterator` 中**拉取**出**多个值**。

#### 推送

在推送体系中，由 `Producer` 决定何时向 `Consumer` 发送数据，而 `Comsumer` 不知道何时会收到数据。

例如 `Promise` 是一个推送体系，`Promise` 是数据的 `Producer`，它会决定何时**推送**解析后的**单个值**到已注册的回调函数中。

而 `Observable` 也是一个推送体系，它是**多个值**的 `Producer`，并将它们**推送**给 `Observer`，`Observer` 即是这个推送体系的 `Consumer`。

### `Observables` 特性

`Observable` 和函数一样都是惰性计算的。如果不调用函数，那么函数体中的语句就不会发送。同样对于 `Observable`，如果不调用其 `subscribe` 方法，那么传入的回调函数中的语句就不会发生。函数的调用和 `Observable` 的订阅都是一种孤立的操作：如果同一个函数被调用两次，就会产生两个单独的副作用；如果同一个 `Observable` 被订阅两次，也会出发两个单独的副作用。

同一个函数调用两次示例：

~~~javascript
function foo() {
  console.log('Hello');
  return 42;
}

const x = foo();
console.log(x);
const y = foo();
console.log(y);

// 控制台输出如下：
// "Hello"
// 42
// "Hello"
// 42
~~~

同一个 `Observable` 订阅两次：

~~~javascript
import { Observable } from 'rxjs';
 
const foo = new Observable((subscriber) => {
  console.log('Hello');
  subscriber.next(42);
});
 
foo.subscribe((x) => {
  console.log(x);
});
foo.subscribe((y) => {
  console.log(y);
});

// 控制台输出如下：
// "Hello"
// 42
// "Hello"
// 42
~~~

### `Observable` 和 `Observer` 的使用

`Observable` 类实例化时接收一个 `subscribe` 函数，在 `subscribe` 函数中定义的代码表示一次 `Observable` 执行。`subscribe` 函数接收一个 `subscriber` 对象作为参数，可以用来传递值给订阅者。

`Observable` 执行时可以传递三种类型的通知：

- `Next` 通知：发送数据。通过 `subscriber.next()` 发送。
- `Error` 通知：发送 `JS` 异常。通过 `subscriber.error()` 发送。
- `Complete` 通知：表示完成，不发送值。通过 `subscriber.complete()` 发送。

`Next` 通知是最重要和最常见的类型，代表要传递给订阅者的实际数据。如果发送了 `Error` 或 `Complete` 通知，则之后将无法发送任何其它通知。

调用 `Observable` 实例的 `subscribe()` 方法可以启动一个  `Observable` 流。`subscribe()` 方法可接收一个 `Observer` 对象，`Observer` 对象中可以定义三种方法，分别用于处理 `Next`、`Error`、`Complete` 三种类型的通知：

~~~javascript
const observer = {
  next: (x) => console.log('Observer got a next value: ' + x),
  error: (err) => console.error('Observer got an error: ' + err),
  complete: () => console.log('Observer got a complete notification'),
};

observable.subscribe(observer);
~~~

 `subscribe()` 也可以分别将这这三个方法作为参数接收，接收顺序是 `next`、`error`、`complete`。这样 `RxJS` 会将它们作为通知处理方法创建一个 `Observer` 对象。但通过这种方式传入 `error` 和 `complete` 处理方法可能将被 `RxJS` 弃用，只推荐在不需要处理 `error` 通知和 `complete` 通知时单独将一个回调函数作为参数传入。

另外，调用 `Observable` 实例的 `subscribe()` 方法后，会得到一个返回值对象 `subscription`，`subscription` 上存在一个 `unsubscribe()` 方法，调用它可以手动将流停止。手动停止流后，`Observable` 中定义的已进入任务队列的异步任务并不会被阻止，只是 `Observer` 中的回调不会再执行。

## `Operators`

`Operators` 本质上是一种函数，分为 `Creation Operators` 和 `Pipeable Operators`。其中  `Creation Operators` 可用来创建新的 `Observable`，`Pipeable Operators` 可以对 `Observable` 发出的数据进行各种操作。

详见：[RxJS - RxJS Operators (rxjs-dev.firebaseapp.com)](https://rxjs-dev.firebaseapp.com/guide/operators#creation-operators-1)、[RxJS 操作符 - RxJS 中文版](https://rxjs.tech/guide/operators#categories-of-operators)

[操作符决策树](https://rxjs.tech/operator-decision-tree)

## `Subjects`

`Subject` 是一种特殊类型的 `Observable`，它同时也是 `Observer`。

`Subject` 作为 `Observable` 时，相比于普通 `Observable` 是单播的（当多个 `Observer` 订阅同一个普通 `Observable` 时，每个 `Observer` 订阅的是独立的流），`Subject` 是可以多播到多个 `Observer` 的（当多个 `Observer` 订阅同一个 `Subject` 时，订阅的是同一个流）。`Subject` 实例通过调用自身的 `next()`、`error()`、`complete()` 发送通知，且自身作为 `Observer` 调用 `subscribe` 方法接收通知。 示例：

~~~typescript
import { Subject } from 'rxjs';
 
const subject = new Subject<number>();
 
subject.subscribe({
  next: (v) => console.log(`observerA: ${v}`),
});
subject.subscribe({
  next: (v) => console.log(`observerB: ${v}`),
});
 
subject.next(1);
subject.next(2);
 
// Logs:
// observerA: 1
// observerB: 1
// observerA: 2
// observerB: 2
~~~

`Subject` 的行为更接近发布订阅模式，通过调用 `subscribe()` 去订阅，通过调用 `next()` 去触发。

`Subject` 作为 `Observer` 时，可以作为其它 `Observable` 的 `subscribe` 方法的参数以观察其它 `Observable`：

~~~typescript
import { Subject, from } from 'rxjs';
 
const subject = new Subject<number>();
 
subject.subscribe({
  next: (v) => console.log(`observerA: ${v}`),
});
subject.subscribe({
  next: (v) => console.log(`observerB: ${v}`),
});
 
const observable = from([1, 2, 3]);
 
observable.subscribe(subject); // You can subscribe providing a Subject
 
// Logs:
// observerA: 1
// observerB: 1
// observerA: 2
// observerB: 2
// observerA: 3
// observerB: 3
~~~
