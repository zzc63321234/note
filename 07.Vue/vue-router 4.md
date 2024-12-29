# vue-router 4

## 路由的概念

- 网络工程中的路由

  - 构架一个网络时，非常重要的两个设备就是路由器和交换机

  - 路由器主要维护的是一个`映射表`，家庭路由器维护的映射关系是`路由器分配的ip地址 <---> 设备mac地址`

  - `映射表`会决定数据的流向

- 路由的概念在软件工程中出现时，最早是在后端路由中实现的，并经历了一些阶段

  - 后端路由阶段
  - 前后端分离阶段 ——> 单页面富应用(SPA)

- 后端路由阶段

  - 早期的网站开发，整个HTML页面都由服务器渲染，再返回客户端进行展示
    - 每个页面都有对应的URL
    - URL发送到服务器，服务器通过正则对该URL进行匹配，最后交给一个`Controller`进行处理
    - `Controller`进行各种处理，最终生成HTML或数据，返回给前端
  - 这种情况下渲染好的页面，不需要单独加载任何的js和css，可以直接交给浏览器展示，这样也有利于SEO优化
  - 缺点
    - 要么整个页面的模块由后端人员来编写和维护
    - 如果要单独的前端人员开发页面，需要通过PHP和Java等语言来编写页面代码
    - 通常情况下HTML代码和数据以及对应的逻辑会混在一起，编写和维护都很糟糕

- 前后端分离阶段

  - 随着`Ajax`的出现，有了前后端分离的开发模式

  - 前后端的理解
    - 每次请求涉及到的**静态资源**都会从**静态资源服务器**获取，这些资源包括`HTML+CSS+JS`，然后在前端对这些请求回来的资源进行渲染
    - 动态数据通过发送`Ajax`请求来获取，通过`JS`将数据渲染到页面中，此时后端只负责提供`API`
    - 这样做的最大优点是前后端责任的清晰，后端专注于数据，前端专注于交互和可视化

- 单页面富应用阶段

  - `SPA`最主要的特点是在前后端分离的基础上加了一层`前端路由`，由前端来维护一套`路由规则`
  - 前端路由的核心是，改变URL，但页面不进行整体的刷新

## vue-router简介

- `vue-router`是`Vue`的官方路由

- `vue-router`是基于路由和组件的
  - 路由用于设定**访问路径**，将**路径**和**组件**映射起来
  - 在`vue-router`的单页面应用中，路径的改变就是组件的切换

## 基本使用

### 使用步骤

- 创建路由需要映射的组件

- 通过`createRouter()`创建`路由对象`，并在配置对象中传入`routes`选项和`history`选项

  - `routes`是由一个个路由关系组成的数组

  - `history`选项用以在不同的`历史模式`中进行选择

  - 示例

    ~~~javascript
    import { createRouter, createWebHistory } from 'vue-router'
    import HomeView from '../views/HomeView.vue'

    // 配置路由的映射
    const routes = [
      {
        path: '/',
        name: 'home', // 路由命名
        component: HomeView
      },
      {
        path: '/about',
        name: 'about',
        // route level code-splitting
        // this generates a separate chunk (about.[hash].js) for this route
        // which is lazy-loaded when the route is visited.
        // 路由懒加载写法
        component: () => import(/* webpackChunkName: "about" */ '../views/AboutView.vue')
      }
    ]

    // 创建router对象
    const router = createRouter({
      history: createWebHistory(process.env.BASE_URL), // 使用html5-history模式，如果要用hash模式，需要引入createWebHashHistory
      routes
    })

    export default router
    ~~~


- 在`main.js`中引入路由对象，并使用`app`注册

  - `app.use(router)`

- 使用路由

  - `<router-view>`进行占位
  - `<router-link>`进行路由切换

### 路由的属性

