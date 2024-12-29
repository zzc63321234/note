## webpack 中常用的 node 内置模块

### path

- `path` 模块用于对路径和文件进行处理
- 常用 `API`
  - 从路径中获取信息
    - `path.dirname(pathStr)`
      - 获取文件的父文件夹
    - `path.basename(pathStr)`
      - 获取文件名
    - `path.extname(pathStr)`
      - 获取文件扩展名
  - 路径拼接
    - `path.join([...paths])`
      - 将多个路径进行拼接
    - `path.resolve([...paths])`
      - 将多个路径拼接，并返回一个绝对路径
      - 如果遇到绝对路径片段，则从该片段往后拼接
      - 如果处理完所有给定路径片段后还没有生成绝对路径，则使用当前工作目录作为初始的绝对路径片段
      - 如果不传参，则返回当前工作目录的绝对路径
      - 生成的路径被规范化并删除尾部斜杠
      - 零长度的路径片段（空字符串）会被忽略

## webpack 的打包过程

- `webpack` 在处理应用程序时，根据命令或配置文件找到入口文件，从入口文件开始，生成一个**依赖关系图**，它会包含应用程序中所需的所有模块，包括 `js` 文件，`css`，图片，字体等
- 遍历图结构，编译 `JS` 中的模块化语法，并将不同的资源使用不同的 `loader` 来解析
- 将编译后的模块合并成一个或少量的几个文件

## 相关命令

- `webpack`
  - 打包文件
  - 默认情况下，入口文件为 `src/index.js`，输出到 `dist/main.js`
- `webpack --entry ./src/main.js --output-path ./build --output-filename bundle.js`
  - 打包文件
  - 指定入口文件为 `src/main.js`，输出到 `build/bundle.js`
- `webpack --config webpack.comm.conf.js`
  - 使用指定的 `webpack` 配置文件打包文件
  - 如不指定，默认为根目录下的 `webpack.config.js`
- `webpack --env proudction`
  - 使用生产模式执行

## webpack.config.js

~~~javascript
const path = require('path')

module.exports = {
    mode: 'development' // 指定当前的开发模式，默认值为 'production'
    context: path.resolve(__dirname, './'), // webpack 的工作目录，默认为项目根目录，配置项中可以传递的相对路径都是基于 context
    entry: './src/main.js', // 入口文件
    output: { // 输出配置
        filename: 'bundle.js', // 输出文件名
        path: path.resolve(__dirname, './dist'), // 输出路径，必须是绝对路径
        clean: true, // 每次打包前清空输出文件夹， 5.20.0+ 版本支持
        // 以下为暴露模块相关
        library: 'firstore', // 暴露的模块的变量名
        libraryTarget: 'umd', // umd 模式兼容
        umdNamedDefine: true,
        globalObject: 'this'
    }, 
    module: {
        rules: [] // 配置 loader 或 asset module type
    },
    plugins: [], // 配置 plugins
    devServer: {}, // 配置 devServer
    devtool: 'source-map' // 生成完整 source-map 文件
}
~~~

- 一般会将开发环境和生产环境的配置文件区分配置
  - `webpack.comm.conf.js`
  - `webpack.dev.conf.js`
  - `webpack.prod.conf.js`

## target

- `target` 配置项用于指定代码的运行环境。

- 支持的值包括但不限于：

  - `web`：编译为类浏览器环境里可用（默认）。
  - `node`：编译为类 `Node.js` 环境可用。
  - `electron-main`：编译为 `Electron` 主进程。
  - `electron-renderer`：编译为 `Electron` 渲染进程。

- 可以同时指定多个 `target`，`Webpack` 将生成对应平台的运行时代码，并且只使用这些平台共有的特性。例如：

  ~~~javascript
  module.exports = {
    target: ['web', 'es5'],
  };
  ~~~

## mode

- 开发模式的默认配置

  ~~~javascript
  // webpack.development.config.js
  module.exports = {
    mode: 'development',
    devtool: 'eval',
    cache: true,
    performance: {
      hints: false
    },
    output: {
      pathinfo: true
    },
    optimization: {
      namedModules: true,
      namedChunks: true,
      nodeEnv: 'development',
      splitChunks: {
        hidePathInfo: false,
        minSize: 10000,
        maxAsyncRequests: Infinity,
        maxInitialRequests: Infinity,
      },
    },
    plugins: [
      new webpack.DefinePlugin({ "process.env.NODE_ENV": JSON.stringify("development") }), 
    ]
  }
  ~~~

