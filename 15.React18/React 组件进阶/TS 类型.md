使用 `create-react-app` 创建项目时，可以指定 `typescript` 模板：

~~~bash
$ npx create-react-app --template typescript project-name
~~~

## `ReactNode` 和 `ReactElement`

在函数式组件中返回一个 `jsx`，会发现类型提示会自动推导其返回值类型是 `JSX.Element`。

`JSX.Element` 的类型定义：

~~~typescript
interface Element extends React.ReactElement<any, any>
~~~

也就是说，`JSX.Element` 实际上就是 `React.ReactElement` 类型，如果需要描述一个 `jsx` 的类型，可以直接使用 `React.ReactElement`。

`ReactNode` 是 `React` 节点的类型，它可以是 `number`、`string`、`null`、`boolean` 等可以包含 `ReactElement` 中的类型，也可以是 `ReactElement` 本身。

## `FunctionComponent`

函数组件的类型是 `FunctionComponent`，可以传入一个泛型参数作为它接收的 `props` 的类型，返回一个 `ReactElement`。

## `Hooks` 中的类型

### `useRef`

`useRef()` 调用时，泛型中传入要引用的值的类型。

如果它是用来保存 `dom` 对象的，泛型中传入 `HTMLElement` 类型，且需要传入 `null` 作为参数。这样返回的对象就会是 `React.RefObject` 类型，它的 `current` 属性是只读的，这样引用的 `dom` 对象就无法被替换。

如果没有传入 `null`，那么返回的对象就会是 `React.MutableRefObject` 类型，可以修改它的 `current` 属性的值。

## `props` 类型

如果希望组件可以接收很多 `html` 属性作为参数，可以将其 `props` 的类型继承 `HTMLAttributes`。`HTMLAttributes` 中只有 `html` 的通用属性，如果希望包含一些特定标签特有的属性，可以在泛型中指定，如： `HTMLAttributes<FormHTMLAttributes>`

也可以继承自 `ComponentProps`，可以传入标签名作为泛型参数，如 `ComponentProps<'form'>`。

## `EventHandler` 类型

如果组件需要传入一些事件处理函数，这些事件处理函数可以使用 `xxxEventHandler` 类型。，比如 `MouseEventHandler`、`ChangeEventHandler` 等，它的泛型参数是事件元素的类型。