- `path`

  - 匹配的路径，使用 `/` 分隔

  - 如果想匹配任意路径，可以使用自定义的路径参数正则表达式，在路径参数后面的括号中加入正则表达式

    ~~~javascript
    const routes = [
      // 将匹配所有内容并将其放在 `$route.params.pathMatch` 下
      { path: '/:pathMatch(.*)*', name: 'NotFound', component: NotFound },
      // 将匹配以 `/user-` 开头的所有内容，并将其放在 `$route.params.afterUser` 下
      { path: '/user-:afterUser(.*)', component: UserGeneric },
    ]
    ~~~

    - `'/:pathMatch(.*)*'` 的含义
      - `/:pathMatch` 定义了一个名为 `pathMatch` 的路由参数
      - `(.*)` 表示这是一个正则表达式，上述路由参数可以匹配到该正则表达式能匹配到的内容
      - 最后的 `*` 表示这个路由参数可以在路径中出现任意次（包括 `0` 次）

  - [捕获所有路由或 404 Not found 路由](https://router.vuejs.org/zh/guide/essentials/dynamic-matching.html#捕获所有路由或-404-not-found-路由)

- `redirect`

  - 当访问`/`路径时，重定向到`/home`路径

    ~~~javascript
    const routes = [
        { path: '/', redirect: '/home'}
        { path: '/home', component: Home }，
    ]
    ~~~



- `name`

  - 为路由命名

- `meta`
  - 配置自定义数据

  - 示例

    ~~~javascript
    const routes = [
      {
        path: '/posts',
        component: PostsLayout,
        children: [
          {
            path: 'new',
            component: PostsNew,
            // 只有经过身份验证的用户才能创建帖子
            meta: { requiresAuth: true }
          },
          {
            path: ':id',
            component: PostsDetail
            // 任何人都可以阅读文章
            meta: { requiresAuth: false }
          }
        ]
      }
    ]
    ~~~

- `children`

  - 配置嵌套路由

    ~~~javascript
    [
        {
            path: '/about',
            component: About
        },
        {
            path: '/home',
            component: Home,
            children:[
                {
                    path: 'news', // 前面不要加斜杠，否则会被识别为绝对路径
                    component: News
                },
                {
                    path: 'message', // 前面不要加斜杠，否则会被识别为绝对路径
                    component: Message
                }
            ]
        }
    ]
    ~~~


### router-link的属性

- `to`

     ~~~vue
     // 使用路径跳转
     <router-link to="/hone/news">跳转</router-link>
     ~~~

     ~~~vue
     // 通过路由名跳转
     <router-link :to={name:'news'}}>跳转</router-link>
     ~~~

- `replace`

     - 作用：控制路由跳转时操作浏览器历史记录的模式
     - 浏览器的历史记录有两种写入方式
       - `push`：往栈中追加历史记录
       - `replace`：替换当前栈顶的记录
     - `router-link`默认调用`router.push()`，如果在`router-link`标签中加入`replace`属性，则调用`router.replace()`

- `active-class`

     - 默认值为`router-link-active`
     - 链接激活时，应用于渲染的`<a>`元素的`class`，可结合`CSS`设置高亮样式

- `exact-active-calss`

     - 默认值为`router-link-exact-active`
     - 链接精准激活时，应用于渲染的`<a>`元素的`class`，可结合`CSS`设置高亮样式

## 历史模式的选择

### 前端路由的历史模式

#### URL的hash

- `URL`的`hash`也就是`锚点`(`#`)

- `BOM`中的`hash`

  - 通过`window.location.href`可以获取到当前的完整`URL`，如果为其赋值，则会作为`hash`传入当前`URL`，并跳转
  - 通过`window.location.hash`可以获取当前`URL`中的`hash`，如果为其赋值，则会修改当前`URL`栏中的hash，但不会跳转

  - 可以监听`window`的`hashchange`事件以监听`hash`的改变

- `hash`不会包含在`HTTP`请求中，所以不会带给服务器

- 使用`hash`来实现前端路由映射的优势是兼容性好，缺点是在`URL`中带有`#`显得不美观

#### HTML5的History

- `window.history`是`HTML5`新增的接口，它有六种模式改变`URL`而不刷新页面
  - `replaceState()` 替换原来的路径
  - `pushState()` 使用新的路径
  - `popState()` 路径的回退
  - `go(num)` 向前或向后改变路径，参数可以是正整数或负整数
  - `forward()` 向前改变路径
  - `back()` 向后改变路径
- 通过`window`的`popstate`事件来监听`URL`变化，来实现前端路由的映射
- 兼容性比`hash`实现的方式差，且在应用部署时需要在服务器上添加回退路由

### vue-router中选择历史模式

- 在`createRouter`接收的配置对象中，使用`history`字段指定历史模式
- 需要从`vue-router`中引入`createWebHistory`函数（对应`Html5 History`模式）或`createWebHashHistory`函数（对应`Hash`模式）
  - 这两个函数可以接收一个字符串，表示一个`baseURL`，如果应用被部署在一个子路径下，就可以用`baseURL`表示这个子路径
  - 其中`Hash`模式的`baseURL`会被定义在`#`之后
  - 可以将`baseURL`设置在`process.env.BASE_URL`中
- `history`字段接收`createWebHistory`函数或`createWebHashHistory`执行后的返回值以指定历史模式

## 路由懒加载

- 当打包构建应用时，JS包会变得非常大，影响首屏加载速度

- 可以将不同路由对应的组件分割成不同的代码块，当路由被访问的时候才加载对应组件

