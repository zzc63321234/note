## `Symbol`（符号）

`symbol` 是 `ES6` 中引入的一种基本数据类型。

一个 `symbol`值能作为对象属性的标识符，这也是该数据类型的仅有的目的。

### 创建 `symbol`

调用 `Symbol([description])` 会返回一个 `symbol` 类型的值，且每次返回的 `symbol` 值都是唯一的。`description` 是一个可选的字符串类型的参数，是用于对生成的 `symbol` 描述，仅用于调试时方便分辨，不影响生成的 `symbol` 的唯一性：

~~~javascript
var sym1 = Symbol();
var sym2 = Symbol("foo");
var sym3 = Symbol("foo");

console.log(sym1); // Symbol()
console.log(sym2); // Symbol(foo)
console.log(sym3); // Symbol(foo)
console.log(sym2 === sym3); // false
~~~

### `Symbol` 类

`Symbol` 类似于 `Number`、`String`、`Boolean` 这样的内建对象类，但它并不是一个完整的构造函数，无法像其它三者一样可以直接用 `new` 关键字创建一个包装器对象：

~~~javascript
const num = new Number(1);
const str = new String('str');
const bool = new Boolean(true);

console.log(num); // Number {1}
console.log(str); // String {'str'}
console.log(bool); // Boolean {true}

const sym = new Symbol(); // Uncaught TypeError: Symbol is not a constructor
~~~

在 `ES6` 的设计中，已不推荐使用基本数据类型的内建对象类显式地创建包装器对象，但是 `Number`、`String`、`Boolean` 这三个内建类为了兼容 `ES` 之前版本所以仍可以使用 `new` 关键字创建。如果需要显式创建一个 `Symol` 包装器对象，可以使用 `Object()` 函数创建：

~~~javascript
const sym = Symbol('foo');
console.log(typeof sym); // "symbol"
const symWrapper = Object(sym);
console.log(typeof symWrapper); // "object"
console.log(sym instanceof Symbol); // false
console.log(symWrapper instanceof Symbol); //true
~~~

### 创建全局共享的 `symbol`

使用 `Symbol.for(key)`  和 `Symbol.keyFor(sym)` 方法可以创建或获取一个全局共享的 `symbol` 值。

`Symbol.for(key)` 方法会根据给定的 `key`，来从运行时的 `symbol` 注册表中找到对于的 `symbol` 值并将其返回，如果不存在，则会创建一个与该 `key` 关联的 `symbol` 值，将其放入全局 `symbol` 注册表中，并将其返回。

`Symbol.keyFor(sym)` 方法可以用来返回全局 `symbol` 注册表中与传入的 `sym` 关联的 `key`，如果不存在，则返回 `undefined`。

### 内置 `symbol` 值

`ES6` 中，`JS` 内置了一些 `symbol` 值，它们代表了语言内部的一些行为，允许开发者自定义或扩展标准行为，见 [Symbol - JavaScript | MDN (mozilla.org)](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Symbol)。

## `Iterator`（迭代器）

### 迭代器协议

`JS` 中的迭代器（`Iterator`）是遵循迭代器协议的对象。迭代器需实现一个 `next()` 方法，每次调用 `next()` 方法时都会返回一个结果对象，该结果对象有两个属性，`value` 字段表示本次迭代的值，`done` 字段表示迭代是否结束。

使用 `ES5` 语法创建一个遵循迭代器协议的对象：

~~~javascript
function createIterator(items) {
    var i = 0;
    return {
        next: function() {
            var done = i >= item.length;
            var value = !done ? items[i++] : undefined;

            return {
                done: done,
                value: value
            };
        }
    };
}

// iterator 就是一个迭代器对象
var iterator = createIterator([1, 2, 3]);

console.log(iterator.next()); // { done: false, value: 1 }
console.log(iterator.next()); // { done: false, value: 2 }
console.log(iterator.next()); // { done: false, value: 3 }
console.log(iterator.next()); // { done: true, value: undefined }
~~~

### `ES6` 中部署迭代器

在 `ES6` 中，`Iterator` 是一个接口，一种数据结构只要部署了 `Iterator` 接口，那么这种数据结构就是可遍历的（`Iterable`），它可以被 `for...of` 遍历。默认的 `Iterator` 接口部署在数据结构的 `[Symbol.iterator]` 属性上， `Symbol.iterator` 是一个内置 `symbol` 值。

