# JavaScript模块化

## 模块化概念

#### 模块化的理解

- 将一个复杂的程序依据一定的规则拆分成单个文件，并最终组合在一起
- 这些拆分的文件就是模块，模块内部数据是私有的，只是向外暴露一些方法与外部其它模块通信

#### 模块化的优点

- 降低复杂度，提高解耦性
  - 注：高内聚，低耦合
    - 内聚：度量一个模块内部各个元素彼此结合的紧密程度
    - 耦合：度量模块之间互相连接的紧密程度
  - 解耦即将不同模块分开处理
- 避免命名冲突
- 更好的分离，按需加载
- 高复用性，高可维护性

## 模块化规范

### CommonJS(CJS)

- 每个文件都是一个模块
- CommonJS模块化的代码既可以在服务端运行，也可以在浏览器端运行
  - 在服务器端可以直接运行
  - 在浏览器端需要通过Browserify编译

#### 基本语法

##### 暴露语法

- 第一种方式：moudule.exports = value
- 第二种方式：exports.xxx = value

##### 引入语法

- 引入第三方模块：require(模块名)
- 引入自定义模块：require(模块文件的路径)

#### Browserify

- 使用包管理器全局安装browserify

- 编译指定文件

  ~~~shell
  browserify app.js --outfile build.js
  ~~~

- 在html页面中引入build.js

### ES Module

- 每个文件都是一个模块
- 要借助Bable和Browserify依次编译代码，才能在浏览器端运行

#### 模块暴露及引入

##### 建立文件结构

~~~markdown
- src
    - moudule1.js
    - moudule2.js
    - moudule3.js
    - moudule4.js
    - app.js
~~~

##### 分别暴露

~~~javascript
// module1.js
export const person = {name: 'tom', age: 18}
export const msg = 'Hello World'
~~~

##### 统一暴露

~~~javascript
// module2.js
const arr = [1, 2, 3]
function showArr () {
    console.log(arr)
}
export {arr, showArr as showData}
~~~

##### 默认暴露

~~~javascript
// module3.js
// 默认暴露在一个文件中只能使用一次
export default {name: '张三', age: 18}
~~~

##### 暴露方式混用

~~~javascript
// module4.js

// 使用分别暴露
export const a = 1
export const b = 2

// 使用统一暴露
const c = 3
const d = 4
export {c, d}

// 使用默认暴露
export default 5
~~~

##### 下载第三方模块

~~~shell
npm i uniq
~~~

##### 在主文件中引入模块

~~~javascript
// app.js
// 使用分别暴露和统一暴露的模块，引入方式相同

// 引入module1的模块，并将msg重命名为message
import {person, msg as message} from './module1'

// 将module1的模块打包引入
import * as module1 from './module1'

// 引入module2的模块
import {arr, showData} from './module2'

// 将module2的模块打包引入
import * as module2 from './module2'
// -------------------------------------------

// 引入module3的模块
import person2 from './module3'
// -------------------------------------------

// 引入module4的模块
import e, {a, b, c, d} from './module4'
import f, * as module4 from './module4'
// -------------------------------------------

// 引入第三方模块
import uniq from 'uniq'
~~~

##### export 和 import 结合使用

~~~javascript
export { * as xxx } from './xxx'
// 用于封装功能库时，将暴露的所有接口放到一个文件中
~~~

##### 动态加载模块

~~~javascript
let flag = true
if (flag) {
    import('./xxx').then(xxx => {
        xxx.xxx()
    })
} else {
    import('./yyy').then(yyy => {
        yyy.yyy()
    })
}
~~~

##### import.meta

- `ES11`(`ES2020`)新增特性

- `import.meta` 是一个给 `JS` 模块暴露特点上下文的元数据属性的对象

- 它包含了这个模块的信息，比如这个模块的 `URL`


#### 在浏览器端运行ES6模块化

##### 准备工作

- 全局安装babel-cli、Browserify

  - cli --- command line interface，命令行接口

- 定义.babelrc`文件`

  - rc：run controller

  ~~~json
  {
    "presets": ["es2015"]
  }
  ~~~

##### 编译代码

- 进入目录，使用Babel将ES6编译为ES5代码 : 

  ~~~shell
  babel ./src -d ./build
  ~~~

- 使用Browserify编译ES5代码

  ~~~shell
  browserify ./build/app.js -o ./build/build.js
  ~~~

##### 在HTML页面中引入script标签

~~~html
 <script type="text/javascript" src="../build/build.js"></script>
~~~

#### 在入口文件中引入其它类型文件

##### 引入json文件

- 在ES6模块化规范中，json文件相当于默认暴露了自己本身，可在入口文件中使用引入默认暴露模块的方式引入

~~~javascript
import data from 'json文件路径'
~~~

##### 引入css文件

~~~javascript
import 'css文件路径'
~~~

