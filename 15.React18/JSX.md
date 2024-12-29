## 简介

- `JSX` 是一种 `JavaScript` 语法扩展
- `JSX` 全称有说是 `JavaScript XML`，有说是 `JavaScript extention`
- `JSX` 用于描述 `UI` 界面，允许在 `JavaScript` 代码中编写类似 `HTML` 的标记语言
- `JSX` 在编译阶段会被转换为普通的 `JavaScript` 代码

## 语法

### 书写规范

- `JSX` 的顶层只能有一个根元素
  - 很多时候会在外层包裹一个 `div` 元素作为根元素
- 为方便阅读，通常在 `JSX` 的外层包裹一个小括号，也使得 `jsx` 可以换行书写
- `JSX` 中的标签可以是单标签，也可以是双标签
  - 不同于 `HTML`，在 `JSX` 中如果使用单标签，不能省略结束符号

### 使用注释

- 在 `JSX` 的扩展语法中，无法直接使用 `JS` 中的注释写法，也无法使用 `HTML` 的注释写法

- `JSX` 中的注释写法示例

  ~~~jsx
  /* 在非扩展语法中注释写法与 JS 相同 */
  // 也可以使用单行注释
  const jsx = (
    <div>
      { /* JSX 扩展语法中的注释写法，只能使用块状注释 */ }
      <h2>Hello JSX</h2>
    </div>
  )
  ~~~

### 嵌入表达式

- `JSX` 扩展语法中可以使用 `{}` 嵌入 `JS` 表达式作为子元素
  - 可以作为标签体嵌入
  - 可以在标签属性中作为属性值嵌入
    - 作为标签属性值嵌入时，外面不加引号
- 表达式的值类型不同时，会有不同的解析
  - `number`、`string` 类型
    - 直接显示
  - `null`、`undefined`、`boolean` 类型
    - 内容为空，等同于空串
  - `object` 类型（`array` 除外）
    - 报错：`Objects are not valid as a React child`
  - `array` 类型
    - 会将内容按顺序直接拼接成一个字符串显示
    - 连接方式
      - 作为标签体时，连接符为空串，即没有连接符
      - 作为标签属性时，连接符为 `,`
    - 数组中如果还存在数组，也会将其进行拼接
    - 如果数组中存在 `object` 类型会报错

### `class` 与 `style` 绑定

- `class` 绑定
  
  - `class` 与 `className`
    - 由于 `JSX` 是 `JS` 语法的扩展，而 `class` 在 `JS` 中是关键字
    - 因此推荐使用 `className` 作为标签的类名
  - 如果元素存在多个 `class`，并且需要绑定数据
    - 可以将其存储到一个 `JS` 数组中，变量名可以使用 `classList`
    - 之后嵌入 `classList.join(' ')`
  - 也可以用一些第三方库处理 `className`，如 `classnames` 库
  
- `style` 绑定

  - `JSX` 中不支持 `HTML` 中的内联样式表示方式，即不支持用字符串的形式表示内联样式

  - `JSX` 中需要使用对象的形式表达内联样式

    - 示例

      ~~~jsx
      const jsx = (
        <div>
          <h2 style={{ color: 'red', fontSize: 30 }}>Hello JSX</h2>
        </div>
      )
      ~~~

    - 其中的双大括号并不是 `Vue` 模板中的 `mustache` 语法

      - 外侧的大括号仍是内嵌表达式的形式
      - 内测的大括号表示这是一个样式对象

    - `{ font-size: 30 }` 中的键值对分别使用以下任意方式表示皆可

      - `{ 'font-size': 30 }`
        - 如值为数字，则其单位会被解析为 `px`
      - `{ fontSize: '30px' }`

## `JSX` 的本质

- `JSX` 扩展语法本质上是 `React.createElement(type[, config][, ...children])` 函数的语法糖
  - `JSX` 扩展语法最终会被转换成 `React.createElement` 的函数调用
  - `React.createElement`
    - 接收三个参数
      - `type`
        - 当前 `ReactElement` 的类型
        - 如果是标签元素，就用字符串表示标签名
        - 如果是组件元素，则用字符串表示组件名
      - `config`（可选）
        - 一个对象，包含该元素具有的属性
        - 包括
          - 一般的 `HTML Attributes`
          - 事件处理函数
          - 自定义属性
          - 特殊的 `React` 属性，如 `className`、`key`、`ref` 等
      - `children`（可选）
        - 一个数组，包含该 `ReactElement` 的子元素
        - 可以是
          - 基本类型，如字符串或数字
          - `ReactElement`
          - `React` 组件
    - 返回一个 `ReactElement`，即 `React` 中的虚拟 `DOM` （`Virtual DOM`）
