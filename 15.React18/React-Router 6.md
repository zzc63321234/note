## `React-Router` 适配版本

- `react-router`
  - 实现 `React Router` 的核心功能
- `react-router-dom`
  - 基于 `react-router`，加入了在浏览器运行环境下的一些功能
- `react-router-native`
  - 基于 `react-router`，加入了 `react-native` 运行环境下的一些功能

## 基本使用

- `react-router` 最主要的 `API` 是提供的一些组件

### 选择匹配模式

- `BrowserRouter` 和 `HashRouter` 都是 `Router` 组件，包含了对路径改变的监听，并且会将响应的路径传递给子组件

- 区别是

  - `BrowserRouter` 使用 `history` 模式
  - `HashRouter` 使用 `hash` 模式

- 应用方式

  ~~~jsx
  <React.StrictMode>
    <HashRouter>
      <APP />
    </HashRouter>
  </React.StrictMode>
  ~~~

### 路由映射配置

- 使用组件的方式
  
  - 相关组件
  
    - `Routes`
      - 包裹所有的 `Route` 组件，在其中匹配一个路由
      - 包裹的 `Route` 组件按先后顺序匹配，即靠前的优先匹配
      - 在 `react-router 5` 版本中使用的是 `Switch` 组件
    - `Route`
      - 用于匹配的路径
      - 接收 `props`
        - `path`
          - 用于设置要匹配的路径
          - 如果接收 `'*'` 则表示匹配所有路径，可排在其余 `Route` 之后表示匹配不到的路径，让其渲染 `404 Not Found` 页面
        - `element`
          - 用于设置匹配到时渲染的组件
          - 传入一个 `React` 元素
          - 在 `react-router 5` 版本中使用的是 `component` 属性
        - `exact`（已不适用）
          - 在 `react-router 5` 版本中用于精确匹配
          - 只有匹配到完全一致的路径，才会渲染对于的组件，否则：
            - 如果当前路径是 `'/about'`
            - 要匹配的路径有 `'/'` 和 `'/about'`
            - 那么 `'/'` 和 `'/about'` 都会被匹配到
          - 在 `react-router 6` 版本中，默认就是精确匹配
  
  - 应用时将 `Routes` 组件放到组件中的对应位置
  
    ~~~jsx
    <Routes>
      <Route path='/' element={<Home />}} />
      <Route path='/about' element={<About />}} />
      <Route path='/profile' element={<Profile />}} />
    </Routes>
    ~~~
  
- 使用配置对象的方式
  
  - 方式一：使用 `useRoutes` 传入配置对象以创建路由组件，配合 `BrowserRouter` 或 `HashRouter` 使用
  
    ~~~jsx
    import React from 'react';
    import { BrowserRouter, useRoutes } from 'react-router-dom';
    import Home from './Home';
    import About from './About';
    import Profile from './Profile';
    
    const AppRoutes = () => {
      const routes = useRoutes([
        { path: '/', element: <Home /> },
        { path: '/about', element: <About /> },
        { path: '/profile', element: <Profile /> },
      ]);
      return routes;
    };
    
    function App() {
      return (
        <BrowserRouter>
          <AppRoutes />
        </BrowserRouter>
      );
    }
    
    export default App;
    ~~~
  
  - 方式二：使用 `RouterProvider` 创建路由组件，配合 `createBrowserRouter` 或 `createHashRouter` 使用
  
    ~~~jsx
    import React from 'react';
    import { createBrowserRouter, RouterProvider } from 'react-router-dom';
    import Home from './Home';
    import About from './About';
    import User from './Profile';
    
    const router = createBrowserRouter([
      { path: '/', element: <Home /> },
      { path: '/about', element: <About /> },
      { path: '/user/:id', element: <Profile /> },
    ]);
    
    function App() {
      return <RouterProvider router={router} />;
    }
    
    export default App;
    ~~~

### 路由跳转

#### 点击跳转

- `Link` 和 `NavLink` 组件可以用于触发路由的跳转

- 通常路径的跳转使用 `Link` 组件，最终会被渲染成 `<a></a>` 元素

- `NavLink` 在 `Link` 的基础上增加了一些样式属性，能知道自己是否处于激活状态

- `Link`

  - 接收 `props`

    - `to`（必须）
      - 用于设置跳转的路径
      - 可以接收字符串或对象
      - 当接收对象时，可以包含如下属性：
        - `pathname`
          - `URL` 的路径部分
        - `search`
          - `URL` 的查询字符串部分
        - `hash`
          - 带有 `'#'` 的字符串，用来表示 `URL` 的片段标识符（例如 `#section1`）
    - `replace`
      - 是否以 `replace` 的形式进行跳转（不会在浏览器保留跳转记录）
      - 接收布尔值，默认 `false`
    - `reloadDocunment`
      - 路径跳转后，是否重新加载页面
      - 接收布尔值，默认 `false`
    - `state`
      - 传递给路由组件的一个对象，可以包含任意数据
      - 在路由组件中可以通过 `location.state` 来访问

  - 示例

    ~~~jsx
    <div>
      <Link to="/">首页</Link>
      <Link to="/about">关于</Link>
      <Link to="/profile">我的</Link>
    </div>
    ~~~

- `NavLink`

  - 相比 `Link` 额外接收的 `props`
    - `className`
      - 为生成的 `<a></a>` 元素添加类名
      - 接收一个函数
        - 函数接收一个对象，对象中的 `isActive` 字段的布尔值表示当前 `NavLink` 是否是被激活
        - 返回类名字符串
    - `style`
      - 指定样式
      - 接收一个函数
        - 函数接收一个对象，对象中的 `isActive` 字段的布尔值表示当前 `NavLink` 是否是被激活
        - 返回 `style` 对象
  - `NavLink` 组件激活时会默认添加 `active` 类，因此也可以直接在 `css` 中为被激活的 `NavLink` 生成的元素添加样式

