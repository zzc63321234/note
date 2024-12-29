## `encodeURI()` / `encodeURIComponent()`

`encodeURI()` / `encodeURIComponent()` 函数用于对 `URI` 的组成部分进行编码。该函数会将传入的字符串中除了特定字符之外的所有字符进行编码，替换为转义序列。

这两种方法的区别在于不转义的字符不同：

- 都不转义的字符：

  `A-Z a-z 0-9 - _ . ! ~ * ' ( )`

- 除此之外，`encodeURI()` 对于以下字符也不会转义：

  `; , / ? : @ & = + $ #`

`encodeURI()` 自身*无法*产生能适用于 `HTTP` `GET` 或 `POST` 请求的 `URI`。例如对于 `XMLHTTPRequests`，因为 `encodeURI()`  不会对 `&`、`+`、`=`  进行编码，而在 `GET` 和 `POST` 请求中它们会作为特殊字符。而 `encodeURIComponent()` 会对这些字符进行编码。

使用示例：

~~~javascript
var set1 = ";,/?:@&=+$"; // 保留字符
var set2 = "-_.!~*'()"; // 不转义字符
var set3 = "#"; // 数字标志
var set4 = "ABC abc 123"; // 字母数字字符和空格

console.log(encodeURI(set1)); // ;,/?:@&=+$
console.log(encodeURI(set2)); // -_.!~*'()
console.log(encodeURI(set3)); // #
console.log(encodeURI(set4)); // ABC%20abc%20123 (空格被编码为 %20)

console.log(encodeURIComponent(set1)); // %3B%2C%2F%3F%3A%40%26%3D%2B%24
console.log(encodeURIComponent(set2)); // -_.!~*'()
console.log(encodeURIComponent(set3)); // %23
console.log(encodeURIComponent(set4)); // ABC%20abc%20123 (空格被编码为 %20)
~~~

