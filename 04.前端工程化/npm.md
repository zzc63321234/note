# NPM

## 介绍

全称Node Package Manager，Node的包管理器，也是一个应用程序

### 包

Node.js的包遵循CommonJS规范，将一组相关的模块组合在一起，形成一个完整的工具

## 作用

通过NPM可以对Node的工具包进行搜索、下载、安装、删除、上传

## 常用命令

#### 查看npm版本

~~~shell
npm -v
~~~

#### 初始化

~~~shell
npm init
npm init --yes # 快速创建，包的名字不能使用中文和大写字母，不能使用npm作为包的名字
~~~

初始化后会创建package.json文件

~~~json
{
  "name": "1-npm",      #包的名字
  "version": "1.0.0",   #包的版本
  "description": "",    #包的描述
  "main": "index.js",   #包的入口文件
  "scripts": {			#脚本配置
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",			#作者
  "license": "ISC"		#版权声明
}
~~~

关于开源证书扩展阅读

<http://www.ruanyifeng.com/blog/2011/05/how_to_choose_free_software_licenses.html>

#### 搜索包

~~~shell
npm search jquery
npm s jquery
~~~

一般在搜索工具包的时候，会到https://npmjs.com 搜索

#### 安装工具包

~~~shell
npm install jquery
npm i jquery

# 安装并在 package.json 中保存包的信息(dependencies 属性，生产依赖)
# 6版本之后的npm，安装包时会自动保存在dependencies中，无需再写-s
npm install jquery --save
npm install jquery -S

# 安装并在 package.json 中保存包的信息(devDependencies 属性，开发依赖)
npm install babel --save-dev
npm install babel -D
# 项目本身需要用到的是生产依赖，只有开发过程需要用到的是开发依赖

# 全局安装
npm install less -g
npm install nodemon -g
# 全局安装一般用于安装全局工具，如cnpm，yarn，webpack，gulp等
# 全局命令的安装位置为 C:\Users\用户名\AppData\Roaming\npm
# 查看全局工具包的安装路径
npm root -g

# 根据package.json中的依赖声明，安装工具包
npm i
npm install
npm i --production # 只安装dependencies中的依赖
~~~

#### 常用全局工具包的使用示例

~~~shell
# less包可将less文件编译为css文件
less 要编译的less文件.less 编译后的css文件.css
# 通过nodemon启动的应用程序，可在检测到目录中文件更改时自动重启
nodemon 文件名.js
~~~

#### 移除工具包

~~~shell
npm remove jquery
~~~

## 使用流程

1. 在命令行中将工作目录定位到项目文件夹
2. 允许 npm init
3. 安装工具包

## 自定义NPM包

#### 封装包

1. 确认镜像地址为官方地址
2. 创建文件夹，创建文件index.js，在文件中声明函数，使用[moudule.exports]暴露
3. npm初始化，填写包名
4. [npm login]登录
5. [npm publish]提交包

#### 升级包

1. 修改包代码
2. 修改package.json中的版本号
3. [npm publish]提交包

#### 删除包

~~~shell
npm unpublish 包名 --force
~~~

#### 让发布的包过期

~~~shell
npm deprecate 包名
~~~

## CNPM

#### 介绍

CNPM是淘宝对国外npm服务器的一个完整镜像版本（只读），也就是淘宝npm镜像，网站地址<http://npm.taobao.org/>

#### 安装

有两种方式

~~~shell
npm install -g cnpm --registry=https://registry.npm.taobao.org
~~~

~~~shell
alias cnpm="npm --registry=https://registry.npm.taobao.org \
--cache=$HOME/.npm/.cache/cnpm \
--disturl=https://npm.taobao.org/dist \
--userconfig=$HOME/.cnpmrc"
# 只能在Linux下使用
~~~

#### 使用

- 安装完成后，可以使用 cnpm 命令来管理包，使用方法和 npm 一样
- 注意：使用 cnpm 安装包时，不会自动将依赖保存到 package.json 中

#### 配置镜像地址

~~~shell
# 淘宝镜像
npm config set registry https://registry.npm.taobao.org
npm config set registry https://registry.npmmirror.com/
# 官方镜像
npm config set registry https://registry.npmjs.org/
# 查看当前镜像地址
npm config get registry
npm config list
# 配置镜像地址为淘宝镜像后，仍可使用 npm 命令
~~~

## Yarn

#### 介绍

yarn是Facebook开源的新的包管理器，可以用来代替npm

#### 特点

相比与npm有几个特点

- 本地缓存
  - 安装过的包下次不会进行远程安装
- 并行下载
  - 一次下载多个包
  - npm是串行下载
- 精准的版本控制，保证每次安装和上次都是一样的

#### 安装

1. 使用npm安装

   ~~~shell
   npm install yarn -g
   # 该方式需自行配置环境变量path，否则yarn全局命令不可用
   ~~~

2. 使用msi安装包安装

   https://classic.yarnpkg.com/en/docs/install#windows-stable

#### 相关命令

- yarn --version

  - 查看varn版本

- yarn init

  - 初始化

- yarn add 包名

  - 局部安装

- yarn add 包名 --dev

  - 局部安装并添加开发依赖

- yarn remove 包名

  - 移除包

- yarn global add 包名

  - 全局安装
  - 安装路径 C:\Users\用户名\AppData\Local\Yarn\bin

- yarn global remove 包名

  - 全局删除

- yarn list

  - 列出已安装的包

- yarn

  - 安装package.json中的所有依赖

- 修改镜像地址

  ~~~shell
  yarn config set registry https://registry.npm.taobao.org
  ~~~

- 安装cyarn

  ~~~shell
  npm install cyarn -g --registry "https://registry.npm.taobao.org"
  ~~~


#### 锁文件

- 用来锁住版本

## 相关补充

#### 环境变量Path的配置

- 在cmd命令行中输入指令后，会从当前路径下寻找同名可执行文件并执行

  如果当前路径下没有，则会从环境变量Path里配置的路径中寻找

- 配置方法

  此电脑 -> 属性 -> 高级系统设置 -> 环境变量 -> 双击Path -> 新建 -> 输入路径

- 配置完成后，重启cmd命令行生效

#### npm在多人协作中的注意点

- node_modules目录不建议进入Git仓库

  项目后期node_modules中的文件数量和体积过大，不利于内容传递


#### npm清除缓存

~~~shell
npm cache clean
~~~

#### 关于版本号

- 版本格式：主版本号.次版本号.修订号(`mojor`.`minor`.`patch`)

  - major 表示非兼容的重大 API 改变（主要的）
  - minor 表示向后兼容的功能性改变（次要的）
  - patch 表示向后兼容的 bug 修正（修补的）

- 锁定版本

  - "^3.0.0"

    锁定主版本，以后安装工具包的时候，保证包是3.x.x版本中的最新版

  - "~3.2.x"

    锁定次版本，以后安装工具包的时候，保证包是3.2.x版本中的最新版

  - "3.2.1"

    锁定完整版本，以后安装工具包的时候，保证包必须是3.1.1版本

- 安装指定版本的工具包

~~~shell
yarn add jquery@1.11.2
~~~

#### 使用npm执行命令

1. 在 package.json 文件的 "scripts" 对象中配置命令以及命令的别名

   > "别名": "命令"

2. 在命令行中使用命令[npm run 别名]，等同于执行配置的命令

#### 查看当前函数的全部代码

~~~javascript
console.log(arguments.callee.toString())
~~~

#### host文件

- 路径

  > C:\Windows\System32\drivers\etc\host

- 作用为绑定域名和ip地址