以下示例是实现一个简易的链表结构，并在其中实现 `Iterator` 接口，使得该链表结构中的数据可以被 `for...of` 遍历：

~~~javascript
class ListNode {
  next = null;
  constructor(value) {
    this.value = value;
  }
}

class LinkedList {
  head = null;


  addItem(value) {
    const listNode = new ListNode(value);
    if (!this.head) {
      this.head = listNode;
    } else {
      let current = this.head;
      while (current.next) {
        current = current.next;
      }
      current.next = listNode;
    }
    this.size++;
  }

  // 为 LinkedList 实现 Iterator 接口
  [Symbol.iterator]() {
    return {
      current: this.head,
      next() {
        const done = !this.current;
        const value = !done ? this.current.value : undefined;
        if (!done) {
          this.current = this.current.next;
        }··········
        return { done, value };
      },
    };
  }
}

const linkedList = new LinkedList();

linkedList.addItem("a");
linkedList.addItem("b");
linkedList.addItem("c");

for (const value of linkedList) {
  console.log(value);
}
~~~

### 内建迭代器

`ES6` 为 `Array`、`Map`、`Set` 集合内建了以下三种迭代器：

- `keys()`：返回一个迭代器对象，用来遍历所有的 `key`。对于数组来说，`key` 就是 `index`。
- `values()`：返回一个迭代器对象，用来遍历所有的 `value`。

- `entries()`：返回一个迭代器对象，用来遍历 `[key, value]` 组成的数组。

以 `Array` 类型为例：

~~~javascript
const colors = ["red", "green", "blue"];

for (const index of colors.keys()) {
    console.log(index);
}

// 0
// 1
// 2

for (const color of colors.values()) {
    console.log(color);
}

// red
// green
// blue

for (const item of colors.entries()) {
    console.log(item);
}

// [ 0, "red" ]
// [ 1, "green" ]
// [ 2, "blue" ]
~~~

`Map` 类型与 `Array` 类型相似，但对于 `Set` 类型来说，`keys()` 和 `values()` 返回的是相同的迭代器，意味着在 `Set` 中 `key` 与 `value` 是相同的：

~~~javascript
const colors = new Set(["red", "green", "blue"]);

for (const index of colors.keys()) {
    console.log(index);
}

// red
// green
// blue

for (const color of colors.values()) {
    console.log(color);
}

// red
// green
// blue

for (const item of colors.entries()) {
    console.log(item);
}

// [ "red", "red" ]
// [ "green", "green" ]
// [ "blue", "blue" ]
~~~

## `Generator`（生成器）

### 生成器函数

`function*` 这种声明方式会定义一个生成器函数（`generator function`），它返回一个 `Generator` 对象：

~~~javascript
function* generator(i) {
  yield i;
  yield i + 10;
}

const gen = generator(10);

console.log(gen.next().value); // 10

console.log(gen.next().value); // 20
~~~

不能使用箭头函数定义生成器函数。

生成器函数在执行时能暂停，后面又能从暂停处继续执行。

调用一个生成器函数并不会马上执行其中的语句，而是返回一个 `Generator`，它是 `Iterator` 的子类，因此也遵循迭代器协议。

`Generator` 实现了 `Iterable` 接口，调用其默认的 `Iterator` 时相当于调用了 `Generator` 自身。

~~~javascript
function* g(){}
console.log(g()) // g {<suspended>}
console.log(g()[Symbol.iterator]()) // // g {<suspended>}

// 需注意， Generator 是动态创建的，每次创建的都是不同的引用
console.log(g()[Symbol.iterator]() === g()) // false
console.log(g() === g()) // false
~~~

当 `Generator` 的 `next()` 方法被调用时，生成器函数中的语句会开始执行。执行生成器函数时，在遇到 `yield` 关键字时，执行会停止，函数作用域的状态会被保留。停止执行的生成器函数只能通过在生 `Generator` 上调用 `next() ` 方法来恢复执行。

#### `yield`

