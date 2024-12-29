## 简介

- `Pinia` 本质上是一个状态管理的库，用于**跨组件、页面进行状态共享**，与 `Vuex`、`Redux `一样
- `Pinia` 对比 `Vuex` 的优势
  - `mutations` 不再存在
  - 更友好的 `TypeScript` 支持
  - 不再有 `modules` 的嵌套结构
    - 可以灵活使用每一个 `store`，它们通过扁平化的方式来相互使用
  - 不再需要命名空间的概念

## `Pinia` 安装及 `pinia` 实例对象的创建

- 安装

  - `yarn add pinia`
  - `npm install pinia`

- 创建 `pinia` 实例对象

  - `/src/stores/index.js`

    ~~~javascript
    import { createPinia } from 'pinia'
    
    const pinia = createPinia()
    
    export default pinia
    ~~~

- 应用`pinia`实例对象

  - `/src/main.js`

    ~~~javascript
    ...
    import pinia from './stores'
    
    createApp(App).use(pinia).mount('#app')
    ~~~

## `Store`

- 什么是 `Store`
  - 一个 `Store`（如  `Pinia`）是一个实体，它会保存组件树中的**状态**和**业务逻辑**
  - 它像一个始终存在，并且每个人都可以读取和写入的组件
  - 可以在应用程序中定义任意数量的 `Store` 来管理状态
  
- `Store` 中的三个核心概念
  
  - `state`、`getters`、`actions`
  - 等同于组件中的 `data`、`computed`、`mothods`
  - 一旦 `Store` 被实例化，就可以直接在 `store` 上访问 `state`、`getters `和`action` 中定义的任何属性
  
- 定义 `store`

  - `Store` 是使用 `defineStore()` 方法定义的，并且它需要一个唯一名称（`id`），作为第一个参数传递

  - 第二各参数可以是配置对象，也可以是一个 `setup` 函数

  - 例，`/src/stores/count.js`

    ~~~javascript
    // 配置对象形式
    import { defineStore } from 'pinia'
    
    const useCount = defineStore('count', {
        state: () => ({
            count: 0
        }),
        getters: {
            doubleCount: (state) => state.count * 2
        },
        actions: {
            increment() {
                this.count++
            }
        }
    })
    
    export default useCount
    // defineStore() 中传入的第一个字符串参数，代表定义的 store 的标识
    // defineStore() 的返回值，即此处的 useCount 是一个函数
    ~~~
    
    ~~~javascript
    // setup 函数形式
    import { ref, computed } from 'vue'
    import { defineStore } from 'pinia'
    
    export const useCounter = defineStore('counter', () => {
      const count = ref(0)
      const doubleCount = computed(() => count.value * 2)
      function increment() {
        count.value++
      }
    
      return { count, doubleCount, increment }
    })
    
    ~~~
    
  
- 使用  `store`

  -  直到调用 `defineStore()` 返回的 `use` 函数，才会创建 `store` 实例
  
  -  例，`/src/views/count.vue`
  
     ~~~vue
     <template>
         <h2>count: {{ countStore.count }}</h2>
     </template>
     
     <script setup>
         // 通过useCount函数来获取store
         import useCount from '@/store/count'
         const countStore = useCount()
     </script>
     ~~~
  
  -  `store` 获取到后不能被解构，否则会失去响应式
  
     - 如果需要从 `store` 中提取属性并保持响应式，需要使用 `pinia` 提供的 `storeToRefs()`，或 `vue` 提供的 `toRefs()`
  
     - 例，`/src/views/count.vue`
  
       ~~~javascript
       ...
       import 'storeToRefs' from 'pinia'
       import 'toRefs' from 'vue'
       const { count } = countStore // 失去响应式
       const { count: counter2 } = toRefs(countStore) // 保持响应式
       const { count: counter3 } = storeToRefs(countStore) // 保持响应式
       ...
       ~~~
  

## `State`

- 定义 `state`

  - 在 `Pinia` 中，`state` 被定义为返回初始状态的函数

    ~~~javascript
    import { defineStore } from 'pinia'
    
    const useCount = defineStore('count', {
        // 定义state
        state: () => ({
            count: 99,
            age: 18
        })
    })
    
    export default useCount
    ~~~

- 操作 `state`

  - 默认情况下，可以通过 `store` 实例访问 `state` 来直接读取和写入状态

    ~~~javascript
    const countStore = useCount()
    countStore.count++
    countStore.age = 28
    ~~~

  - 使用 `store` 实例提供的方法操作 `state`

    - `$reset()`

      - 重置 `state`

        ~~~javascript
        const countStore = useCount()
        countStore.$reset()
        // countStore中的state会被整个重置到定义时的初始状态
        ~~~

    - `$patch()`

      - 改变 `state` 中的多个数据

        ~~~javascript
        const countStore = useCount()
        countStore.$patch({
            count: 100,
            age: 28
        })
        ~~~

    - `$state()`
    
      - 替换整个state
    
        ~~~javascript
        const countStore = useCount()
        countStore.state({
            count: 100,
            age: 28
        })
        ~~~
  