- 生产模式的默认配置

  ~~~javascript
  // webpack.production.config.js
  module.exports = {
    mode: 'production',
    performance: {
      hints: 'warning'
    },
    output: {
      pathinfo: false
    },
    optimization: {
      namedModules: false,
      namedChunks: false,
      nodeEnv: 'production',
      flagIncludedChunks: true,
      occurrenceOrder: true,
      sideEffects: true,
      usedExports: true,
      concatenateModules: true,
      splitChunks: {
        hidePathInfo: true,
        minSize: 30000,
        maxAsyncRequests: 5,
        maxInitialRequests: 3,
      },
      noEmitOnErrors: true,
      checkWasmTypes: true,
      minimize: true,
    },
    plugins: [
      new TerserPlugin(/* ... */),
      new webpack.DefinePlugin({ "process.env.NODE_ENV": JSON.stringify("production") }),
      new webpack.optimize.ModuleConcatenationPlugin(),
      new webpack.NoEmitOnErrorsPlugin()
    ]
  }
  ~~~


## webpack 中的路径解析

- `webpack` 使用 `enhanced-resolve` 来解析文件路径，可以从 `require`/`import` 语句中，找到需要引入的模块代码

- 可以在 `resolve` 属性中配置

- `webpack` 能解析三种文件路径

  - 绝对路径
    - 不需要进一步解析
  - 相对路径
    - 使用 `require`/`import` 语句的资源文件所在的目录，被认为是上下文目录
    - 在 `require`/`import` 语句中使用的相对路径，会拼接上下文目录的路径，生成模块的绝对路径
  - 模块路径
    - 在 `resolve.modules` 中指定的所有目录检索模块
      - 默认值是 `['node_modules']`
      - 可以在该数组替换或添加初识模块路径

- `resolve.extensions`

  - 用于配置解析到文件时自动添加的拓展名
  - 默认值是 `['.js', '.json', '.wasm']`

- `resolve.alias`

  - 用于给路径配置别名

    ~~~javascript
    {
        resolve: {
            alias: {
                '@': path.resolve(__dirname, './src')
            }
        }
    }
    ~~~

- 当解析出来的路径最后不带拓展名时

  - 如果是文件，则使用 `resolve.extensions` 中配置的拓展名解析
  - 如果是文件夹，则进入文件夹中，根据 `resolve.mainFiles` 中配置指定文件（默认值是 `['index']`）进行查找，再根据 `resolve.extensions` 解析拓展名

## loader

- `loader` 用于对特定的模块类型进行转换

###  配置方式

- `module.rules` 对应的值是一个数组，数组中存放一个个的 `Rule` 对象，对象中的属性：
  - `test`
    - 用于匹配要处理的资源，通常会设置成正则表达式
  - `use`
    - 对应的值是一个数组，数组中存放一个个 `UseEntry` 对象，对象中的属性：
      - `loader`
        - 使用的`loader`，对应的值是一个字符串
      - `options`
        - 可选的选项属性，值是一个字符串或对象，会传入 `loader` 中
      - `query`
        - 已被 `options` 代替
    - `UseEntry` 也可以传递字符串，用以当不需要传递 `options` 时简写
      - 如：`use: [ 'style-loader' ]` 等价于 `use: [{ loader: 'style-loader' }]`
    - `use` 中多个 `loader` 的使用顺序是从后往前
  - `loader`
    - 用以当 `Rule` 中只需要使用一个 `loader` 时，对 `use` 属性的简写
    - 如 `use: [ 'style-loader' ]` 等价于 `loader: 'style-loader'`

### 样式文件处理类的loader

- `style-loader`、`css-loader`、`less-loader` 等

  - `style-loader` 用以将样式文件插入页面中
  - `css-loader` 用于读取 `css` 文件
  - `less-loader` 用于将 `less` 文件转换为 `css` 文件
  - 按此顺序配置