`yield` 关键字生成的值会出现在 `Generator` 调用 `next()` 方法返回的对象（以下称为 `IteratorResult`）中的 `value` 属性中。如果是通过 `yield` 关键字停止生成器函数，那么 `IteratorResult` 中的 `done` 会是 `false`。如果通过 `return` 关键字退出生成器函数，那么 `IteratorResult` 中的 `done` 会是 `true`。例：

~~~javascript
function* g() {
  yield "foo";
  yield "bar"
  return "baz";
}

const generator = g();

console.log(generator.next()); // { value: "foo", done: false }
console.log(generator.next()); // { value: "bar", done: false }
console.log(generator.next()); // { value: "baz", done: true }
~~~

生成器函数内部的执行流程会针对每个 `Generator` 区分作用域。在一个 `Generator` 调用 `next()` 不会影响其他 `Generator`：

~~~javascript
function* g() {
  yield "foo";
  yield "bar"
  return "baz";
}

const generator1 = g();
const generator2 = g();

console.log(generator1.next()); // { value: "foo", done: false }
console.log(generator2.next()); // { value: "foo", done: false }
~~~

`yield` 关键字类似于 `return` 关键字，只能在生成器函数内部使用，而无法在嵌套的非生成器函数中使用，否则会抛出语法错误：

~~~javascript
// 有效
function validGeneratorFn() {
  yield
}
  
// 无效
function invalidGeneratorFn() {
  function fn() {
    yield
  }
}
~~~

`yield` 关键字本身也可以作为值来使用，`Iterator` 调用 `next()` 时传递的第一值会被传递给 `yield`（除了第一次调用 `next()` 时）：

~~~javascript
function* g(initial) {
  console.log(initial)
  console.log(yield)
  console.log(yield)
}

const generator = g('foo') // 调用生成器函数时传入的第一个值，会传递给第一个 yield

console.log(generator.next('bar')) // 'foo'
console.log(generator.next('baz')) // 'baz'
console.log(generator.next('qux')) // 'qux'
~~~

`yield` 关键字也可以用来输出：

~~~javascript
function* g(initial) {
  return yield ‘foo’
}

const generator = g()

console.log(generator.next()) // { done: false, value: 'foo' }
console.log(generator.next('bar')) // { done: true, value: 'bar' }
~~~

#### `yield*`

可以使用 `yield*` 来增强 `yield` 的行为，让它能够迭代一个可迭代对象，从而一个个产出值，相当于把 `yield` 放进一个循环中：

~~~javascript
function* g1() {
  for (const x of [1, 2, 3]) {
    yield x
  }
}

function* g2() {
  yield* [1, 2, 3]
}

// g1 和 g2 等价
~~~

生成器函数自身也是可迭代对象，所以也可以用 `yield*` 迭代：

~~~javascript
function* g1() {
  yield 2;
  yield 3;
  yield 4;
}

function* g2() {
  yield 1;
  yield* g1();
  yield 5;
}

var iterator = g2();

console.log(iterator.next()); // { value: 1, done: false }
console.log(iterator.next()); // { value: 2, done: false }
console.log(iterator.next()); // { value: 3, done: false }
console.log(iterator.next()); // { value: 4, done: false }
console.log(iterator.next()); // { value: 5, done: false }
console.log(iterator.next()); // { value: undefined, done: true }
~~~

`yield*` 迭代各种可迭代对象：

~~~javascript
function* g() {
  yield* [1, 2];
  yield* "34";
  yield* arguments;
}

const iterator = g(5, 6);

console.log(iterator.next()); // { value: 1, done: false }
console.log(iterator.next()); // { value: 2, done: false }
console.log(iterator.next()); // { value: "3", done: false }
console.log(iterator.next()); // { value: "4", done: false }
console.log(iterator.next()); // { value: 5, done: false }
console.log(iterator.next()); // { value: 6, done: false }
console.log(iterator.next()); // { value: undefined, done: true }
~~~

`yield*` 表达式的值关联迭代器返回 `done: true` 时的 `value` 的值，对于大多数迭代器来说，这个值是 `undefined`，但是对于生成器函数产生的迭代器来说，这个值就是生成器函数的返回值：

~~~javascript
let result;

function* g1() {
  result = yield* g2();
}

function* g2() {
  yield 1;
  return "foo";
}

const iterator2 = g2();
console.log(iterator2.next()); // { value: 1, done: false }
console.log(iterator2.next()); // { value: "foo", done: true }

