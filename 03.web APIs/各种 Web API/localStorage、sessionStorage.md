- `localStorage` 和 `sessionStorage` 都是在用户浏览器中存储属性的方式 
- `localStorage` 与 `sessionStorage` 区别在于：
  - 存储在 `localStorage` 的数据可以长期保留
  - 存储在 `sessionStorage` 的数据会在页面会话结束（一般是页面关闭时）被清除
- `localStorage` 中的键值对总是以字符串的形式存储
- 方法
  - `setItem(key, value)`
    - 新增一个数据项
  - `getItem(key)`
    - 读取一个数据项
  - `removeItem(key)`
    - 移除一个数据项
  - `clear()`
    - 移除所有的数据项