- 订阅 `state`

  - 可以通过 `store` 的 `$subscribe()` 方法查看状态及其变化，`state ` 的订阅只会在 `patch` 之后触发一次

    ~~~javascript
    cartStore.$subscribe((mutation, state) => {
       
      // mutation主要包含三个属性值：
      //   events：当前state改变的具体数据，包括改变前的值和改变后的值等等数据
      //   storeId：是当前store的id
      //   type：用于记录这次数据变化是通过什么途径，主要有三个分别是
      //        'direct'：通过 action 变化的
      //        'patch object'：通过 $patch 传递对象的方式改变的
      //        'patch function'：通过 $patch 传递函数的方式改变的
      
      // mutation 的类型
      mutation.type // 'direct' | 'patch object' | 'patch function'
      // 执行 mutation 的 store 的 id，与 cartStore.$id 相同
      mutation.storeId // 'cart'
      // 仅适用于 mutation.type === 'patch object'
      mutation.payload // 传递给 to cartStore.$patch() 的对象
    
      // 每当它发生变化时，将整个状态持久化到本地存储
      localStorage.setItem('cart', JSON.stringify(state))
    })
    ~~~

  - 默认情况下，组件卸载后，订阅会被删除。如果想保留，需将 `{ detached: true }` 作为第二个参数传递给 `$subscribe()`

    ```javascript
    export default {
      setup() {
        const someStore = useSomeStore()
    
        // 此订阅将在组件卸载后保留
        someStore.$subscribe(callback, { detached: true })
    
        // ...
      },
    }
    ```

## `Getters`

- `getters` 相当于 `store` 状态的计算属性

- 定义  `getters`
  - `getters` 可以用 `defineStore()` 中的 `getters` 属性定义
  
  - `getters `属性中可以定义接收一个 `state` 作为参数的函数
  
    - 函数内部可以直接使用接收的 `state` 参数
    - 函数的 `this` 指向当前定义的 `store` 实例
  
  - 例  `/src/stores/foo.js`
  
    ~~~javascript
    // 如果需要访问其他store中的getters，需引入该store
    import 'useMessage' from './message.js'
    
    const useFoo = defineStore('foo', {
        // 定义state
        state: () => ({
            count: 99,
            users：[
                {userId: 001, name: '小明'}，
                {userId: 002, name: '张三'}
            ]
        }),
        // 定义getters
        getters: {
            // 一般情况下推荐使用箭头函数
            doubleCount: (state) => state.count * 2,
            // 如果需要在一个getter中访问其他getters，需调用this
            doubleCountPlusOne() {
                return this.doubleCount + 1
            },
            // 实现给getters传递参数
            getUserById: (state) => {
                // 当在组件中将此getter作为函数并携带参数调用时，返回以下这个函数
                // 以下这个函数会接收传递给getters的参数，并再次返回一个值
                return (userId) => state.users.find((user) => user.id === userId)
            },
            // 访问其他store中的getters
            message(state) {
                const messageStore = useMessage()
                return state.count + messageStore.message1
            }
        }
    })
    ~~~

## `Actions`

- `actions` 相当于组件中的 `methods`，非常适合定义业务逻辑

- 定义 `actions` 
  - `actions `可以使用 `defineStore()` 中的 `actions` 属性定义
  
    ~~~javascript
    export const useStore = defineStore('main', {
      state: () => ({
        counter: 0,
      }),
      actions: {
        increment() {
          this.counter++
        },
        randomizeCounter() {
          this.counter = Math.round(100 * Math.random())
        },
      },
    })
    ~~~
  
- 与 `getters` 一样，`actions` 可以通过 `this` 访问 `store` 实例

- `actions `可以是异步的，可以在其中 `await` 任何异步操作

  ~~~javascript
  import { mande } from 'mande'
   
  const api = mande('/api/users')
  
  export const useUsers = defineStore('users', {
    state: () => ({
      userData: null,
      // ...
    }),
  
    actions: {
      async registerUser(login, password) {
        try {
          this.userData = await api.post({ login, password })
          showTooltip(`Welcome back ${this.userData.name}!`)
        } catch (error) {
          showTooltip(error)
          // 让表单组件显示错误
          return error
        }
      },
    },
  }) 
  ~~~

- 在 `actions` 中访问其他 `store`

  ~~~javascript
  import { useAuthStore } from './auth-store'
  
  export const useSettingsStore = defineStore('settings', {
    state: () => ({
      // ...
    }),
    actions: {
      async fetchUserPreferences(preferences) {
        const auth = useAuthStore()
        if (auth.isAuthenticated) {
          this.preferences = await fetchPreferences()
        } else {
          throw new Error('User must be authenticated')
        }
      },
    },
  })
  ~~~

- 订阅 `actions`

  - 可以在组件中使用 `store.$onAction()` 订阅 `action` 及其执行的结果

    ~~~javascript
    const unsubscribe = userStore.$onAction(
      ({
        name, // action 的名字
        store, // store 实例
        args, // 调用这个 action 的参数
        after, // 在这个 action 执行完毕之后，执行这个函数
        onError, // 在这个 action 抛出异常的时候，执行这个函数
      }) => {
        // 记录开始的时间变量
        const startTime = Date.now()
        // 这将在 `store` 上的操作执行之前触发
        console.log(`Start "${name}" with params [${args.join(', ')}].`)
     
        // 如果 action 成功并且完全运行后，after 将触发。
        // 它将等待任何返回的 promise
        after((result) => {
          console.log(
            `Finished "${name}" after ${Date.now() - startTime}ms.\nResult: ${result}.`
          )
        })
     
        // 如果 action 抛出或返回 Promise.reject ，onError 将触发
        onError((error) => {
          console.warn(
            `Failed "${name}" after ${Date.now() - startTime}ms.\nError: ${error}.`
          )
        })
      }
    )
     
    // 手动移除订阅
    unsubscribe()
    ~~~
    
  - 默认情况下，`actions`的订阅会被绑定到添加它们的组件中，当组件被卸载时，订阅将被自动删除，如果需要在卸载组件后仍保留订阅，可以将`true`作为第二个参数传递给`store.$onAction()`

    ~~~javascript
    export default {
      setup() {
        const someStore = useSomeStore()
    
        // 此订阅将在组件卸载后保留
        someStore.$onAction(callback, true)
    
        // ...
      },
    }
    ~~~
  
    