- `postcss-loader`

  - 可以通过插件进行一些 `css` 的转换和适配，如自动添加浏览器前缀，`css` 样式重置等

  - `postcss-loader` 需要对应的插件才会起效果，如 `autoprefixer` 用于自动添加浏览器前缀

    ~~~shell
    npm i autoprefixer -D
    ~~~

    - 可以在 `UseEntry.options` 中配置插件

      ~~~javascript
      {
          loader: 'postcss-loader',
          options: {
              postcssOptions: {
                  plugins: [ require('autoprefixer') ]
              }
          }
      }
      ~~~

    - 也可以在根目录下创建 `postcss.config.js` 中配置插件

      ~~~javascript
      // postcss.config.js
      module.exports = {
          plugins:[ require('autoprefixer') ]
          // 某些 postcss 插件可以直接在 plugins 数组中传入字符串，如 postcss-preset-env
      }
      ~~~

  - `postcss-preset-env` 插件内置了一些常用的 `postcss` 插件，包括 `autoprefixer`，可以将一些现代 `CSS` 特性，转为大多数浏览器认识的 `CSS`

## 资源模块类型

- 资源模块类型(`asset module type`)，用于替代 `webpack 5` 之前的一些 `loader`，共有4中模块类型：

  - `asset/resource`
    - 输出资源至指定路径
    - 之前通过 `file-loader` 实现
  - `asset/inline`
    - 导出资源的 `data URI` （`base64` 编码）
    - 之前通过 `url-loader` 实现
  - `asset`
    - 在导出资源的 `date URI` 和输出资源至指定路径之间自动选择
    - 之前通过 `url-loader`，并配置资源体积限制实现
  - `asset/source`
    - 导出资源的源代码
    - 之前通过 `raw-loader` 实现

- 使用示例

  - 使用 `asset/resource`

    - 在 `module.rules` 中配置

      ~~~javascript
      {
          module: {
              rules: [
                  {
                      test: /\.(png|svg|jpe?g|gif)$/i
                      exclude: /node_modules/
                      type: 'asset/resource'
                      generator: { // 自定义输出的文件名
                          filename: 'img/[name].[hash6][ext]'
                      }
                  }
              ]
          }
      }
      ~~~
  
    - 另外，也可以在 `output` 中添加 `assetModuleFilename` 属性以统一设置 `asset module type` 的自定义输出的文件名
  
      ~~~javascript
      {
          output:{
              filename: 'js/bundle.js'
              path: path.resolve(__dirname, './dist')
              assetModuleFilename: 'img/[name].[hash:6][ext]'
          }
      }
      ~~~
  
    - 几个常用的 `placeholder`
  
      - `[ext]` 被处理的文件的拓展名
      - `[name]` 被处理的文件的名称
      - `[hash]` 被处理的文件的内容，使用 `MD4` 的散列函数处理，生成的一个128位的 `hash` 值（表达成32个十六进制），支持 `[xxx:length]` 语法，默认 `length` 为20
  
  - 使用 `asset`
  
    - 在 `module.rules` 中配置
  
      ~~~javascript
      {
          module: {
              rules: [
                  {
                      test: /\.(png|svg|jpe?g|gif)$/i
                      type: 'asset'
                      generator: { 
                          filename: 'img/[name].[hash:6][ext]'
                      },
                      paser: {
                          dataUrlCondition: {
                              maxSize: 100 * 1024 // 单位是 byte
                          }
                      }
                  }
              ]
          }
      }
      ~~~

## plugin

- `plugin` 用于执行比 `loader` 更广泛的任务，如打包优化、资源管理、环境变量注入等

### 一些插件

- `CleanWebpackPlugin`

  - 用于每次重新打包时，自动清除 `dist` 文件夹

  - 在 `webpack 5.20.0+` 中可通过将 `output.clean ` 设为 `true` 来替代实现该插件的功能

  - 使用：

    ~~~shell
    npm i clean-webpack-plugin -D
    ~~~

    ~~~javascript
    const { CleanWebpackPlugin } = require('clean-webpack-plugin')
    
    module.exports = {
        plugins: [
            new CleanWebpackPlugin()
        ]
    }
    ~~~

