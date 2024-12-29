流是用于在 `Node.js` 中处理流数据的抽象接口。 `stream` 模块提供了用于实现流接口的 `API`。

## 流的类型

`Node.js` 中有四种基本的流类型：

- `Writable`： 可写流。例如 `fs.createWriteStream()`。
- `Readable`：可读流。例如 `fs.createReadStream()`。
- `Duplex`：`Readable` 和 `Writable` 的流。例如，`net.Socket`。
- `Transform`：可以在写入和读取数据时修改或转换数据的 `Duplex` 流。例如 `zlib.createDeflate()`。

所有类型的流都是 `EventEmitter` 的实例，它们都实现了 `EventEmitter` 接口。

## 流的 `Promise API`

`stream/promise` 提供的的 `API` 创建的流将返回 `Promise` 对象。这些 `API` 可以通过 `require('node:stram/promises')` 或 `require('node:stream').promises` 访问。

## 对象模式

通常，`Node.js` `API` 创建的所有流都只对字符串和 `Buffer`（或 `Uint8Array`）对象进行操作。当流在对象模式下运行时，流可以处理任何 `JavaScript` 对象（除了 `null` 值，它在流中具有特殊用途）。

如果需要使用对象模式的流，需要在创建流时使用 `objectMode` 选项切换到对象模式，而不要将现有的流切换到对象模式。

## `Writable`

