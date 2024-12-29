## 项目搭建规范

### 统一编码 - `EditorConfig`

- `EditorConfig` 有助于为不同 `IDE` 编辑器上处理同一项目的多个开发人员维护一致的编码风格

- 使用方式

  - 在项目根目录下配置 `.editorconfig` 文件，配置参考

    ~~~ini
    # http://editorconfig.org
    
    # 表示这里是项目根目录，会停止 editorconfig 继续向上级查找
    root = true
    
    [*] # 表示所有文件适用
    # 设置文件字符集为 utf-8
    charset = utf-8
    # 缩进风格（tab | space）
    indent_style = space
    # 缩进大小
    indent_size = 2
    # 控制换行类型(lf | cr | crlf)
    end_of_line = lf
    # 去除行尾的任意空白字符
    trim_trailing_whitespace = true
    # 始终在文件末尾插入一个新行
    insert_final_newline = true
    
    [*.md] # 表示仅 md 文件适用以下规则
    max_line_length = off
    trim_trailing_whitespace = false
    ~~~

  - 在 `VSCode` 下，可以使用插件 `EditorConfig for VS Code` 使配置生效

### 代码格式化 - `Prettier`

- `Prettier` 是一款强大的代码格式化工具，支持 `JavaScript`、`TypeScript`、`CSS`、`SCSS`、`Less`、`JSX`、`Angular`、`Vue`、`GraphQL`、`JSON`、`Markdown` 等语言，基本上前端能用到的文件格式它都可以搞定

- 使用 `create-vue` 创建项目时可以选择是否预装 `prettier`，且可以自行生成配置文件

- 使用方式

  - 安装 `prettier`

    ~~~bash
    npm install prettier -D
    ~~~

  - 在项目根目录下配置 `.prettierrc` 文件，配置参考

    ~~~json
    {
      "useTabs": false, // 是否使用 tab 缩进，否则使用空格缩进
      "tabWidth": 2, // 使用空格缩进时，几个空格代表一个缩进
      "printWidth": 80, // 当一行超过多少字符时会尝试进行换行表示
      "singleQuote": true, // 是否使用单引号，否则使用双引号
      "trailingComma": "none", // 多行输入的末尾是否加逗号
      "semi": false // 语句结尾是否加分号
    }
    ~~~

  - 也可以配置 `.prettierignore` 忽略文件

    ~~~.ignore
    /dist/*
    .local
    .output.js
    /node_modules/**
    
    **/*.svg
    **/*.sh
    
    /public/*
    ~~~

  - 在 `VSCode` 下可以使用 `Prettier - Code formatter` 插件，并将文件的格式化程序指定为 `prettier` 提供的格式化程序（可以指定 `VSCode` 配置中的 `editor.defaultFormatter` 为 `Prettier - Code formatter`

### 代码规范检测 - `ESLint`

- `ESLint` 是一个 `JavaScript` 代码检查工具，用以发现代码错误、统一代码风格和规范

- 使用 `create-vue` 创建项目时可以选择是否预装 `ESLint`，且可以自行生成配置文件

- 使用方式

  - `VSCode` 下可使用 `ESLint` 插件

  - 在代码根目录下配置 `.eslintrc.cjs` 文件

  - 如何避免 `ESLint` 中的规范和 `prettier` 中的配置冲突

    - 安装 `eslint-plugin-prettier` 和 `eslint-config.prettier`

      ~~~bash
      npm install eslint-plugin-prettier eslint-config.prettier -D
      ~~~

    - 如果使用 `create-vue` 创建项目时先择了 `prettier`，那么这两个插件会被预装

    - 在 `.eslintrc.cjs` 中加入

      ~~~javascript
      /* eslint-env node */
      require('@rushstack/eslint-patch/modern-module-resolution')
      
      module.exports = {
        root: true,
        'extends': [
          'plugin:vue/vue3-essential',
          'eslint:recommended',
          '@vue/eslint-config-typescript',
          '@vue/eslint-config-prettier/skip-formatting'
          // 加入以下这行，使得 ESLint 会同步 prettier 中的规则
          'plugin:prettier/recommended'
        ],
        parserOptions: {
          ecmaVersion: 'latest'
        }
        // rules 用于配置规则
        rules: {
          '@typescript-eslint/no-unused-vars': 'off'
          // https://typescript-eslint.io/rules/ 规则列表
        }
      }
      ~~~
      

## 创建 `Vue` 项目

- 方式一：`Vue CLI`
  - 基于 `webpack` 工具
  - 命令 `vue create`
- 方式二：`create-vue`
  - 基于 `vite` 工具
  - 需要 `Node 16` 版本以上
  - 命令 `npm init vue@latest` 
    - 该命令会自动执行 `npm init create-vue`
    - 如果没有安装 `create-vue` 会自动提示

## `TS` 支持

- `create-vue` 创建的项目环境中，可以选择 `TS` 支持

- `env.d.ts` 中用于定义全局类型声明

  - 默认已经配好了大多数模块的声明

  - `*.vue` 文件的声明并没有配置

    - 如果在 `VSCode` 环境下应用了 `TypeScript Vue Plugin (Volar)` 插件，则会帮助编辑器识别 `*.vue` 文件中导入的模块

    - 也可以自行在 `env.d.ts` 中声明

      ~~~typescript
      declare module '*.vue' {
        import { type DefineComponent } from 'vue'
        const component: DefineComponent
        export default component
      }
      ~~~

      - 声明之后，项目的 `ts` 文件中会将所有从 `.vue` 文件导入的模块定义为 `DefineComponent` 类型，但是这样即使导入了一个不存在的 `.vue` 文件，编辑器也不会发现并报错

