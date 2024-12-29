# webpack4

## 概念

#### 简介

- webpack是一个模块打包器/构建工具
- 在webpack中会将前端的所有资源文件都作为模块处理
- 它将根据模块的依赖关系进行分析，生成对应的资源

#### 核心概念

##### 入口(entry)

指示webpack应该使用哪个模块，来作为构建内部依赖图的开始

##### 输出(output)

在哪里输出文件，以及如何命名这些文件

##### Loader

用来处理非JavaScript文件

- webpack自身只能解析JavaScript和json，如果要加载其它类型的文件（模块），就需要使用对应的loader
- loader本质是函数，接受源文件作为参数，返回转换的结果
- loader一般以xxx-loader的方式命名，xxx代表了这个loader要做的转换功能，如css-loader

##### 插件(plugins)

执行范围更广的任务，从打包到优化都可以实现

- 插件可以完成一些loader不能完成的功能

##### 模式(mode)

分为生产模式production和开发模式development

## webpack在开发环境中的使用

#### webpack安装

- 全局安装

  作为指令使用

  ~~~shell
  npm install webpack@4 webpack-cli@3 -g
  ~~~

- 本地安装

  作为本地开发依赖使用

  ~~~shell
  npm install webpack@4 webpack-cli@3 -D
  ~~~

#### 处理js和json文件

- 创建js文件、json文件、主页面

  ~~~markdown
  - src
      - js
          - app.js <!-- app.js是入口文件，所有外部文件都需要在这里引入使用 -->
          - module1.js
          - module2.js
      - json
          - data.json
      - index.html
  ~~~

- 运行指令

  - 打包指令（开发模式）

    - 功能：打包js和json文件，并将es6的模块化语法转换成浏览器能识别的语法（不转换其它ES6语法）

    ~~~shell
    webpack ./src/js/app.js -o ./build/js/app.js --mode=development
    ~~~

  - 打包指令（生产模式）

    - 功能：在开发模式的功能上增加一个压缩代码的功能

    ~~~shell
    webpack ./src/js/app.js -o ./build/js/app.js --mode=development
    ~~~

  - 在index.html中引入 ../build/js/app.js

#### webpack配置文件

- 在项目根目录定义配置文件webpack.config.js

- 所有的webpack任务，用到的loader、plugins都在该文件进行配置

- 该文件要符合CJS模块化规范

- 文件内容

  ~~~javascript
  const { resolve } = require('path') // 引入node内置模块path，用来设置路径
  
  // 使用CJS模块化规范，暴露webpack的详细配置对象（规则）
  module.exports = {
      mode: 'development', // 'development'（开发模式）或'production'（生产模式）
      entry: './src/js/app.js', // 入口文件配置（精简写法）
      /* 完整写法：
          entry:{
              main:'./src/js/app.js'
          }
      */
      output: { // 输出配置
          path: resolve(__dirname, 'build'), // 输出文件路径(绝对路径)
          filename: './js/app.js' // 输出文件名
      },
  
      // ------ 以下为module完整写法示例，可根据实际需要进行简写，详见具体loader配置示例
      module: { // 配置loader的完整写法
          rules: [ // 配置规则
              {
                  test: /\.(jpg|png|gif)$/i, // 使用正则表达式匹配文件名，必须
                  use: [ // 使用到的loader，可根据情况精简
                      // 如果只需要配置一个loader，可省略use，将use数组中的对象的内容放到上一层
                      // 如果配置多个loader且不需要配置options，则可直接将loader名称字符串写在use数组中
                      {
                          loader: 'url-loader',
                          options: {
                              name: '[hash:10].[ext]',
                              outputPath: './imgs',
                              limit: 8 * 1024
                          }
                      }
                  ]
              }
          ]
      },
      // ------------------------------------------------------------------------------
      plugins: [], // 配置插件，插件需先使用CJS模块化规范引入，再将其实例化后配置
      devServer: {} // 配置devServer
  }
  ~~~

- 定义完配置文件后，在命令行运行指令webpack，即可根据配置文件的内容进行打包

#### 打包样式表文件资源到入口js文件中

- webpack需要借助loader编译解析css、less文件

- 创建样式表文件

  ~~~markdown
  - src
      - css
          - test1.css
          - test2.less
  ~~~

- 在入口文件app.js中引入样式表文件

  ~~~javascript
  import '../css/test1.css'
  import '../css/test2.less'
  ~~~

- 安装loader（对应webpack 4.46.0版本）

  - style-loader@2.0.0
  - css-loader@5.2.7
  - less-loader@7.3.0
  - less
  - 需在github上查看loader的package.json文件中的peerDependencies属性，以安装对应webpack版本号的loder版本