- `vue-router`支持动态导入组件

  - `routes`中的`component`选项可以传入一个组件，也可以接收一个能返回一个`Promise`的函数

  - `import()`函数是一个较新的`ECMAScript语法`，用于动态导入，并返回一个`Promise`

  - 示例

    ~~~javascript
    const routes = [
      {
        path: '/',
        name: 'home', // 路由命名
        component: () => import(/* webpackChunkName: "home" */ '../views/HomeView.vue')
      }, // /* webpackChunkName: "about" */ 是魔法注释，可以为使用webpack打包后的js分包命名
      {
        path: '/about',
        name: 'about',
        component: () => import('../views/AboutView.vue')
      }
    ]
    ~~~

    - 为何要将`import()`的返回的`Promise`放入一个箭头函数中？
      - 如果直接使用`import()`返回的`Promise`，则这个`Promise`会在代码执行到这一行时立即执行
      - 当使用工厂函数来返回这个`Promise`时，则只有在工厂函数被调用时才会执行
      - `vue-router`会在访问到该路由时执行该工厂函数


## 路由参数

#### params参数

##### 基本使用

- 定义路由

  - 示例

    ~~~javascript
    {
        path: '/user/:id',
        component: () => import('../views/User.vue')
    }
    ~~~

- 传递`params`参数

  - 示例

    ~~~html
    <router-link to="/user/123">用户123</router-link>
    ~~~

- 获取`params`参数

  - 模板中

    - `$route.params.id`

  - `created`生命周期中

    - `this.$route.params.id`

  - `setup`函数中

    - 使用`vue-router`提供的一个`Hook`：`useRoute()`

    - 该`Hook`会返回一个`Route`对象

    - 示例

      ~~~vue
      <script setup>
          import { useRoute } from 'vue-router'
          const route = useRoute()
          console.log(route.params.id)
      </script>
      ~~~


##### 在参数中自定义正则

- 定义路由时，可以在路径的`params`参数后使用括号来为参数指定一个自定义的正则

  ~~~javascript
  const routes = [
    // /:orderId -> 仅匹配数字
    { path: '/:orderId(\\d+)' },
    // /:productName -> 匹配其他任何内容
    { path: '/:productName' },
  ]
  ~~~

