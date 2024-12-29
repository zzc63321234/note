## Redux基础

### Action

- `action`是把数据从应用传到`store`的有效荷载，它是改变`store`数据的**唯一**途径

- 一般通过`store.dispatch()`将`action`传到`store`

- `action`是一个普通对象

  - `action`内必须使用一个字符串类型的`type`字段来表示将要执行的动作
  - `action`内除`type`字段之外，其余结构可自行决定，但应尽量减少在`action`中传递的数据

  - `action`只描述有事情发生这一事实，并没有描述应用如何更新`state`

- 一些通常做法

  - 将`action types`定义为字符串常量
    - 大型应用中，往往会使用单独的模块或文件来定义，且使用单独的模块或文件来存放`action`
    - 将`action types`定义为常量有以下好处
      - 帮助维护命名一致性，因为所有的`action type`汇总在同一位置
      - 有时，在开发一个新功能之前你想看到所有现存的`actions`，而你的团队里可能已经有人添加了你所需要的`action`，而你并不知道
      - `Action types`列表在`Pull Request`中能查到所有添加，删除，修改的记录。这能帮助团队中的所有人及时追踪新功能的范围与实现
      - 如果在`import`一个`Action`常量的时候拼写错了，会得到 `undefined` 。在`dispatch`这个 `action`的时候，`Redux`会立即抛出这个错误，你也会马上发现错误
  - `Action Creators`
    - 通过创建`Action Creator`函数，调用它以生成`action`对象，而不是在`dispatch`时内联生成它们
    - 当项目中的需求经常变更时，修改`Action Creator`即可同时修改所有生成的`action`
    - 可以在`Action Creator`中定义逻辑，根据对应需求生成不同的`action`
    - 可以再定义生成`action creator`的函数，来快速生成一些简单的`action creator`

### Reducer

- `reducer`指定了`state`如何被`action`更新

- `reducer`是一个**纯函数**，接收旧的`state`和`action`，返回新的`state`

- 一个简单`reducer`示例

  ~~~javascript
  function todoApp(state = initialState, action) {
    switch (action.type) {
      case SET_VISIBILITY_FILTER:
        return Object.assign({}, state, {
          visibilityFilter: action.filter
        })
      default:
        return state
    }
  }
  ~~~

  - `Redux`如何运行`state`
    - 首次执行时，`Redux`会为`reducer`的`state`参数传入`undefined`，
    - 分发`action`时，`store`中的`state`会被传入`reducer`中
  - 注意点
    - 不要在reducer中修改`state`本身，多使用展开运算符
    - 在`default`的情况下返回旧的`state`

- 拆分`reducers`

  - `子reducers`示例

    ~~~javascript
    function todos(state = [], action) {
      switch (action.type) {
        case ADD_TODO:
          return [
            ...state,
            {
              text: action.text,
              completed: false
            }
          ]
        case TOGGLE_TODO:
          return state.map((todo, index) => {
            if (index === action.index) {
              return Object.assign({}, todo, {
                completed: !todo.completed
              })
            }
            return todo
          })
        default:
          return state
      }
    }
    
    function visibilityFilter(state = SHOW_ALL, action) {
      switch (action.type) {
        case SET_VISIBILITY_FILTER:
          return action.filter
        default:
          return state
      }
    }
    ~~~

    - 注意每个`子reducer`只负责管理全局`state`中它负责的一部分。每个`子reducer`的`state`参数都不同，分别对应它管理的那部分`state`数据

  - 合并`reducer`

    - 在`根reducer`中合并`子reducers`

      ~~~javascript
      function todoApp(state = {}, action) {
        return {
          visibilityFilter: visibilityFilter(state.visibilityFilter, action),
          todos: todos(state.todos, action)
        }
      }
      ~~~

    - 另外，`Redux`提供了`combineReducers()`工具类来生成上面的`todoApp`，以下代码和上面代码完全等价

      ~~~javascript
      import { combineReducers } from 'redux'
      
      const todoApp = combineReducers({
        visibilityFilter,
        todos
      })
      ~~~

      - [`combineReducers()`](https://www.redux.org.cn/docs/api/combineReducers.html) 所做的只是生成一个函数，这个函数来调用你的一系列 reducer，每个 reducer **根据它们的 key 来筛选出 state 中的一部分数据并处理**，然后这个生成的函数再将所有 reducer 的结果合并成一个大的对象
      - `Reducx`中只有一个`store`，但是可以拥有多个`reducer`，同时保持各自负责逻辑块的独立性
      - 传入`combineReducers()`中的`子reducer`需要满足以下规则
        - 传入的`action`未被匹配到时，必须把该`reducer`传入的初始`state`原封不动返回
        - 不能返回`undefined`，否则`conbineReducers`会报错
        - 根据前两条，初始`reducer`禁止使用`undefined`

    - 也可以给它们设置成不同的属性名，或调用不同的函数，下面两种合成`reducer`的方式完全等价

      ~~~javascript
      import { combineReducers } from 'redux'
      
      const reducer = combineReducers({
        a: doSomethingWithA,
        b: processB,
        c: c
      })
      ~~~

      ~~~javascript
      function reducer(state = {}, action) {
        return {
          a: doSomethingWithA(state.a, action),
          b: processB(state.b, action),
          c: c(state.c, action)
        }
      }
      ~~~

### Store

- `store`有以下职责

  - 维持应用的`state`
  - 提供`getState()`方法获取`state`
  - 提供`dispatch(action)`方法更新`state`
  - 通过`subscribe(listener)`注册监听器，并可通过其返回的函数注销监听器

- `Redux`应用只有一个单一的`store`，当需要拆分数据处理逻辑时，应使用`reducer`组合而不是创建多个`store`

- 创建`store`

  - 将`根reducer`作为第一个参数传入[`createStore()`](https://www.redux.org.cn/docs/api/createStore.html)中创建`store`

    ~~~javascript
    import { createStore } from 'redux'
    import todoApp from './reducers'
    let store = createStore(todoApp)
    ~~~

  - `creator()`的第二个参数是可选的, 用于设置`state`初始状态

    - 这对开发同构应用时非常有用，服务器端`redux`应用的`state`结构可以与客户端保持一致, 那么客户端可以将从网络接收到的服务端`state`直接用于本地数据初始化

### 三大原则

- 单一数据源
  - 整个应用的`state` 被存储在一颗`object tree`中，而这个`object tree`只存在于唯一一个`store`中
- `state`是只读的
  - 唯一改变`state`的方式就是触发`action`
- 使用纯函数来执行修改
  - 为了描述`action`如何改变`state`，需要编写纯函数`reducer`

### 数据流

- **严格的单向数据流**是`Redux`的设计核心
- `Redux`应用中数据的生命周期遵循以下4个步骤
  1. 调用`store.dispatch(action)`
  2. `Redux store`调用传入的`reducer`函数
  3. `根reducer`把多个`子reducer`输出合并成一个单一的`state`树
  4. `Redux store`保存了根`reducer`返回的完整`state`树