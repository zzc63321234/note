安装：

~~~bash
$ npm i zustand
~~~

## 基本使用

创建的 `store` 是一个 `hook`。

可以在 `store` 中存放任何东西，如：基本类型值、对象、函数。

遵循不可变数据原则，状态必须不可变地更新。

`set` 函数可以实现状态更新。

创建 `store` 示例：

~~~typescript
import { create } from 'zustand'

const useBearStore = create((set) => ({
  bears: 0,
  increasePopulation: () => set((state) => ({ bears: state.bears + 1 })),
  removeAllBears: () => set({ bears: 0 }),
}))
~~~

获取 `store` 中的数据示例：

~~~typescript
const bears = useBearStore((state) => state.bears)
const increasePopulation = useBearStore((state) => state.increasePopulation)
~~~

## 更新机制

当运行 `set` 函数时，可以触发 `React` 的更新。

### `Selector`

在 `store hook` 中传入 `selector` 可以进行状态选择，使用 `selector` 可以控制 `zustand` 何时触发 `React` 的 更新。

使用以下方式使用 `selecctor` 可以取出 `store` 中的单个值：

> ~~~typescript
> const nuts = useBearStore((state) => state.nuts)
> const honey = useBearStore((state) => state.honey)
> ~~~
>
> 如果使用 `set` 函数修改了状态，`zustand` 会检测修这种方式取出的状态的新值是否全等于旧值（判断 `old === value` 是否为真），如果不全等，则会触发 `React` 更新。
>
> `store hook` 接收的第二个参数是一个对比函数，下面例子中使用了 `zustand/shallow` 提供的 `shallow` 相等函数，告诉 `zustand` 如果一个对象的第一层的引用相等，则视为相等：
>
> ~~~typescript
> import { shallow } from 'zustand/shallow'
> 
> const treats = useBearStore((state) => state.treats, shallow)
> ~~~
>
> 可以自定义对比函数：
>
> ~~~typescript
> const treats = useBearStore(
>   (state) => state.treats,
>   (oldTreats, newTreats) => compare(oldTreats, newTreats)
> )
> ~~~

使用以下方式使用 `selector` 可以将 `store` 中的多个值取出并放入一个单一对象中：

> ~~~typescript
> import { shallow } from 'zustand/shallow'
> 
> // 选择 Object, 当`state.nuts`或`state.honey`发生变化后，组件重新渲染
> const { nuts, honey } = useBearStore(
>   (state) => ({ nuts: state.nuts, honey: state.honey }),
>   shallow
> )
> 
> // 选择 Array, 当`state.nuts`或`state.honey`发现变化后，组件重新渲染
> const [nuts, honey] = useBearStore(
>   (state) => [state.nuts, state.honey],
>   shallow
> )
> ~~~

也可以不使用 `selector` 直接使用 `store hook` 取出 `store` 中的整个 `state` 对象，但是这样会导致每次使用 `set` 时都会触发 `React` 的更新：

> ~~~typescript
> ...
> const state = useBearStore()
> ~~~

### `Set`

默认情况下，`set` 函数传入一个对象或返回一个对象的函数，那么会将该对象与 `store` 中的现有状态进行浅合并：

~~~typescript
const useStore = create((set) => ({
  salmon: 1,
  tuna: 2,
  setSalmon: (newVal) => set(() => ({ salmon:1 })),  // 不会抹除掉 tuna，将得到 {salmon, tuna, setSalmon }
}))
~~~

`set` 函数的第二个参数默认为 `false`。如果为 `true`，它将取代(覆盖)状态模型，而不是合并：

~~~typescript
import omit from 'lodash-es/omit'

const useStore = create((set) => ({
  salmon: 1,
  tuna: 2,
  deleteTuna: () => set((state) => omit(state, ['tuna']), true), // 将删除 state.tuna ，保留其它 state、actions
  deleteEverything: () => set({}, true), // 将清除掉完整的 store，包括 state、actions
}))
~~~

## 中间件

中间件可以用来改写使用 `create` 定义 `store` 时，接收的函数的参数。

自定义中间件示例：

~~~typescript
// state 每次发生变化都将输出日志
const log = (config) => (set, get, api) =>
  config(
    (...args) => {
      console.log('  applying', args)
      set(...args)
      console.log('  new state', get())
    },
    get,
    api
  )

// 如果需要 TS 类型推断，则需要柯里化调用 create
const useBeeStore = create()(
  log((set) => ({
    bees: false,
    setBees: (input) => set({ bees: input }),
  }))
)
~~~

另外可以使用 `ramda` 提供的 `pipe` 函数将中间件串联起来：

~~~typescript
import create from 'zustand'
import { pipe } from 'ramda'

const logger = (setState) => (config) => set => {
  return setState({
    ...config,
    set: args => {
      console.log('  applying', args)
      set(args)
      console.log('  new state', getState())
    }
  })
}

const useStore = create(pipe(
  logger,
  set => ({
    bears: 0,
    increasePopulation: () => set(state => ({ bears: state.bears + 1 })),
    removeAllBears: () => set({ bears: 0 })
  })
))
~~~

## 持久化存储

例如将 `store` 中的状态 `fishes` 存储到 `URL` 哈希中：

~~~typescript
import { create } from 'zustand'
import { persist, StateStorage, createJSONStorage } from 'zustand/middleware'

const hashStorage: StateStorage = {
  getItem: (key): string => {
    const searchParams = new URLSearchParams(location.hash.slice(1))
    const storedValue = searchParams.get(key) ?? ''
    return JSON.parse(storedValue)
  },
  setItem: (key, newValue): void => {
    const searchParams = new URLSearchParams(location.hash.slice(1))
    searchParams.set(key, JSON.stringify(newValue))
    location.hash = searchParams.toString()
  },
  removeItem: (key): void => {
    const searchParams = new URLSearchParams(location.hash.slice(1))
    searchParams.delete(key)
    location.hash = searchParams.toString()
  },
}

export const useBoundStore = create(
  persist(
    (set, get) => ({
      fishes: 0,
      addAFish: () => set({ fishes: get().fishes + 1 }),
    }),
    {
      name: 'food-storage', // unique name
      storage: createJSONStorage(() => hashStorage),
      partialize: (state) => ({ fishes: state.fishes })
    }
  )
)
