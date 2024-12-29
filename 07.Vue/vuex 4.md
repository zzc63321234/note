# Vuex

- 在`Vue`中实现集中式状态（数据）管理的一个`Vue插件`，对`Vue`应用中多个组件的共享状态进行集中式的管理（读/写），也是一种组件间通信的方式，且适用于任意组件间通信

## Vuex核心概念

![](D:\前端学习\笔记\7.Vue\images\vuex.png)

### State、Mutation、Action之间的关系

- State
  - `store`中管理的状态`state`

- Mutation
  - `mutation`用于**改变(`mutate`)`store`中的`state`**
  - `mutation`中不能包含异步操作

- Action

  - `action`可以**提交(`commit`)`mutation`**

  - `action`中可以包含任意异步操作

### Store

- 每个`Vuex应用`的核心就是`store`(仓库)

- `store`本质上是一个容器，包含着应用装大部分的状态(`state`)

- 在项目目录结构中，一般放在`./src/store/index.js`

- 创建`store`实例

  ~~~javascript
  import { createStore } from 'vuex'
  
  export default createStore({
    state: () => ({
        // state中传入一个函数，并让其返回一个对象
    }),
    getters: {
    },
    mutations: {
    },
    actions: {
    },
    modules: {
    }
  })
  ~~~

- 在`main.js`中引入`store`实例，并使用`app.use(store)`将其作为插件注册

- 在组件中访问`store`实例的方式

  - 模板中

    - `$store`

  - `Options API`中

    - `this.$store`

  - `setup`函数中

    - 通过`vuex`提供的`Hook`：`useStore()`

      ~~~javascript
      import { useStore } = 'vuex'
      const store = useStore()
      ~~~

### State

- `单一状态树`概念
  - 使用一个对象就包含了全部的应用层级的状态，即每个应用只有一个`store`实例
  - 英文名`SSOT`(`Single Source of Truth`)，也可以翻译为`单一数据源`
  - 单一状态树和模块化不冲突
  - 特点
    - 优势：能使用最直接的方式找到某个状态的片段
    - 劣势：不够灵活

- 组件中处理`state`

  - `Options API`中

    - 一般存放在`computed`中

      ~~~javascript
      computed: {
          counter() {
              return this.$store.state.counter
          }
      }
      ~~~

  - `setup`函数中

    - `store.state`是一个由`reactive()`创建的响应式对象，如果将里面的属性赋值给变量，则它们不会是响应式的
    - 可以使用`toRefs()`结合解构赋值取出其中的属性

    - 示例

      ~~~javascript
      const store = useStore()
      // 如果需要使用解构赋值，则需要为store.state中的属性创建ref
      const state = toRefs(store.state) // state中每个属性都被指向了store.state中属性的ref
      const { counter } = state // 解构出来的counter是一个ref
      ~~~

- `mapState`

  - `vuex`提供的方法，用于批量映射`state`

  - 基本使用

    - 传入一个对象或数组，返回一个包含对应`state`属性的对象(在`Options API`中返回的对象可以直接使用，在`setup`中需进一步处理)

    - 传入数组写法

      ~~~javascript
      import { mapState } from 'vuex'
      mapState([ 'num1', 'num2' ]) // 沿用state中的属性名时使用数组写法
      ~~~

    - 传入对象写法

      ~~~javascript
      import { mapState } from 'vuex'
      mapState({ 
          num01: state => state.num1,
          num02: state => state.num2
      }) // 需要指定属性名时使用对象写法
      ~~~

  - 在`Options API`中使用`mapState`

    - 使用展开运算符将其放入`computed`

      ~~~javascript
      computed: {
          ...mapState([ 'num1', 'num2' ])
      }
      ~~~

  - 在`setup`中使用`mapState`

    - `Vuex`并没有提供方便地使用`mapState`的方式

    - `mapState()`返回的对象的属性实际上是函数，并且会读取`this.$store.state`以获得`state`中的属性，而`setup`中的`this`并不指向组件实例因此需要重新指定函数的`this`

    - 示例

      ~~~javascript
      import { computed } from 'vue'
      import { mapState, useStore } from 'vuex'
      
      const { num1, num2 } = mapState([ 'num1', 'num2' ])
      const cNum1 = computed(num1.bind({ $store: store }))
      const cNum2 = computed(num2.bind({ $store: store }))
      ~~~

    - 在`setup`中可以使用**`torefs`+解构赋值**的方式代替`mapState`，更为简便

### Getters

- `getter`可以将`state`中的数据加工后再使用