- 遇到正则表达式中出现`\`符号的情况，需要在其之前再添加一个`\`作为转义反斜杠

##### 可重复参数

- 如果需要匹配具有多个部分的路由，如`/one/two/three`等。应该用`*`(表示0个或多个)和`+`(表示1个或多个)将参数标记为可重复

  ~~~javascript
  const routes = [
    // /:chapters ->  匹配 /one, /one/two, /one/two/three, 等
    { path: '/:chapters+' },
    // /:chapters -> 匹配 /, /one, /one/two, /one/two/three, 等
    { path: '/:chapters*' },
  ]
  ~~~

- 这样，上述案例中的`params.chapters`会是一个数组，而不是字符串

- 可以与自定义正则结合使用

#### query参数

- 传递`query`参数

  - `query`参数需要在设置路由跳转时传入对象来传递

  - 编程式导航传递`query`参数示例

    ~~~javascript
    router.push({
        path: '/profile'
        query: {
            name: 'Ash',
            age: 10
        }
    })
    ~~~

- 获取`query`参数

  - 模板中

    - `$route.query.name`

  - `created`生命周期中

    - `this.$route.query.name`

  - `setup`函数中

    - 使用`vue-router`提供的一个`Hook`：`useRoute()`

    - 该`Hook`会返回一个`Route`对象

    - 示例

      ~~~vue
      <script setup>
          import { useRoute } from 'vue-router'
          const route = useRoute()
          console.log(route.query.name)
      </script>
      ~~~

## 编程式路由导航

- `Options API`中

  - `this.$router.push()`
  - `this.$router.replace()`

- `setup`函数中

  ~~~javascript
  const router = useRouter()
  // router.replace()
  // router.push()
  ~~~

- 传递的参数

  - 可以是路径字符串

    ~~~javascript
    router.push('/profile')
    ~~~

  - 可以传入对象

    ~~~javascript
    router.push({
        path: '/profile'
        query: {
            name: 'Ash',
            age: 10
        }
    })
    ~~~


## 动态添管理路由

### 动态添加路由

- 某些情况可能需要动态添加路由，比如根据用户的不同权限，注册不同的路由

- 通过`router`实例的`addRoute()`实现动态添加路由

- 添加一条新的路由记录到当前路由

  ~~~javascript
  const categoryRoute = {
      path: '/category',
      name: 'category',
      component: () => import('../views/Category.vue')
  }

  router.addRoute(categoryRoute)
  ~~~

- 添加一条新的路由作为现有路由的子路由

  ~~~javascript
  const homeMomentRoute = {
      path: '/moment',
      name: 'moment',
      component: () => import('../views/Category.vue')
  }

  router.addRoute('home', homeMomentRoute) // moment会作为home的children
  ~~~

  - 如果传入的路由设置了`name`，并且已经有一个与之名字相同的路由，它会删除之前的路由

### 删除路由

- 有三种方式

  - 方式一：添加一个`name`相同的路由

  - 方式二：通过`router.removeRoute(要删除的路由的名称)`

  - 方式三：通过`router.addRoute()`的返回值回调

    ~~~javascript
    const removeCategoryRoute = router.addRoute(categoryRoute)
    removeCategoryRoute()
    ~~~

### 动态管理路由的其它方法

- `router.hasRoute()`
  - 检查路由是否存在
- `router.getRoutes()`
  - 获取一个包含所有路由记录的数组

## 路由导航守卫

- `vue-router`提供的导航守卫主要用来**通过跳转或取消的方式守卫导航**

### 全局前置路由守卫

- 可以使用`router.beforeEach()`注册一个全局路由守卫
- `router.beforeEach()`接收一个回调作为参数
  - 回调一般接收两个参数
    - `to` 即将进入的路由`Route`对象
    - `from` 即将离开的路由`Route`对象
  - 可选第三个参数`next`，在`vue2`中一般使用`next()`进行跳转路由，在`vue3`中通过回调返回值控制跳转，不再推荐使用`next()`
  - 回调设置返回值
    - `false`
      - 取消当前的导航
      - 如果浏览器的`URL`发生了改变(可能是用户手动，或者浏览器后退按钮)，那么`URl`会重置到`from`路由对应的地址
    - 不返回/`undefined`/`true`
      - 则导航是有效的，并调用下一个导航守卫
    - 一个路由地址
      - 跳转到这个路由地址
      - 可以是一个路径字符串，或一个包含`path`、`query`、`params`等信息的对象

### 其他导航守卫

- 主要路由守卫的概述
  - **全局守卫（`Global Guards`）**：
    - `router.beforeEach`: 在路由正式进入之前调用，可以用来检查用户权限、登录重定向等。
    - `router.afterEach`: 在路由确认进入后调用，不会改变导航本身，常用于设置页面标题、发送统计数据等。
    - `router.onError`: 用于注册一个全局错误处理器，当路由导航过程中发生错误时调用。
  - **路由独享守卫（`Per-Route Guard`）**：
    - `beforeEnter`: 在路由配置中定义，类似于 `router.beforeEach`，但只适用于单个路由。常用于保护特定路由。
  - **组件内守卫（`In-Component Guards`）**：
    - `beforeRouteEnter`: 在渲染该组件的对应路由被确认前调用。无法访问 `this`，因为守卫执行时组件实例还没被创建。
    - `beforeRouteUpdate`: 在当前路由改变，但是该组件被复用时调用（例如，当参数或查询改变，但是路由没有改变）。
    - `beforeRouteLeave`: 导航离开该组件的对应路由时调用，可以用来防止用户在未保存修改的情况下离开。

- [导航守卫 | Vue Router (vuejs.org)](https://router.vuejs.org/zh/guide/advanced/navigation-guards.html)
- 完整的导航解析流程
  1. 导航被触发。
  2. 在失活的组件里调用 `beforeRouteLeave` 守卫。
  3. 调用全局的 `beforeEach` 守卫。
  4. 在重用的组件里调用 `beforeRouteUpdate` 守卫(2.2+)。
  5. 在路由配置里调用 `beforeEnter`。
  6. 解析异步路由组件。
  7. 在被激活的组件里调用 `beforeRouteEnter`。
  8. 调用全局的 `beforeResolve` 守卫(2.5+)。
  9. 导航被确认。
  10. 调用全局的 `afterEach` 钩子。
  11. 触发 DOM 更新。
  12. 调用 `beforeRouteEnter` 守卫中传给 `next` 的回调函数，创建好的组件实例会作为回调函数的参数传入。

## 在setup中使用的API

### useRoute()

- 返回当前路由对象
- 相当于在模板中使用`$route`

### useRouter()

- 返回`router`实例
- 相当于在模板中使用`$router`
- `router`实例的一些方法
  - `replace()`
  - `push()`
  - `go()`
  - `back()`
  - `forward()`
  - `addRoute()`

## 一些功能的实现

### NotFound页面

- 对于没有匹配到的路径，通常可以将其匹配到固定的某个页面

  - 通常可以编写一个动态路由用于匹配所有的错误页面

    ~~~javascript
    {
        path: '/:pathMatch(.*)',
        component: () => import('../views/NotFound.vue')
    }
    ~~~
