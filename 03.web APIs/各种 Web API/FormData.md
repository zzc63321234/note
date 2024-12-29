`FormData` 接口提供了一种表示表单数据的键值对 `key/value` 的构造方式，并且可以轻松地将数据通过 `XMLHttpRequest.send()` 方法发送出去。如果发送出去的请求头中设置编码类型为 `multipart.form-data`，那么它会使用和 `HTML` 表单一样的格式。

实现了 `FormData` 接口实现了迭代器，可以直接用 `for...of` 遍历，且支持使用内建迭代器分别迭代其键和值。

`FormData` 是一个构造函数，使用时可直接 `new` 它以创建实例。

方法：

- `FormData.set()`、`FormData.get()`、`FormData.append()`、`FormData.getAll()`：

  如果遍历 `FormData`，会发现其每一项都是一个 `[key, value]` 形式的数组。

  使用 `FormData.set(key, value)` 可以设置一组键值对，如果 `key` 已存在（且无论存在多少个重复的），则会将其覆盖，只保留当前设置的这个键值对。

  使用 `FormData.append(key, value)` 也是设置一组键值对，即使 `key` 已存在，也会继续追加键值对，不会覆盖。

  使用 `FormData.get(key)` 会返回 `FormData` 中第一个匹配该 `key` 的 `value`。

  使用 `FormData.getAll(key)` 会将 `FormData` 中匹配该 `key` 的所有 `value` 放入数组中返回。

  一般来说，`set()` 和 `get()` 会在传输单值时组合使用，`append()` 和 `getAll()` 会在传输多值时组合使用。

- `FormData.has(key)` 会返回一个布尔值，表示该 `FormData` 中是否含有某个 `key`。
- `FormData.delete(key)` 会将 `FormData` 中所有匹配该 `key` 的 `value` 都删除。