- [[设计 | Element Plus (gitee.io)](https://element-plus.gitee.io/zh-CN/guide/design.html)](https://element-plus.gitee.io/zh-CN/guide/installation.html)

## `Form` 表单

### 如何对表单进行校验

通过 `ref` 获取 `el-form` 组件的实例，通过它的 `validate` 方法来进行一些表单校验之后的操作。

`validate` 方法接收一个回调函数作为参数，该回调函数：

- 会在表单校验完成后被调用
- 接收一个布尔值参数 `valid` 表示校验是否成功

## `Dropdown` 下拉菜单

如果 `el-dropdown` 组件的非具名插槽有标签元素的情况下，有时在它 `focus` 状态下 `hover` 时会出现黑色边框，可以将该元素的 `outline` 样式指定为 `none` ，或者全局添加以下 `CSS` 样式：

~~~css
.el-tooltip__trigger:focus-visible {
  outline: unset;
}
~~~