#### 重定向

- `Navigate` 导航
  - `Navigate` 组件用于路由重定向，一旦这个组件在页面中被渲染，就会执行跳转到对应的 `to` 路径中
  - `Navigate` 组件可以接收的 `props` 有：`to`、`replace`、`state`，这些 `props` 的作用和 `Link` 组件中的相同
  - 用途
    - 可以用在条件渲染中，当某个条件达成时，渲染 `Navigate` 组件，使得路由立即重新跳转
    - 可以被 `Route` 组件的 `element` 属性接收，使得一些路径可以重定向到其它路径中

#### 编程式路由跳转

- 在 `react-router 6` 之后，代码类的 `API` 都迁移到了 `Hooks` 写法

- 如果希望使用代码进行路由跳转，需要通过 `useNavigate` 的 `Hook` 获取到 `navigate` 函数进行操作

- `navigate` 函数

  - 接收参数
    - 第一种用法
      - `to`
        - 接收跳转的路径
      - `potions`（可选）
        - 接收一个对象，包含的字段有：
          - `replace`
          - `state`
    - 第二种用法
      - `delta`
        - 接收一个数字
        - 如接收 `1`，则表示前进一级，接收 `-1` 则表示回退一级

- 在函数式组件中的实现

  ~~~jsx
  import { useNavigate } from 'react-router-dom'
  
  function SomeComponent() {
    const navigate = useNavigate()
  
    function handleClick() {
      navigate('/some/path')
    }
  
    return (
      <button onClick={handleClick}>
        Navigate to some path
      </button>
    )
  }
  ~~~

- 在类式组件中可以通过封装一个高阶组件实现

  ~~~jsx
  import { useNavigate } from 'react-router-dom'
  
  export default function withNavigate(WrappedComponent) {
    return props => {
      const navigate = useNavigate()
      return <WrappedComponent {...props} navigate={{navigate}} />
    }
  }
  ~~~

  - 类式组件传入 `withNavigate` 高阶组件后输出一个函数式组件
  - 这个函数式组件将 `navigate` 函数作为 `props` 传给了被包装的类式组件

### 路由嵌套

- `Route` 组件中可以嵌套 `Route` 组件，它们互为父路由和子路由

- 在父路由渲染的元素中，可以使用 `Outlet` 组件为子路由占位

  - 如果精确匹配了父路由，并没有指定默认的子路由，则 `Outlet` 不会渲染内容

- 示例

  ~~~jsx
  function Dashboard() {
    return (
      <div>
        <h1>Dashboard</h1>
        {/* 当 URL 为 "/messages" 时 <Outlet> 会渲染 <DashboardMessages>，为 "/tasks" 时会渲染 <DashboardTasks>，为 "/" 时 渲染 null */}
        <Outlet />
      </div>
    )
  }
  
  function App() {
    return (
      <Routes>
        <Route path="/" element={<Dashboard />}>
          <Route path="messages" element={<DashboardMessages />} />
          <Route path="tasks" element={<DashboardTasks />} />
        </Route>
      </Routes>
    )
  }
  ~~~

### 路由参数传递

- `useParams`
  - 在函数式组件中，调用 `useParams` 返回的 `params` 就是 `params` 参数列表
- `useLocation`
  - 在函数式组件中，调用 `useLocation` 返回 `location`
  - 通过 `location.search` 获取 `query` 参数字符串
- `useSearchParams`
  - 在函数式组件中，调用 `useSearchParams` 返回一个数组
  - 数组的第一个元素是 `searchParams` 对象，第二个元素是 `setSearchParams` 函数
  - `searchParams` 
    - `searchParams.get(key)` 可通过 `query` 参数的键名得到对应的值
    - 由于 `searchParams` 是实现了 `@@iterator` 方法的可迭代对象，因此可使用 `Object.fromEntries()` 将其保存的 `query` 参数的键值对提取出来生成一个普通对象
  - `setSearchParams`
    - `setSearchParams` 接收一个新的参数对象，用其合并现有的查询参数
    - 该函数调用后，组件重新渲染时，`URL` 的查询字符串也将更新

## 路由懒加载的实现

- 路由懒加载的功能可以通过原生 `React` 的 `API` 实现

- 相关 `API`

  - `React.lazy` 函数
    - 用于动态地导入一个组件
    - 接收参数
      - 接收一个函数
      - 在该函数中返回一个 `import` 函数的返回值
        - `import` 函数是一个较新的 `ECMAScript` 语法，用于动态导入，并返回一个 `Promise`
        - 比如当调用 `import(./MyModule.js)` 时
          - 得到一个 `Promise`
          - 浏览器会开始下载 `myModule.js` 文件（如果还没下载过）
          - 模块（`myModule.js`）下载完成后立即执行
          - 模执行完毕后，`Promise` 会被 `resolve`，`value` 是模块中被导出的值
    - 返回一个可懒加载的组件
    - 懒加载组件必须放在 `React.Suspense` 组件中才能使用
    - 当该模块被使用时，`React.lazy` 接收的函数会被调用，并真正下载这个模块
  - `React.Suspense` 组件
    - 用于暂缓渲染过程，并渲染一个临时内容，直到懒加载组件导入完成
    - 接收 `props`
      - `fallback`
        - 接收一个懒加载组件还未准备好前展示的 `React` 元素

- 示例

  ~~~jsx
  import React from 'react'
  
  const OtherComponent = React.lazy(() => import('./OtherComponent'))
  
  function MyComponent() {
    return (
      <React.Suspense fallback={<div>Loading...</div>}>
        <OtherComponent />
      </React.Suspense>
    )
  }
  ~~~

  