- `HtmlWebpackPlugin`

  - 用于生成打包后的 `html` 文件

  - 使用

    ~~~shell
    npm i html-webpack-plugin -D
    ~~~

    ~~~javascript
    const HtmlWebpackPlugin = require('html-webpack-plugin')
    
    module.exports = {
        plugins: [
            new HtmlWebpackPlugin({
                filename: 'index.html', // 生成的文件名
                title: 'Hello World', // 为生成的 HTML 页面配置标题内容
                template: path.resolve(__dirname, 'src/index.html') // 模板文件的绝对路径
                templateContent: `
                    <html>
                      <body>
                          <h1>Hello World</h1>
                      </body>
                    </html>
                ` // 指定 template 内容，优先级高于 template 属性
            })
        ]
    }
    ~~~

  - 该文件是根据 `ejs` 的一个模板生成的

  - 生成的 `html` 中会有类似这样的语法 `<%= 变量名 %>`，这是 `EJS` 模块填充数据的方式

- `DefinePlugin`

  - 用于在编译时创建配置的全局变量

  - 是 `webpack` 的一个内置插件

  - 使用

    ~~~javascript
    const { DefinePlugin } = require('webpack')
    
    module.exports = {
        plugins: [
            new DefinePlugin({
                'Base_URL': "'./'", // 变量配置时传入的值为一个字符串，使用变量时字符串的内容会被作为 JS 表达式读取，如果需要使用字符串本身，则需要在字符串中再加上引号
                'process.env.NODE_ENV': JSON.stringify('production') // 也可以使用 JSON.stringify 方法将 JS 值转化为一个 JSON 字符串格式，等同于 "'production'"
            })
        ]
    }
    ~~~
  
  - 创建的全局变量可以在 `EJS` 模块中使用
  
  - 在 `mode` 配置项为 `development` 或 `production` 时，默认会使用 `DefinePlugin` 为全局注入 `process.env.NODE_ENV` 变量，值与当前 `mode` 对应


## webpack-dev-server

- `webpack-dev-server` 可用于开启一个本地服务器

- ~~~shell
  npm i webpack-dev-server -D
  ~~~

- 使用方式：

  - 命令行执行 `webpack serve`

- `webpack-dev-server` 在编译后不会输出任何文件，而是将打包后的文件保留在内存中

### 热模替换(HMR)

- `HMR` 全称 `Hot Module Replacement`，指在应用程序运行过程中，替换、添加、删除模块，而无需刷新整个页面

- `webpack-dev-server` 支持并默认开启了 `HMR` 

- `HMR` 的基本实现方式

  - 在 `webpack.config.js` 中配置

    ~~~javascript
    {
        devServer: {
            hot: true // 默认已开启
        }
    }
    ~~~

  - 在入口 `js` 文件中指定需要热模替换的模块

    ~~~javascript
    if (module.hot) {
        module.hot.accept('./util.js', ()=>{
            console.log('util更新了')
        })
    }
    ~~~

- 框架中的 `HMR`

  - 如开发 `Vue`、`React` 项目时，会使用 `vue-loader`，`react-refresh`  等实现 `HMR`

### 配置信息