- 基本使用

  - 定义`getter`

    ~~~javascript
    getters: {
        sum(state) {
            return num1 + num2
        }
    }
    ~~~

    - `getter`的参数
      - 第一个参数为`state`，可以用来获取`state`中的数据
      - 第二个参数为`getters`，可以用来获取其他`getter`
    - `getter`的返回值
      - `getter`可以返回js表达式作为其结果
      - `getter`也可以返回一个函数
        - 使用这个`getter`相当于调用这个函数
        - 函数中再返回值作为`getter`的结果
        - 这样使得`getter`在使用时可以传入参数

  - 组件中处理方式大体上同`state`

    - `store.getters`不是一个响应式对象，因此如果和`state`一样配合`toRefs`使用解构赋值取出属性，控制台会弹出警告

    - 如果需要取出其中的属性并保持响应式，需要用到`computed()`

      ~~~javascript
      const store = useStore()
      const sum = computed(() => store.getters.sum)
      ~~~

- `mapGetters`

  - `vuex`提供的方法，用于批量映射`getter`
  - 使用方式基本同`mapState`

### Mutations

- `mutation`是用于改变`store`中的`state`的方法

- `mutation`必须是同步函数

- `mutation`非常类似于事件，每个`mutation`都有一个字符串的**事件类型(`type`)**和一个**回调函数(`handler`)**

- 定义`mutation`

  ~~~javascript
  const store = createStore({
      state: () => ({
          count: 1
      }),
      mutations: {
          increament (state) {
              state.count++
          }, // 'increament'相当于事件类型
          setCount (state, payload) {
              state.count = payload.num
          }
      }
  })
  ~~~

  - `mutation`的第一个参数是`state`，第二个可选参数`payload`可用于携带数据

- 在组件中提交(`commit`)`mutation`

  - `Options API`中

    ~~~javascript
    this.$store.commit("increament")
    
    // 载荷风格的提交方式
    this.$store.commit("setCount", { num: 58 })
    
    // 对象风格的提交方式
    this.$store.commit({
        type: 'setCount',
        num: 58
    })
    ~~~

  - `setup`函数中

    ~~~javascript
    const store = useStore()
    
    store.commit("increament")
    
    // 载荷风格的提交方式
    store.commit("setCount", { num: 58 })
    
    // 对象风格的提交方式
    store.commit({
        type: 'setCount',
        num: 58
    })
    ~~~

- 使用常量替代`Mutation`事件类型

  - 在需要多人协作的大型项目中， 可以使用常量替代`Mutation`事件类型，并将这些常量放在单独的文件中，使得代码合作者对整个app包含的`mutation`一目了然

  - 定义常量

    - 在`.store/mutation-types.js`中定义常量

      ~~~javascript
      export const SET_COUNT = 'SET_COUNT'
      ~~~

    - 在`.store/index.js`中定义`mutation`

      ~~~javascript
      import { SET_COUNT } from './mutation-types.js'
      ......
      mutations{
          [SET_COUNT] (state, payload) {
              state.count = payload.num
          }
      }
      ......
      ~~~

    - 提交`mutation`

      ~~~javascript
      import { SET_COUNT } from '@/store/mutation-types.js'
      ......
      const store = useStore()
      store.commit(SET_COUNT, { num: 58 })
      ......
      ~~~

- `mapMutations`

  - `vuex`提供的方法，用于批量映射`mutation`

  - 传入数组或对象，返回一个对象，调用对象中的方法时，相当于`commit`对应的`mutation`

  - 在`Options API`中将`mapMutations()`返回的对象使用展开运算符放入`methods` 

    ~~~javascript
    import { mapMutations } from 'vuex'
    
    export default {
      // ...
      methods: {
        ...mapMutations([
          'increment', // 将 `this.increment()` 映射为 `this.$store.commit('increment')`
    
          // `mapMutations` 也支持载荷：
          'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.commit('incrementBy', amount)`
        ]),
        ...mapMutations({
          add: 'increment' // 将 `this.add()` 映射为 `this.$store.commit('increment')`
        })
      }
    }
    ~~~

### Actions

- `action`类似于`mutation`，不同在于

  - `action`不直接变更`state`，只能通过提交`mutation`来变更
  - `action`可包含任意异步操作

- 定义`action`

  ~~~javascript
  const store = createStore({
      state: () => ({
          count: 1
      }),
      mutations: {
          increament (state) {
              state.count++
          },
          setCount (state, payload) {
              state.count = payload.num
          }
      },
      actions: {
          increamentAct (context) {
              context.commit("increament")
          },
          setCountAct (context, payload) {
              context.commit("increament", payload)
          }
      }
  })
  ~~~

  - `action`函数接收一个与`store`实例具有相同方法和属性的`context`对象，因此可以通过`context`来获取`state`、`mutations`和`getters`
  - `action`也可以接收第二个可选参数`payload`可用于携带数据

