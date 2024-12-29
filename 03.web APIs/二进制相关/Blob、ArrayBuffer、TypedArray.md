## 区别

`ArrayBuffer` 代表**内存**中的一段二进制数据，不可以直接读写，但可以通过创建 `TypedArray` 视图或 `DataView` 视图来操作这段二进制数据。`TypedArray` 视图主要用来操作简单类型的二进制数据，`DataView` 用来操作复杂类型的二进制数据。

`Blob` 中的数据不一定在内存中，还可以存储在硬盘或其他存储媒介上，因此更适合大块的二进制数据的处理。其数据是不可变的，只能通过 `Blob.prototype.arrayBuffer()` 为其创建 `ArrayBuffer` 后再通过视图来修改 `ArrayBuffer` 中的数据。

## `ArrayBuffer`

https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer

创建一个 `length` 长度的 `ArrayBuffer`：

~~~javascript
new ArrayBuffer(length)
~~~

常用实例属性和方法：

- `bytelength`：获取 `ArrayBuffer` 的长度。
- `resize(length)`：调整 `ArrayBuffer` 的长度。
- `slice(start[, end])`：截取一部分内容并创建一个新的 `ArrayBuffer` 返回。

静态方法：

- `ArrayBuffer.isView(value)`：传入一个值，判断它是不是 `ArrayBuffer` 视图（如 `TypedArray` 或 `DataView`）。

## `SharedArrayBuffer`

https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer

`SharedArrayBuffer` 类似 `ArrayBuffer`，也可以被结构化克隆（可以被 `postMessage()` 传输），但它不是可转移对象（不需要转移），在多个线程中传输时，不同线程中它们将指向同一个内存数据块。 

## `TypedArray`

https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/TypedArray

`TypedArray` 是 `ArrayBuffer` 的一种视图。

`TypedArray` 是一种**抽象类**，并不存在直接暴露的 `TypedArray` 构造函数，它会作为所有 `TypedArray` 子类的通用父类而存在，通过子类的构造函数在创建实例。

其子类如下：

| 类型                                                         | 值范围                                             | 字节大小 | 描述                                                        | `Web IDL` 类型        | 等价的 C 类型                   |
| :----------------------------------------------------------- | :------------------------------------------------- | :------- | :---------------------------------------------------------- | :-------------------- | :------------------------------ |
| [`Int8Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Int8Array) | -128 到 127                                        | 1        | 8 位有符号整型（补码）                                      | `byte`                | `int8_t`                        |
| [`Uint8Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array) | 0 到 255                                           | 1        | 8 位无符号整型                                              | `octet`               | `uint8_t`                       |
| [`Uint8ClampedArray`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Uint8ClampedArray) | 0 到 255                                           | 1        | 8 位无符号整型（一定在 0 到 255 之间）                      | `octet`               | `uint8_t`                       |
| [`Int16Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Int16Array) | -32768 到 32767                                    | 2        | 16 位有符号整型（补码）                                     | `short`               | `int16_t`                       |
| [`Uint16Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Uint16Array) | 0 到 65535                                         | 2        | 16 位无符号整型                                             | `unsigned short`      | `uint16_t`                      |
| [`Int32Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Int32Array) | -2147483648 到 2147483647                          | 4        | 32 位有符号整型（补码）                                     | `long`                | `int32_t`                       |
| [`Uint32Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Uint32Array) | 0 到 4294967295                                    | 4        | 32 位无符号整型                                             | `unsigned long`       | `uint32_t`                      |
| [`Float32Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Float32Array) | `-3.4E38` 到 `3.4E38` 并且 `1.2E-38` 是最小的正数  | 4        | 32 位 IEEE 浮点数（7 位有效数字，例如 `1.234567`）          | `unrestricted float`  | `float`                         |
| [`Float64Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Float64Array) | `-1.8E308` 到 `1.8E308` 并且 `5E-324` 是最小的正数 | 8        | 64 位 IEEE 浮点数（16 位有效数字，例如 `1.23456789012345`） | `unrestricted double` | `double`                        |
| [`BigInt64Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/BigInt64Array) | -263 到 263 - 1                                    | 8        | 64 位有符号整型（补码）                                     | `bigint`              | `int64_t (signed long long)`    |
| [`BigUint64Array`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/BigUint64Array) | 0 到 264 - 1                                       | 8        | 64 位无符号整型                                             | `bigint`              | `uint64_t (unsigned long long)` |