- 配置loader

  ~~~javascript
  module: {
      rules: [
          { // 处理css资源
              test: /\.css$/i, // 正则匹配需要处理的文件
              use: [ // 使用到的loader，后配置的先执行
                  'style-loader', // 用以创建style标签，将js中的样式资源插入，并添加到页面head标签中
                  'css-loader' // 用以将css文件变成CJS模块加载到js中，内容是样式字符串
              ]
          },
          { // 处理less资源
              test: /\.less/i, 
              use: [
                  'style-loader',
                  'css-loader',
                  'less-loader' // 用以将less文件编译成css文件，需安装less模块
              ]
          }
      ]
  }
  ~~~

  - 如果不同的规则需要用到相同的loader，可以使用变量存储配置的代码进行复用

    ~~~javascript
    const baseCssLoader = ['style-loader', 'css-loader']
    ~~~

    ~~~javascript
    module: {
        rules: [
            { // 处理css资源
                test: /\.css$/i, 
                use: [...baseCssLoader]
            },
            { // 处理less资源
                test: /\.less/, 
                use: [...baseCssLoader, 'less-loader']
            }
        ]
    }
    ~~~

#### 打包html文件

- 借助html-webpack-plugin插件打包html资源

- 创建html文件

  ~~~markdown
  - src
      - index.html
  ~~~

- 安装html-webpack-plugin@4.5.2

- 配置插件

  ~~~javascript
  const htmlwebpackPlugin = require('html-webpack-plugin')
  ~~~

  ~~~javascript
  plugins: [
      new htmlwebpackPlugin({
          // 以现html源文件为模板创建新的html，会自动引入打包的资源
          template: './src/index.html'
      })
  ]
  ~~~


#### 打包样式中的文件

- webpack需要借助loader编译解析图片文件

- 在样式表中引入图片

- 安装file-loader和url-loader

  - filr-loader用于处理文件，可将引用到的文件重命名并输出到输出路径中

  - url-loader是file-loader的上层封装，并可将指定大小（一般设置为8kb）以内的文件转换为base64编码存储在url中，需基于file-loader使用

- 配置loader

  ~~~javascript
  module: {
      rules: [
          { // 处理样式表中的图片资源
              test: /\.(jpg|png|gif|bmp)$/i,
              loader: 'url-loader',
              options: {
                  name: '[hash:10].[ext]', // 加工后的文件名，该写法意为：文件名取哈希值前十位，后缀名不变
                  outputPath: './imgs', // 输出路径
                  limit: 8 * 1024 // 小于8kb的文件会被转为base64编码
              }
          }
      ]
  }
  ~~~

#### 打包html中的图片

- webpack需要借助loader编译解析图片文件

- 在html中添加img标签引入图片

- 安装html-loader@1.3.2

- 配置loader

  ~~~javascript
  module: {
      rules: [
          { // 处理html中的<img>资源
              test: /\.html$/i,
              loader: 'html-loader'
          }
      ]
  }
  ~~~

- 在一些版本中，html-loader和url-loader可能产生冲突，使得html中的img的src变为[object Module]

  - 产生原因：file-loader默认采用ES模块化语法，与html-loader采用的CJS语法不兼容

  - 解决办法：在url-loader的options中加入：esModule: false

#### 打包其它资源

- 其它资源需借助file-loader打包

- 配置loader

  ~~~javascript
  module: {
      rules: [
          { // 打包其它资源
              exclude: /\.(js|json|html|css|less|jpg|png|gif|json|gif)$/i, // 排除已配置过的文件
              loader: 'file-loader'
              options: {
                  name: '[hash:10].[ext]',
                  outputPath: 'media' // 输出路径
          }
          }
      ]
  }
  ~~~

#### devServer

- 使用webpack-dev-server，可实现自动编译代码

- 分别全局及本地安装webpack-dev-server@3

- 在webpack配置对象中追加devServer配置

  ~~~javascript
  devServer: {
      port: 3000, // 端口号
      open: true, // 自动打开浏览器
      hot: true // HMR模式，Hot Module Replacemant，模块热更新，热模替换
  }
  ~~~

- 修改package.json中scripts指令后，可使用指令 yarn dev 或npm run dev运行webpack-dev-server

  ~~~json
  "scripts":{
      "dev": "webpack-dev-server"
  }
  ~~~

## 使用webpack搭建生产环境

#### 准备工作

- 新建config文件夹，将webpeck配置文件移入其中，重命名为webpack.dev.js，即webpack开发配置

- 新建webpack.prod.js，即webpack生产配置

- 在package.json的scripts中配置

  ~~~json
  "start": "webpack-dev-server --config ./config/webpack.dev.js",
  "build": "webpack --config ./config/webpack.prod.js"
  ~~~