- 在组件中分发(`dispatch`)`action`

  - `Options API`中

    ~~~javascript
    this.$store.dispatch('incrementAct')
    
    // 载荷风格的分发方式
    this.$store.dispatch('setCountAct', { num: 58 })
    // 对象风格的分发方式
    this.$store.dispatch({
        type: 'setCountAct',
        num: 58
    })
    ~~~

  - `setup`函数中

    ~~~javascript
    const store = useStore()
    
    store.dispatch('incrementAct')
    
    // 载荷风格的分发方式
    store.dispatch('setCountAct', { num: 58 })
    // 对象风格的分发方式
    store.dispatch({
        type: 'setCountAct',
        num: 58
    })
    ~~~

- `mapActions`

  - `vuex`提供的方法，用于批量映射`action`

  - 传入数组或对象，返回一个对象，调用对象中的方法时，相当于`dispatch`对应的`action`

  - 在`Options API`中将`mapActions`返回的对象使用展开运算符放入`methods` 

    ~~~javascript
    import { mapActions } from 'vuex'
    
    export default {
      // ...
      methods: {
        ...mapActions([
          'increment', // 将 `this.increment()` 映射为 `this.$store.dispatch('increment')`
    
          // `mapActions` 也支持载荷：
          'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.dispatch('incrementBy', amount)`
        ]),
        ...mapActions({
          add: 'increment' // 将 `this.add()` 映射为 `this.$store.dispatch('increment')`
        })
      }
    }
    ~~~

- `action`中的异步操作

  - 可以通过让`action`返回`Promise`，在`Promise`的`then`中来处理完成后的操作

    - 定义`action`

      ~~~javascript
      actions: {
        actionA ({ commit }) {
          return new Promise((resolve, reject) => {
            setTimeout(() => {
              commit('someMutation')
              resolve()
            }, 1000)
          })
        }
      }
      ~~~

    - 分发`action`

      ~~~javascript
      store.dispatch('actionA').then(() => {
        // ...
      })
      ~~~


### Module

- `module`的作用
  - 由于使用单一状态树，应用的所有状态会集中到一个比较大的对象，当应用变得非常复杂时，`store`对象可能会变得非常臃肿
  - 可以将`store`分割成`module`
  - 每个模块拥有自己的`state`、`mutation`、`action`、`getter`，甚至是嵌套子模块

- 基本使用

  - 在`store`目录下创建`modules`目录，该目录下保存各个`module`

  - `src/store/modules/home.js`中

    ~~~javascript
    export default {
        state: () => ({
            ......
        }),
        mutations: {......},
        actions: {......},
        getters: {......}
    }
    ~~~

  - `src/store/index.js`中
  
    ~~~javascript
    import { createStore } from 'vuex'
    import home from './modules/home.js'
    
    export default createStore({
      modules: {
          home
      }
    })
    // 读取模块中的state时，如，$store.state.home.xxx是home模块的xxx状态
    // 读取模块中的getters时，则不需要使用模块名，直接可以使用$store.getters.xxx
    // 派发actions和提交mutations时也不需要加入模块名
    ~~~
  
- `module`的局部状态

  - 模块内部的`mutation`和`getter`，接收的第一个参数是模块的局部状态对象

    ~~~javascript
    mutations: {
        changeName(state) {
            state.name = '小明'
        }
    }
    
    getters: {
        info(state, getters, rootState) {
            return `name: ${state.name} age: ${state.age} height: ${state.height}`
        }
    }
    ~~~

- `module`的命名空间

  - 默认情况下，模块内部的`actions`、`mutations`以及`getters`仍是注册在全局的命名空间中

    - 这样使得多个模块能够对同一个action或mutation作出响应

  - 如果希望模块具有更高的封装度和复用性，可以添加`namespaced: true`的方式使其成为带命名空间的模块

    - 当模块被注册后，它的所有`getters`、`actions`即`mutations`都会自动根据模块注册的路径调整命名

    - 例：`src/store/modules/home.js`中

      ~~~javascript
      export default {
          namespaced: true,
            // 模块内容（module assets）
            state: () => ({ ... }), // 模块内的状态已经是嵌套的了，使用 `namespaced` 属性不会对其产生影响
            getters: {
              isAdmin () { ... } // -> getters['account/isAdmin']
            },
            actions: {
              login () { ... } // -> dispatch('account/login')
            },
            mutations: {
              login () { ... } // -> commit('account/login')
            }
      }
      ~~~