## 样式配置

### `CSS` 样式重置

- `normallize.css`

  - 用于将各浏览器不同的默认样式设置为一致

  - 使用方式

    - 安装重置样式

      ~~~shell
      npm install normalize.css
      ~~~

    - 在 `src/assets/main.css` 中引入 `normalize.css`

      ~~~css
      @import "normalize.css"
      ~~~

- 也可以自定义一个样式重置 `css` 文件

  ~~~css
  html, body, div, span, applet, object, iframe,
  h1, h2, h3, h4, h5, h6, p, blockquote, pre,
  a, abbr, acronym, address, big, cite, code,
  del, dfn, em, img, ins, kbd, q, s, samp,
  small, strike, strong, sub, sup, tt, var,
  b, u, i, center,
  dl, dt, dd, ol, ul, li,
  fieldset, form, label, legend,
  table, caption, tbody, tfoot, thead, tr, th, td,
  article, aside, canvas, details, embed, 
  figure, figcaption, footer, header, hgroup, 
  menu, nav, output, ruby, section, summary,
  time, mark, audio, video {
      margin: 0;
      padding: 0;
      border: 0;
      font-size: 100%;
      font: inherit;
      vertical-align: baseline;
  }
  /* HTML5 display-role reset for older browsers */
  article, aside, details, figcaption, figure, 
  footer, header, hgroup, menu, nav, section {
      display: block;
  }
  body {
      line-height: 1;
  }
  ol, ul {
      list-style: none;
  }
  blockquote, q {
      quotes: none;
  }
  blockquote:before, blockquote:after,
  q:before, q:after {
      content: '';
      content: none;
  }
  table {
      border-collapse: collapse;
      border-spacing: 0;
  }
  ~~~

### 配置样式文件预处理器（`less`）

- 安装 `less`

  ~~~bash
  npm install less -D
  ~~~

## 环境变量

### `Webpack` 中的环境变量

- `Webpack` 的 `DefinePlugin` 插件允许在构建时创建全局变量

  ~~~javascript
  // webpack.config.js
  const webpack = require('webpack')
  
  module.exports = {
    // ...
    plugins: [
      new webpack.DefinePlugin({
        'process.env.NODE_ENV': JSON.stringify('production')
      })
    ]
  }
  ~~~

  - 以上配置会使得 `DefinePlugin` 在构建时将代码中的 `process.env.NODE_ENV` 替换为字符串 `'production'`
  - 注意，这么做是构建时将代码中的 `process.env.NODE_ENV` 标识符替换为 `'production'`，并没有改变在运行时环境中的 `process.env` 对象

- 也可以指定 `webpack` 配置对象中的 `mode` 配置为 `’production‘` 或 `'development'`，这将自动配置 `DefinePlugin` 插件将全局的 `process.env.NODE_ENV` 标识符替换为对应的字符串

### `Vite` 中的环境变量

- `Vite` 中可以通过 `import.meta.env` 对象来访问环境变量

- 通过在项目根目录使用 `.env` 文件来定义 `import.meta.env` 中的环境变量

  - `.env` 文件命名

    - `.env` 中存放所有模式下的环境变量

    - 此外，也可以创建文件名格式为 `.env.[mode]` 的文件

      - 例如 `.env.development` 文件中定义的环境变量会在 `mode` 为 `development` 时生效；`.env.production` 文件中定义的环境变量会在 `mode` 为 `production` 时生效

      - 在开发或生产构建中指定相应的 `mode`，`Vite` 会自动加载对应的环境变量

        ~~~bash
        # 开发模式
        vite --mode development
        
        # 生产模式
        vite build --mode production
        ~~~

    - 文件加载规则

      ~~~
      .env               # 所有情况下都会加载
      .env.local         # 所有情况下都会加载，但会被 git 忽略
      .env.[mode]        # 只在指定模式下加载
      .env.[mode].local  # 只在指定模式下加载，但会被 git 忽略
      ~~~

  - 环境变量命名

    - 以 `VITE_` 开头为命名的环境变量会被 `Vite` 自动暴露到 `import.meta.env` 对象中

      ~~~.env
      VITE_HI='Hello Vite'
      ~~~

- `import.meta.env` 包括几个内置的属性

  - `BASE_URL`
    - 项目生产中的服务器地址
  - `MODE`
    - 应用运行的模式
  - `DEV`
    - 布尔值，表示应用是否运行在开发环境（与 `PROD` 永远相反）
  - `PROD`
    - 布尔值，表示应用是否运行在生产环境（与 `DEV` 永远相反）
  - `SSR`
    - 布尔值，表示应用是否运行在服务端

## 如何获取 `vue` 组件实例的类型

- 使用 `ref` 获取到的组件实例，比如 `MyComponent` 组件的实例，其类型是 `InstanceType<typeof MyComponent>`

## 动态注册路由的实现方案

根据用户不同的权限，而注册不同的路由。

### 基于角色（`role`）的动态路由管理

基于角色的动态路由管理，是根据不同的用户 `role` 而注册路由。如何匹配 `role` 和路由在前端进行。

弊端：如果需要新增角色，前端需要重新发布代码。

### 基于菜单（`menu`） 的动态路由管理

基于菜单的动态路由管理，是根据获取到的不同 `menu` 映射成路由。