const iterator1 = g1();
console.log(iterator1.next()); // { value: 1, done: false }
console.log(iterator1.next()); // { value: undefined, done: true }

console.log(result); // "foo"
~~~

#### 异步生成器

生成器函数也可以是异步函数，将返回一个异步生成器（`AsyncGenerator`）对象，可用于 `for await...of` 循环：

~~~javascript
async function* foo() {
  yield await Promise.resolve('a');
  yield await Promise.resolve('b');
  yield await Promise.resolve('c');
}

let str = '';

async function generate() {
  for await (const val of foo()) {
    str = str + val;
  }
  console.log(str);
}

generate();
// Expected output: "abc"
~~~

可以通过设置一个对象的 `[symbol.asyncIterator]` 属性来自定义该对象的异步迭代器，使其成为一个异步可迭代对象：

~~~javascript
const myAsyncIterable = new Object();
myAsyncIterable[Symbol.asyncIterator] = async function* () {
  yield "hello";
  yield "async";
  yield "iteration!";
};

(async () => {
  for await (const x of myAsyncIterable) {
    console.log(x);
    // expected output:
    //    "hello"
    //    "async"
    //    "iteration!"
  }
})();
~~~

### `Generator` 实例

#### `Generator.prototype.next()`

`Generator` 实例的 `next()` 方法同 `Iterator`实例 的 `next()` 方法。

#### `Generator.prototype.return()`

`Generator` 实例的 `return()` 方法的作用类似于将 `return` 语句插入到生成器函数中的当前暂停位置，可传入一个可选参数 `value` 作为 `return` 的值，这会结束生成器：

~~~javascript
function* gen() {
  yield 1;
  yield 2;
  yield 3;
}

const g = gen();

console.log(g.next()); // { value: 1, done: false }
console.log(g.return("foo")); // { value: "foo", done: true }
console.log(g.next()); // { value: undefined, done: true }
~~~

对于已经完成的生成器，调用 `return()` 仍能使其返回 `value`，`done` 保持为 `true`：

~~~javascript
function* gen() {
  yield 1;
  yield 2;
  yield 3;
}

const g = gen();
console.log(g.next()); // { value: 1, done: false }
console.log(g.next()); // { value: 2, done: false }
console.log(g.next()); // { value: 3, done: false }
console.log(g.next()); // { value: undefined, done: true }
console.log(g.return()); // { value: undefined, done: true }
console.log(g.return(1)); // { value: 1, done: true }
~~~

由于  `try...finally` 语句执行时，其中的 `finally`  块始终会被执行，因此如果当生成器调用 `return()` 时，生成器函数正执行到 `try` 块中，生成器函数会继续执行到 `finally` 块：

~~~javascript
function* gen() {
  try {
    yield 1;
    yield 2;
  } finally {
    yield "cleanup";
  }
}

const g = gen();

console.log(g.next()); // { value: 1, done: false }

// 在 try 块中时调用 return()，会继续执行到 finally 块
console.log(g.return("early return")); // { value: 'cleanup', done: false }

// return() 的完成值会被保留到下一次 next()
console.log(g.next()); // { value: 'early return', done: true }
~~~

如果 `finally` 块中直接使用了 `return`，那么执行到 `finally` 块中时会直接结束生成器：

~~~javascript
function* gen() {
  try {
    yield 1;
  } finally {
    return "cleanup";
  }
}

const g = gen();
console.log(g.next()); // { value: 1, done: false }
gconsole.log(g.return("early return")); // { value: 'cleanup', done: true }
~~~

#### `Generator.prototype.throw()`

`Generator` 实例的 `throw()` 方法的作用类似于将 `throw` 语句插入到生成器函数中的当前暂停位置，传入要抛出的异常 `exception` 作为参数，抛出的错误可以被 `try...catch` 块捕获：

~~~javascript
function* gen() {
  try {
    yield 1;
  } catch (e) {
    console.log("捕获到错误！");
  }
  yield 2;
}

const g = gen();

console.log(g.next()); // { value: 1, done: false }

console.log(g.throw(new Error("出现了些问题")));
// 捕获到错误！
// { value: 2, done: false }

console.log(g.next()); // { value: undefined, done: true }
~~~
