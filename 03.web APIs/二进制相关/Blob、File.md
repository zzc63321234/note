## `Blob`

`Blob` 对象表示一个不可变、原始数据的类文件对象。它的数据可以按文本或二进制的格式进行读取，也可以转换成 `ReadalbeStream` 来用于数据操作。

`Blob` 实例拥有两个属性：

- `size`：`Blob` 对象所包含数据的字节数。
- `type`：包含文件 `MIME` 类型的字符串，如果无法确定类型则返回空字符串。

`Blob.prototype.slice()` 方法会截取 `Blob` 中的数据，并为其创建一个新的 `Blob` 对象以返回，它接收三个可选的参数：

- `start`

  代表该 `Blob` 中截取的起始字节的索引。如果传入一个负数，则会作为偏移量从数据的末尾往前开始计算。默认为 `0`。

- `end`

  代表该 `Blob` 中截取的结束字节的后一位的索引（该索引的字节不会被截取）。如果传入一个负数，也一样表示偏移量。默认为该 `Blob` 的原始长度 `size`。

- `contentType`

  给截取的 `Blob` 赋予一个新的内容类型，它会被作为截取的 `Blob` 的 `type` 属性。默认为一个空字符串。

`Blob.prototype.arrayBuffer()` 方法返回一个 `Promise`，其会兑现一个包含 `blob` 二进制数据内容的 `ArrayBuffer`。

`Blob.prototype.stream()` 方法返回一个 `ReadableStream` 对象，读取它将返回包含在 `Blob` 中的数据。

`Blob.prototype.text()` 方法返回一个 `Promise`，其会兑现一个包含 `blob` 内容的 `UTF-8` 格式的字符串。

## `File`

`File` 继承自 `Blob`。浏览器端用户在 `input` 元素上选择文件后返回的 `FileList` 数组中的元素就是 `File` 对象。相比普通 `Blob`，`File` 实例多出了以下属性：

- `lastModifield`：返回当前 `File` 对象所引用文件的最后修改时间，表示为 `13` 位时间戳。
- `lastModifiedDate`：返回当前 `File` 对象所引用文件最后修改时间的 `Date` 对象。
- `name`：返回当前 `File` 对象所引用文件的名字。
- `webkitRelativePath`：返回 `File` 相关的 `path` 或 `URL`。