- 在webpack生产配置的output中配置

  ~~~javascript
  output: {
      path: resolve(__dirname, '../build') // 由于修改了webpack配置文件的路径，__dirname的内容发生改变，需要重新定位输出路径
  }
  ~~~

- 在webpack生产配置中将mode改为production

#### 提取样式表资源为单独文件

- 安装插件mini-css-extract-plugin@1.6.2

  - mini-css-extract-plugin内还包含了一个loader

- 配置mini-css-extract-plugin

  - 引入插件

    ~~~javascript
    const MiniCssExtractPlugin = require('mini-css-extract-plugin')
    ~~~

  - 配置loader

    ~~~javascript
    const baseCssLoader = [MiniCssExtractPlugin.loader, 'css-loader']
    ~~~

    ~~~javascript
    module: {
        rules: [
            { // 处理css资源
                test: /\.css$/i, 
                use: [...baseCssLoader]
            },
            { // 处理less资源
                test: /\.less/i, 
                use: [...baseCssLoader, 'less-loader']
            }
        ]
    }
    ~~~

  - 配置插件

    ~~~javascript
    plugins:[
        new MiniCssExtractPlugin({
            filename: 'css/build.css' // 对输出的css文件进行重命名，并指定路径（可能读取时存在路径问题）
        })
    ]
    ~~~

  - 可能出现的问题

    - 如果mini-css-extract-plugin插件的输出文件名前添加了css路径，则样式表中引入的资源就会找不到正确的路径，不同的插件版本造成的资源

    - 遇到时的版本：

      - mini-css-extract-plugin@1.6.2

      - webpack@4.46.0

    - 解决方法：

      - 给MiniCssExtractPlugin.loader添加options配置，修正读取路径

      ~~~javascript
      const baseCssLoader = [
          {
              loader: miniCssExtractPlugin.loader,
              options: { publicPath: '../' } // 将读取路径设置到往前一层，以跳出css目录
          },
          'css-loader' 
      ]
      ~~~

#### css兼容性处理

- 安装

  - postcss
  - postcss-loader@4.3.0
  - postcss-preset-env

- 配置postcss-loader

  ~~~javascript
  const baseCssLoader = [
      {
          loader: miniCssExtractPlugin.loader,
          options: { publicPath: '../' } 
      },
      'css-loader',
      // ---------postcss-loader配置在下方-------------
      {
          loader: 'postcss-loader',
          options: {
              postcssOptions: {
                  plugins: ['postcss-preset-env']
              }
          }
      }
  ]
  ~~~

- 配置package.json，在其中追加browserslist配置，通过配置加载指定的css兼容性样式

  ~~~json
  "browserslist": {
  	// 开发环境
  	"development": [
  		"last 1 chrome version",
  		"last 1 firefox version",
  		"last 1 safari version"
  	],
  	// 生产环境
  	"production": [
  		">0.2%", //兼容市面上99.8%的浏览器
  		"not dead", //"死去"的浏览器不做兼容，例如IE8
  		"not op_mini all",//不做opera浏览器mini版的兼容
  		"ie 10" //兼容IE10
  	]
  }
  ~~~

  - browserslist 是一套描述产品目标运行环境的工具，它被广泛用在各种涉及浏览器/移动端的兼容性支持工具中，详细配置规则参考：https://github.com/browserslist/browserslist  

#### JS语法检查

- 安装
  - eslint
  - eslint-loader@4
  - eslint-config-airbnb-base
    - eslint-config-airbnb-base定制了一套标准的、常用的js语法检查规则
  - eslint-plugin-import
    - 用来将js语法检查规则导入到eslint中

- 配置loader

  ~~~javascript
  module: {
    rules: [
      { // 对js进行语法检查
          test: /\.js$/,
          exclude: /node_modules/, // 排除node_modules
          // 优先执行
          enforce: 'pre', // 优先执行  只要webpack启动时  尽可能先执行  可不写
          loader: 'eslint-loader',
          options: {
            fix: true // 若有问题自动修复，重要！！！！
      	}
  	}      
    ]
  }
  ~~~

- 修改package.json

  ~~~json
  "eslintConfig": {
    "extends": "airbnb-base", // 直接使用airbnb-base提供的规则 需要下载的
    "env": {
     "browser": true // 如果运行环境不是浏览器  则运行环境为node  此时需要将这个改为false
    }
  }
  ~~~

#### JS语法转换（ES6 -> ES5）

- 将JS新语法转换为旧语法

- 安装

  - babel-loader@8
  - @babel/core
  - @babe/preset-env

- 配置loader

  ~~~javascript
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader",
          options: {
            presets: ['@babel/preset-env']
          }
        }
      }
    ]
  }
  ~~~


#### JS高级兼容性（Promise）