~~~javascript
{
    devServer: {
        host: '0.0.0.0', // 主机地址，默认值是 'localhost'
        port: 8888, // 开启的服务器的端口号，默认 8080
        open: true, // 开启服务器时是否自动打开浏览器，默认为 false
        compress: true // 是否对静态文件进行压缩，默认为 false
}
~~~

- `host`
  - `localhost` 和 `0.0.0.0` 的区别
    - `localhost` 本质上是一个域名，通常情况下会被解析成 `127.0.0.1`
    - `127.0.0.1`，即回环地址（`Loop Back Address`），表达的意思是主机发出去的包由自己接收
      - 正常的数据包经过 `应用层` - `传输层` - `网络层` - `数据链路层` - `物理层`
      - 而回环地址在网络层被获取到，不会经过数据链路层和物理层
    - `0.0.0.0`
      - 监听 `IPV4` 上所有的地址，根据端口找到不同的应用程序
      - 监听 `0.0.0.0` 时，在同一网段下的主机中，通过 `IPV4` 地址可以访问到主机

## source-map

- `source-map` 可将已转换的代码，映射到原始的源文件，使得浏览器可以重构原始源并在调试器中显示

- `source-map` 的通常实现

  - `webpack` 打包时，根据源文件生成对应的 `source-map` 文件

  - 在转换后的代码末尾处有一个注释，指向了 `source-map` 文件

    `//# sourceMappingURL=xxx.js.map`

  - 浏览器会根据注释，查找相应的 `source-map`，以此还原代码

- `source-map` 本质上是一个 `json` 对象，包含以下信息

  - `version`
    - 当前使用的 `source-map` 版本
  - `sources`
    - 源文件目录
  - `sourceContent`
    - 转换前的具体代码信息
  - `names`
    - 转换前的变量和属性名称
  - `mappings`
    - `source-map` 用来和源文件映射的信息（如位置信息等）
    - 是一串 `base64 VLQ` （`Variable-length quantity` 可变长度值）编码
  - `file`
    - 打包后的文件名

  - `sourceRoot`
    - 所有的 `sources` 相对的根目录

- 通过 `devtool` 配置项可指定 `webpack` 中生成 `source-map` 的方式

  - [Devtool | webpack 中文文档 (docschina.org)](https://webpack.docschina.org/configuration/devtool/)

  - 选择不同的方式，生成的 `source-map` 会有差异，打包过程也有性能差异，可以根据不同情况进行选择

  - 以下值不会生成 `source-map`

    - `none`
      - 不生成 `source-map`
      - `production` 模式和 `none` 模式下的默认值
    - `eval`
      - 不生成 `source-map` 文件
      - 打包时会将代码放入 `eval()` 中执行，并添加 `//# sourceURL=xxx`，使得浏览器解析时能在调试面板中生成对应的一些文件目录，准确性不及 `source-map` 文件
      - `development` 模式下的默认值

  - 一些常见的值

    - `source-map`
      - `source-map` 的通常实现方式
      
      - 生成一个完整的 `source-map` 文件
      - 通过 `//# sourceMappingURL=xxx.js.map` 引入到打包文件的末尾处
      
    - `eval-sourc-map`
    
      - 结合了 `eval` 和 `source-map`
      - 将生成的 `source-map` 以 `DataUrl` 的方式添加到 `eval()` 中的末尾处
    
    - `inline-source-map`
      - 将生成的 `source-map` 以 `DataUrl` 的方式添加到打包文件的末尾处
      
    - `cheap-source-map`
    
      - `cheap` 指低开销
    
      - 生成的 `source-map` 只包含行信息，不包含列信息
    
    - `cheap-module-source-map`
      - 类似于 `cheap-source-map`
      - `cheap-module-source-map` 生成的 `source-map` 对于使用 `loader` 处理过的文件的源文件能更好地进行还原（能还原处理前代码中的换行符、空格等信息）
      
    - `hidden-source-map`
    
      - 会生成 `source-map` 文件，但不会自动将其引入打包文件中
    
    - `nosources-source-map`
    
      - 生成的 `source-map` 只能还原错误信息提示，不能还原源代码
    
  - 多种方式组合规则
  
    - \[`inline-`|`hidden-`|`eval-`]\[`nosources-`\]\[`cheap-`\[`module-`\]\]`source-map`
  
  - 不同阶段推荐的方式
  
    - 开发阶段/测试阶段
      - `source-map` 或 `cheap-module-source-map`
      - 这分别是 `Vue` 和 `React` 使用的值，可以获取调试信息，方便快速开发
    - 发布阶段
      - `none`

## babel

- `babel` 是一个工具链，主要用于旧浏览器或者环境中将 `ECMAScript 2015+` 代码转换为向下兼容版本的 `JavaScript`，包括：语法转换、源代码转换、`polyfill` 实现目标环境缺少的功能等
- `babel`可以作为独立工具使用，也可以集成在 `webpack` 中使用

### babel 的原理

- 可以将 `babel` 看作是一个编译器
- 编译器将一种源代码（原生语言）转换为另一种源代码（目标语言）
- `babel` 拥有编译器的工作流程
  - 解析阶段（`Parsing`）
  - 转换阶段（`Transformation`）
  - 生成阶段（`Code Generation`）

### 命令行使用 babel

- 需安装
  - `@babel/core`
    - `babel` 的核心代码
  - `@babel/cli`
    - 使得可以在命令行使用 `babel`
- 常用指令
  - `npx babel src --out-dir dist`
    - `src` 处指定源文件的目录
    - `--out-dir dist` 处指定要输出的目录
  - `npx babel src --out-dir dist --plugins=xxx --presets=xxx`
    - `--plugins=xxx` 处指定要使用的插件
    - `--presets=xxx` 处指定要使用的预设

### 在 webpack 中使用 babel

- 安装 `babel-loader` 并配置到 `webpack.config.js` 中

- `@babel/core` 会作为 `babel-loader`  的`peerDependencies` 自动被安装

-  `webpack.config.js` 中配置 `loader`

  ~~~javascript
  module: {
      rules: [
          test: /\.js$/i,
          use: [{
              loader: 'babel-loader',
              options: {
                  plugins: [] // 配置插件
                  presets: [] // 配置预设
              }
          }]
      ]
  }
  ~~~

- `babel` 的相关配置可以写在根目录的 `babel.config.js` 中

  ~~~javascript
  // babel.config.js
  module.exports = {
      plugins: [
          // 单独配置安装的 babel 插件
      ],
      presets: [
          // 配置 babel 的预设
          '@babel/preset-env'
      ]
  }
  ~~~

### babel 预设

- `babel` 预设可以将预设的插件列表批量传递给 `babel`
- 比较常见的预设有 `@babel/preset-env`、`@babel/preset-react`、`@babel/preset-TypeScript` 等

### babel 配置文件

- `.babelrc` / `.babelrc.json` / `.babelrc.js`

  - 早期使用的配置方式

- `babel.config.json` / `babel.config.js`

  - `babel 7` 之后推荐使用的配置方式
  - 可以直接作用于 [`Monorepos`项目](https://juejin.cn/post/6944877410827370504) 的子包

- 示例

  ~~~javascript
  // babel.config.js
  module.exports = {
      plugins: []
      presets: [
          ['@babel/preset-env', {
              ......
          }]
      ]
  }
  ~~~

## browserslist

- `browserslist` 是一个在不同的前端工具之间，共享目标浏览器和 `Node.js` 版本的配置，适用于
  - `Autoprefixer`
  - `babel`
  - `postcss-preset-env`
  - `eslint-plugin-compat`
  - `stylelint-no-unsupported-browser-features`
  - `postcss-normalize`
  - `obsolete-webpack-plugin`

- 查询原理
  - 根据编写的配置来获取相关的浏览器信息，决定是否需要进行兼容性的支持
  - 条件查询使用的是 `caniuse-lite` 的工具，其数据来自于 `caniuse` 网站

### 查询条件

- 可编写的条件

  - `defaults`

    - 默认配置
    - `> 0.5%, last 2 versions, Firefox ESR, not dead`

  - 百分比**（常用）**

    - 通过全局使用情况统计信息选择的浏览器版本

    - 可以在开头加上 `>`、`>=`、`<`、`<=`
    - 可以跟随内容，例：
      - `> 5%`：全球使用情况统计选择的浏览器版本。 `>=`，`<`也`<=`工作
      - `> 5% in US`：使用美国使用情况统计，它接受[两个字母的国家/地区代码](https://links.jianshu.com/go?to=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FISO_3166-1_alpha-2%23Officially_assigned_code_elements)
      - `> 5% in alt-AS`：使用亚洲地区使用情况统计，可在以下位置找到所有地区代码的列表[caniuse-lite/data/regions](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Fben-eb%2Fcaniuse-lite%2Ftree%2Fmaster%2Fdata%2Fregions)
      - `> 5% in my stats`：使用[自定义使用数据](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Fbrowserslist%2Fbrowserslist%23custom-usage-data)
      - `cover 99.5%`：提供覆盖的最流行的浏览器
      - `cover 99.5% in US`：与上述相同，使用[双字母国家代码](https://links.jianshu.com/go?to=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FISO_3166-1_alpha-2%23Officially_assigned_code_elements)
      - `cover 99.5% in my stats`：使用[自定义使用数据](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Fbrowserslist%2Fbrowserslist%23custom-usage-data)

  - `dead` **（常用）**

    - 24个月内没有官方支持或更新的浏览器

  - `last 2 versions` **（常用）**

    - 每个浏览器的最后2个版本
    - 也可以指定浏览器的最新版本，如：
      - `last 2 Chrome versions`：最近2个版本的 `Chrome` 浏览器
      - `last 2 major versions` / `last 2 iOS major versions`：最近 2 个主要版本的所有次要 补丁版本

  - `node` 版本，例：

    - `node 10`：选择最新的 `Node.Js 10.x.x` 版本
    - `node 10.4`：选择最新的 `Node.Js 10.4.x` 版本
    - `current node`：`browserslist` 当前环境使用的 `Node.js` 版本
    - `maintained node versions`：所有 `Node.js` 版本

  - 指定浏览器版本，例：

    - `Firefox > 20`：选择 `Firefox 20` 以上的版本，也可使用 `>`、`>=`、`<`、`<=`
    - `ie 6-8`：选择一个包含范围的版本
    - `Firefox ESR`：选择最新的 `Firefox ESR` 版本
    - `PhantomJS 2.1` / `PhantomJS 1.9`：选择类似于 `PhantomJS` 运行时的 `Safari`

  - `extends browserslist config mycompany`：从 `browserslist config mycompanynpm` 包中查询

  - `supports es6 module`：选择支持特定功能的浏览器

    - `es6 module` 是 `caniuse` 页面 `feat` 的 `URL` 上的参数，有关所有可用功能的列表，见  `caniuse lite/data/features`

  - `browserslist config`：在 `browserslist` 配置中定义的浏览器

    - 在差异服务中很有用，可用于修改用户的配置，例如 `browserslist config and supports es6 module`

  - `since 2015`：自2015年以来发布的所有版本

  - `last 2 years`：近两年发布的所有版本

  - `unreleased versions` /  `unreleased Chrome versions`：`Alpha` 和 `beta` 版本

  - 排除条件，例：

    - `not ie <= 8`

- 条件的组合

  - 使用 `,` / `or`
    - 表示“或”
  - 使用 `and`
    - 表示“与”
  - 使用 `not`
    - 表示“非”

### 使用 browserslist

- 命令行使用 `browserslist`

  - 可以直接通过命令来查询由条件匹配到的浏览器

    `npx browserslist "> 0.1%, last 2 version, not dead"`

- 在文件中配置 `browserslist`

  - 通过 `package.json` 配置

    ~~~json
    {
        "browserslist": [
            "> 0.1%",
            "last 2 version",
            "not dead"
        ]
    }
    ~~~

  - 通过 `.browserslistrc` 配置

    ~~~.browserslistrc
    > 0.1%
    last 2 version
    not dead
    ~~~

  - 通过 `@babel/preset-env` 配置项中的 `targets` 配置

    ~~~javascript
    module.exports = {
        presets: [
            ['@babel/preset-env', {
                targets: '> 5%'
            }]
        ]
    }
    ~~~

    - `targets` 中配置的查询条件会覆盖 `browserslist`，但不会在多个前端工具中共享

## polyfill

- `polyfill` 用于为代码打上一种补丁，使得代码中的一些 `ES` 新特性可以在原本不支持环境下运行
- 引入 `polyfill`
  - `babel 7.4.0` 之前
    - 使用 `@babel/polyfill` （由 `core-js 2.x.x` 和 `regenerator-runtime` 组成）
  - `babel 7.4.0` 之后
    - 分别引入 `core-js` 和 `regenerator-runtime`
- 使用 `polyfill`
  - 为 `preset-env` 配置一些属性
    - `useBuiltIns`
      - 设置以什么样的方式来使用 `polyfill`，有以下常见的值
        - `false`
          - 不适用 `polyfill` 进行适配
        - `"usage"`
          - 会根据源代码中出现的语言特性，自动检测所需要的 `polyfill`
        - `"entry"`
          - 导入入口文件引入的所有 `polyfill`
          - 需在入口文件中引入 `'core-js/stable'` 和 `'regenerator-runtime/runtime'`
    - `corejs`
      - 设置 `corejs` 的版本 

## 补充

- `production` 模式下，通过配置 `optimization.usedExpots` 为 `false` 可关闭 `tree-shaking`，适用于打包工具库的情况。