[stream 流 | Node.js API 文档 (nodejs.com.cn)](https://www.nodejs.com.cn/api-v14/stream.html#writable-streams)

可写流是数据写入目标的抽象。

所有的 `Writable` 流都实现了 `stream.Writable` 类定义的接口。

虽然 `Writable` 流的特定实例可能会有各种不同之处，但是所有的 `Writable` 流都遵循相同的基本使用模式，如：

~~~javascript
const myStream = getWritableStreamSomehow();
myStream.write('some data');
myStream.write('some more data');
myStream.end('done writing data');
~~~

### 相关事件

#### `'finish'` 事件

调用 `stream.end() ` 方法之后，且所有数据以及刷新到底层系统，则触发 `'finish'` 事件。

#### `'close'` 事件

当流及其任何底层资源（例如文件描述符）已关闭时，则会触发 `'close'` 事件。 该事件表明将不再触发更多事件，并且不会发生进一步的计算。

如果 `Writable` 流是使用 `emitClose` 选项创建的，则始终会触发 `'close'` 事件。

#### `'error'` 事件

回调中接收一个 `Error` 参数。

如果在写入或 `pipe` 数据时发送错误，则会触发 `'error'` 事件。

触发 `'error'` 事件时将关闭流，除非创建流时将 `autoDestroy` 选项设置为 `false`。

在 `'error'` 之后，不应触发除 `'close'` 之外的其他事件（包括 `'error'` 事件）。

#### `'pipe'` 事件

回调中接收一个 `src` 参数，是一个 `Readable`，表示 `pipe` 到此可写流的源流。

当在 `Readable` 上调用 `stream.pipe()`  方法将此 `Writable` 添加到其目标集时，触发 `'pipe'` 事件。

示例：

> ~~~javascript
> const writer = getWritableStreamSomehow();
> const reader = getReadableStreamSomehow();
> writer.on('pipe', (src) => {
>   console.log('Something is piping into the writer.');
>   assert.equal(src, reader);
> });
> reader.pipe(writer);
> ~~~

#### `'unpipe'` 事件

回调中接收一个 `src` 参数，表示被取消的源流。

当在 `Readable` 上调用 `stream.unpipe()`  从目标集合中删除此 `Writable` 时，触发 `'unpipe'` 事件。

示例：

> ~~~javascript
> const writer = getWritableStreamSomehow();
> const reader = getReadableStreamSomehow();
> writer.on('unpipe', (src) => {
>   console.log('Something has stopped piping into the writer.');
>   assert.equal(src, reader);
> });
> reader.pipe(writer);
> reader.unpipe(writer);
> ~~~

#### `'drain'` 事件

当 `Writable` 流的内部缓冲区被填满到超出 `highWaterMark` 设置的阈值时，`write()` 方法会返回 `false`。此时应该停止向 `Writable` 中继续写入数据。一旦缓冲区中的数据流减少并低于 `highWaterMark`，`Writable` 就会触发 `'drain'` 事件，表明现在可以安全地写入更多的数据。

示例：

> ~~~javascript
> function writeData(writer, data, encoding, callback) {
>     let i = 1000000;
>     write();
>     function write() {
>         let ok = true;
>         do {
>             i--;
>             if (i === 0) {
>                 // 最后一次写入
>                 writer.write(data, encoding, callback);
>             } else {
>                 // 检查是否应该继续写入，还是等待
>                 // 不要传入回调，因为我们还没有完成
>                 ok = writer.write(data, encoding);
>             }
>         } while (i > 0 && ok);
>         if (i > 0) {
>             // 必须提前停止写入
>             // 当缓冲区排空时再写入更多数据
>             writer.once('drain', write);
>         }
>     }
> }
> ~~~
>
> 上述代码中，如果 `writer.write()` 返回 `false`，就停止写入数据，直到 `'drain'` 事件的触发后继续写入剩余的数据。这样可以确保不会因为写入过多的数据而耗尽系统资源。

### 常用方法

#### `write()`

`writable.write(chunk[, encoding][, callback])` 方法将一些数据写入流，并在该数据块完全处理后调用提供的 `callback`。

#### `end()`

`writable.end([chunk[, encoding]][, callback])` 方法表示不再有数据写入 `Writable`。

可选的 `chunk` 和 `encoding` 参数允许关闭流之前写入最后一个额外的数据块。如果提供，则可选的 `ballback` 函数被绑定为 `'finish'` 和 `'error'` 事件的监听器。

#### `destroy()`

方法用于销毁流。如果传入一个 `Error` 对象，可还会触发 `'error'` 事件一定会触发 `'close'` 事件（除非 `emitClose` 设置为 `false`）。流销毁后，`writable.destroyed` 属性的值会转变为 `true`。

## `Readable`

[stream 流 | Node.js API 文档 (nodejs.com.cn)](https://www.nodejs.com.cn/api-v14/stream.html#writable-streams)

### 暂停与流动

`Readable` 流有两种读取模式：

- 流动模式：数据会自动从底层系统读取，并通过 `EventEmitter` 接口使用事件尽快提供给应用程序。流动模式下会自动调用 `stream.read()` 方法。
- 暂停模式：必须显式地调用 `stream.read()` 方法从流中读取数据块。

`Readable` 流被创建时默认处于暂停模式，但可以通过以下方式之一切换到流动模式：

- 添加 `'data'` 事件的处理函数。

  示例：

  ~~~javascript
  const fs = require('fs');
  const readStream = fs.createReadStream('file.txt');
  
  readStream.on('data', (chunk) => {
    console.log(`Received ${chunk.length} bytes of data.`);
  });
  ~~~

  在这个例子中，创建了一个指向 `file.txt` 文件的 `Readable` 流。此时 `readStream` 会以流动模式开启并从 `file.txt` 中读取数据。当有数据可用时，`'data'` 事件就会触发，并将数据块传递给回调函数。

- 调用 `stream.resume()` 方法。

  示例：

  ~~~javascript
  const fs = require('fs');
  const readStream = fs.createReadStream('file.txt');
  
  readStream.on('readable', () => {
    console.log('Stream is readable (not paused)');
  });
  
  readStream.resume();
  ~~~

  在这个例子中，当 `readStream.resume()` 调用后，`readStream` 便会开始从 `file.txt` 中读取数据，并触发 `'readable'` 事件。

- 调用 `stream.pipe()`方法将数据发送到 `Writable`。

  示例：

  ~~~javascript
  const fs = require('fs');
  const readStream = fs.createReadStream('file.txt');
  const writeStream = fs.createWriteStream('output.txt');
  
  readStream.pipe(writeStream);
  ~~~

  在这个例子中，创建了一个 `Readable` 流和一个 `Writable` 流，然后使用 `pipe()` 方法将 `Readable` 流的数据发送到 `Writable` 流。这将自动将 `Readable` 流切换到流动模式，并且数据将被连续地写入 `‘output.txt’` 文件，直到 `Readable` 流结束为止。

处于流动模式的 `Readable` 可以使用以下方式之一切换回暂停模式：

- 如果没有 `pipe` 目标，则可以调用 `stream.pause()` 方法。

  该方式对应添加了 `'data'` 事件句柄或使用了 `stream.resume()` 方法的 `Readable`。注意删除 `'data'` 事件句柄并不会自动暂停流。

- 如果有 `pipe` 目标，则通过 `stream.unpipe()` 方法删除管道目标。

- 产生背压（`backpressure`）。

每个 `Readable` 都处于三种可能的状态之一：

- `readable.readableFlowing === null`：

  这种状态下，流不会产生数据。

  此时如果为 `'data'` 事件绑定监听器，或调用 `readable.resume()` 或 `readable.pipe()` 方法都会将 `readable.readableFlowing` 的值转换为 `true`。

- `readable.readableFlowing === true`

  这种状态下，流会生成数据并主动触发事件。

  此时如果调用让流处于暂停模式，则会将 `readable.readableFlowing` 的值转换为 `false`。

- `readable.readableFlowing === false`

  这种状态下，流不会触发事件，但是数据可能还是会继续生成，在流的内部缓冲区中累积。

  此时如果为 `'data'` 事件绑定监听器不会改变 `readable.readableFlowing` 的值。

  示例：

  ~~~javascript
  const { PassThrough, Writable } = require('node:stream');
  const pass = new PassThrough();
  const writable = new Writable();
  
  pass.pipe(writable);
  pass.unpipe(writable);
  // readableFlowing 现在为 false。
  
  pass.on('data', (chunk) => { console.log(chunk.toString()); }); // 此时添加 'data' 句柄不会将 readableFlowing 转变为 true。
  pass.write('ok');  // 不会触发 'data'。
  pass.resume();     // 将 readableFlowing 转变为 true，使流触发 'data'。
  ~~~

### 相关事件

#### `'data'` 事件

回调接收 `chunk` 作为参数。对于不在对象模式下操作的流，`chunk` 可以传递字符串、 `Buffer`、`Uint8Array`。对于处于对象模式下操作的流，`chunk` 可以传递除了 `null` 之外的任何 `JavaScript` 值。

每当 `Readable` 将数据块提供给 `Comsumer` ，即每当调用一次 `readable.read()` 并成功读取到数据块时，都会触发 `'data'` 事件。流动模式下会自动调用 `readable.read()`，因此也会触发 `'data'` 事件。

对于不在对象模式下的流，如果使用 `readable.setEncoding()` 方法为流指定了默认编码，则监听器回调将把数据块作为字符串传入，否则数据将作为 `Buffer` 传入。

#### `'end'` 事件

当流中没有更多数据可供消费时，则会触发 `'end'` 事件。

#### `'close'` 事件

当流及其任何底层资源（例如文件描述符）已关闭时，则会触发 `'close'` 事件。 该事件表明将不再触发更多事件，并且不会发生进一步的计算。

如果 `Readable` 流是使用 `emitClose` 选项创建的，则始终会触发 `'close'` 事件。

#### `'error'` 事件

回调接收单个 `Error` 对象。

`'error'` 事件可以随时由 `Readable` 的实现触发。 通常，如果底层流由于底层内部故障而无法生成数据，或者当流实现尝试推送无效数据块时，可能会发生这种情况。

#### `'pause'` 事件

但 `readableFlowing` 为 `true` 时使用 `stream.pause()` 方法，将其转变为 `false`，则会触发 `'pause'` 事件。

#### `'resume'` 事件

当调用  `stream.pause()` 方法使得 `readableFlowing` 从其他值转变为 `true` 时，则会触发 `'resume'` 事件。

#### `'readable'` 事件

当有可从流中读取的数据或已到达流的末尾时，则将触发 `'readable'` 事件。

 实际上，`'readable'` 事件表明流有新的信息。 如果数据可用，则 `stream.read()` 将返回该数据。

### 常用方法

#### `pause()`

`readable.pause()` 方法将导致处于流动模式的流停止触发 [`'data'`](https://www.nodejs.com.cn/api/stream.html#event-data) 事件，切换出流动模式。 任何可用的数据都将保留在内部缓冲区中。

#### `pipe()`

`readable.pipe(destination[, options])` 方法将 `Writable` 流绑定到 `readable`，使其自动切换到流动模式并将其所有数据推送到绑定的 `Writable`。 数据流将被自动管理，以便目标 `Writable` 流不会被更快的 `Readable` 流漫过。

第一个参数 `destination` 时传入要接收数据的 `Writable`。且该方法还会将它作为返回值返回，使得如果目标流是可读可写流，可由链式调用实现管道链。

示例：

~~~javascript
const fs = require('node:fs');
const readable = getReadableStreamSomehow();
const writable = fs.createWriteStream('file.txt');
// 可读流的所有数据进入 'file.txt'。
readable.pipe(writable);
~~~

默认情况下，当源 `Readable` 流触发 [`'end'`](https://www.nodejs.com.cn/api/stream.html#event-end) 时，则在目标 `Writable` 流上调用 [`stream.end()`](https://www.nodejs.com.cn/api/stream.html#writableendchunk-encoding-callback)，因此目标不再可写。 要禁用此默认行为，可以将 `end` 选项作为 `false` 传入，从而使目标流保持打开状态：

~~~javascript
reader.pipe(writer, { end: false });
reader.on('end', () => {
  writer.end('Goodbye\n');
});
~~~

#### `unpipe()`

`readable.unpipe([destination])` 方法分离先前使用 `stream.pipe()` 方法绑定的 `Writable` 流。

可选的 `destination` 是要指定取消 `pipe` 的特定流：

- 如果未指定 `destination`，则所有 `pipe` 都将分离。
- 如果指定的 `destination` 没有被设置 `pipe`，则该方法不执行任何操作。

#### `read()`

`readable.read([size])`  方法从内部缓冲区中读取数据并返回。 如果没有数据可以读取，则返回 `null`。

可选的 `size` 参数指定要读取的特定字节数。 如果无法读取 `size` 字节，则将返回 `null`，除非流已结束，在这种情况下，将返回内部缓冲区中剩余的所有数据。如果未指定 `size` 参数，则将返回内部缓冲区中包含的所有数据。`size` 参数必须小于或等于 `1 GiB`。

`readable.read()` 方法应该只在暂停模式下操作的 `Readable` 流上调用。 在流动模式下，会自动调用 `readable.read()`，直到内部缓冲区完全排空。

#### `destroy()`

同 `writable.destroy([error])`。

#### `setEncoding()`

`readable.setEncoding(encoding)` 方法为从 `Readable` 流读取的数据设置字符编码。

默认情况下，没有分配编码，流数据将作为 `Buffer` 对象返回。 设置编码会导致流数据作为指定编码的字符串而不是 `Buffer` 对象返回。 例如，调用 `readable.setEncoding('utf8')` 将导致输出数据被解释为 `UTF-8` 数据，并作为字符串传入。 调用 `readable.setEncoding('hex')` 将使数据以十六进制字符串格式进行编码。