- 安装@babel-polyfill

- 用于转换promise语法，可兼容ie

- 在app.js中引入

  ~~~javascript
  import '@babel/polyfill'
  ~~~

#### 压缩HTML、JS

- 修改webpack.prod.js中的model为production
- 如果报错，则在new HtmlwebpackPlugin时添加配置minify: false

#### 压缩CSS

- 安装optimize-css-assets-webpack-plugin

- 配置插件

  ~~~javascript
  const OptimizeCssAssetsPlugin = require('optimize-css-assets-webpack-plugin')
  ~~~

  ~~~javascript
  plugins: [
      new OptimizeCssAssetsPlugin({
          cssProcessorPluginOptions: {
              // 使用默认配置，另外移除所有注释
              preset: ['default', { discardComments: { removeAll: true } }]
          }
      })
  ]
  ~~~

## 总结

#### webpack配置文件

~~~javascript
module.exports = {	
    entry: '', // 入口 
    output: {}, // 输出
    module: { // 配置loader
      rules: []
    },
    plugins: [], // 配置plugin
    mode: '' // 工作模式
}
~~~

#### webpack打包的基本流程

- 连接

  webpack从入口JS文件开始，递归查找出所有相关的模块，并连接起来形成一个网状结构

- 编译

  将JS模块中的模块化语法编译为浏览器可以直接运行的语法，并借助loaders和plugins处理其它资源

- 合并

  将所有编译过的模块合并成一个或少量的几个文件，浏览器真正运行的时打包后的文件

#### loader与plugin

- loader

  用于加载特定类型的资源文件，webpack本身只能打包js和json

- plugins

  用来扩展webpack其它方面的功能，一般loader处理不了的资源可以交给plugins处理

#### live-reload（自动刷新）与HMR（热模交换）

- 相同点
  - 代码修改后都会自动重新编译打包
- 不同点
  - live-reload：刷新整个页面
  - HMR：只加载修改模块的打包文件运行，页面的其他部分没有刷新

#### webpack常用loader与plugins

##### 常用loader

~~~markdown
1. 【less-loader】用于将less文件翻译成为css
2. 【css-loader】用于将css以CommonJs语法打包到js中
3. 【style-loader】用于动态创建一个style标签，将css引入页面
4. 【file-loader】用于处理其他资源，也可以处理图片资源，核心操作就是：提取资源到指定位置，且可修改文件名等操作。
5. 【url-loader】与file-loader功能几乎一致，优势是可以对图片进行动态转换base64编码（控制limit属性值可以控制阈值）。
			备注：上述两个loader中url-loader应用比file-loader广泛，且url-loader是file-loader的上层封装。
6. 【html-loader】用于处理html中<img>标签的图片。
6. 【eslint-loader】对项目中的js语法进行检查。		
7. 【babel-loader】将es6语法转换为es5语法
			备注1：直接使用只能处理简单的语法，Promise等无法处理。
			备注2：借助polyfill完成高级es6语法的转换，缺点：所有都转换，无法按需转换，生成的js体积大。
			备注3：使用core-js配合polyfill完成按需转换。
8. 【postcss-loader】经典的loadr，用于处理css兼容性问题，需要与【postcss】和【postcss-preset-env】配合使用
			备注1：使用的时机为：["css-loader","postcss-loader","less-loader"]。
			备注2：需要在package.json中配置browserslist属性指定具体的兼容规则
			备注3：browserslist是一个单独的库，被广泛用在各种涉及浏览器/移动端的兼容性支持工具中
~~~

##### 常用plugins

~~~markdown
1. 【mini-css-extract-plugin】：用于提取项目中的css为一个单独的文件。
			备注1：使用的时机为：[MiniCssExtractPlugin.loader,"css-loader","postcss-loader",less-loader"]。
			备注2：使用是可以在new时传入具体配置例如：
			{
                loader: "postcss-loader",
                options: {
                    postcssOptions: {
                        plugins: ["postcss-preset-env"]
                    }
                }
            }
2. 【html-webpack-plugin】：根据指定模板，自动创建html文件，且自动引入外部资源
3. 【eslint-plugin-import】：用于配合eslint-loader
4. 【eslint-config-airbnb-base】用于引入airbnb配置好的语法检查规则，否则需要一个一个配置，比较麻烦
5. 【@babel/polyfill】用于处理JS兼容性问题，例如IE浏览器不支持Promise
6. 【optimize-css-assets-webpack-plugin】用于压缩css	
~~~

#### tree-shaking

- tree-shangking会在打包时，会将模块中暴露了但没有被用到的数据去除    
- 同时满足两个条件webpack会自动开启tree-shaking：
  1.  使用ES6模块化  
   2.  开启production环境
