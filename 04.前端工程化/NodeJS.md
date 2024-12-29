# NodeJS

## 简介

Node.js是一个基于Chrome V8引擎的JavaScript运行环境

Node.js中的全局对象是global

## 下载及文档地址

官方网址 <https://nodejs.org/en/>

中文站 <http://nodejs.cn/>

历史版本下载 <https://npm.taobao.org/mirrors/node/>

安装完成后在CMD命令行窗口下运行`node -v`，如显示版本号则证明安装成功，反之安装失败

## NodeJS的基本使用

### 交互模式

- 在命令行下输入命令`node`，即可进入NodeJS交互模式，此时输入的命令会作为NodeJS语法执行

### 文件执行

- 在命令行下输入`node 要执行的文件.js`，即可在node环境下执行JS文件

## Buffer(缓冲器)

- Buffer是一个和数组类似的对象，不同是Buffer是专门用来保存二进制数据的
- 特点
  - 在创建时大小就确定，无法调整
  - 直接对计算机内存进行操作，性能较好
  - 每个元素大小为1字节(byte)
- 操作
  - 创建Buffer
    - Buffer.alloc(字节数)
      - 直接创建一个Buffet并返回
      - 创建时会将当前所占内存空间的值归零
    - Buffer.allocUnsafe
      - 不安全创建
      - 创建时不会将所占内存空间的值归零、
    - Buffer.from(字符串)
      - 通过字符串创建
  - Buffer读取和写入
    - <u>Buffer</u>.[下标]
      - 对Buffer进行读取和设置
    - <u>Buffer</u>.toString()
      - 将Buffer转化为字符串
- ASCII字符与编号转换
  - String.fromCharCode(十进制编号)
    - 通过十进制编号获取对应的ASCII字符
  - 字符.charCodeAt()
    - 通过ASCII字符获取对应的十进制编号
- 溢出
  - 一个字节最多存储八位，超出八位的高位数据会被舍弃
- 一个UTF-8的中文字符大多数情况下占3个字节

## 模块

#### fs文件系统模块

- fs全称为file system，是NodeJS中的内置模块，可以对计算机中的文件进行增删改查等操作

- 引入fs模块

  ~~~javascript
  const fs = require('fs')
  ~~~

- 文件写入

  - fs.writeFile(file, data[, options], callback)

    - 异步API，简单写入

    - 参数

      - file 文件名

      - data 写入的数据

      - options对象

        - encoding 字符集，默认值‘utf8’

        - mode 模式，默认值0o666

          - Linux下文件权限的管理方式

          - 0o为八进制数值表示形式
          - 最高位数字代表所有者的权限
          - 第二位数字代表所属组的权限
          - 最低位数字代表其他人的权限
          - 4表示可读
          - 2表示可写
          - 1表示可执行
          - 一般文件设置为755，文件夹设置为644

        - flag 标记，默认值‘w’

          - r read 只读
          - w write 可写
          - a append 追加

      - callback 回调函数

        - 传递一个err形参，如果传入数据发生错误，会将错误对象传递给err，如果没有发生错误，则传递给err的值为null
  - fs.writeFileSync(file, data[, options])

    - 同步API，简单写入
    - 参数
  - 流式写入
    - const ws = fs.createWriteStream(path[, options])
      - 创建一个写入流对象，将其返回
      - 参数
        - path 文件路径
        - options同简单写入
    - ws.write(data)
      - 执行写入
    - ws.close()
      - 关闭写入流

- 文件读取

  - fs.readFile(path[, options], callback)

    - 异步API，简单读取

    - 参数

      - options同文件写入

      - callback 回调函数
        - 可用形参第一个是err，传递错误对象
        - 第二个是data，传递文件的内容

  - fs.readFileSync(path[, options])

    - 同步API，简单读取，将文件内容返回

  - 流式读取

    - 流式读取用于大文件的读取
    - const rs = createReadStream(path[, options])
      - 创建一个读取流对象，将其返回
    - rs.on('data', (chunk) => {console.log(chunk)})
      - 当读取完一块数据后触发
    - rs.pipe(ws)
      - 将流式读取的内容传至写入流（复制文件）

- 文件拷贝

  - fs.copyFile(src, dest[, flag],callback)
    - 参数
      - src 要拷贝的源文件名
      - dest 拷贝操作的目标文件名
      - callback可传参数为err
  - fs.copyFileSync(src, dest[, flag])

- 文件重命名

  - fs.rename(oldPath, newPath, callback)
    - 可移动文件
    - callback可传参数为err
  - fs.renameSync(olfPath, newPath)

- 文件删除

  - fs.unlink(path, callback)
  - fs.unlinkSync(path)

- 文件夹操作

  ~~~javascript
  // 创建文件夹
  fs.mkdir('./html',err => {
      if (err) throw err
      console.log('创建成功')
  })

  // 读取文件夹列表信息
  fs.readdir('./project', (err, files) => {
      if (err) throw errs
      console.log(files)
  })

  // 删除空文件夹
  fs.rmdir('./html',err => {
      if (err) throw errs
      console.log('删除成功')
  })

  // 递归删除文件夹
  fs.rmdir('./html', {recurisive: true}, err => {
      if (err) throw errs
      console.log('删除成功')
  })
  ~~~

- __dirname 特殊变量

  - 始终保存当前文件所在文件夹的绝对路径

- 获取文件的状态

  ~~~javascript
  fs.stats('路径', (err, stats) => {
      if (err) throw err
      console.log('是否为文件夹' + stats.isDirectory)
      console.log('是否为文件' + stats.isFile)
  }) 
  ~~~

#### path路径模块

- Node.js提供的处理路径的模块
- path.join([...paths])
  - 将路径片段使用特定的分隔符(/)连接起来形成路径，并规范化生成的路径
  - 如果出现分隔符(/)，则会作为定界符
- path.resolve([...paths])
  - 把一个路径或路径片段的序列解析为一个绝对路径，相当于执行cd操作
  - 如果遇到路径片段为(/)开头，则会被解析为根目录（windows下为所在盘符的根目录），从(/)往后拼接路径，如果遇到的是相对路径，则和path.join()一样拼接
  - 如果不传参，则得到的是当前执行文件的绝对路径
- path.basename(path[, ext])
  - 获取路径中的最后一部分
  - 参数
    - path <string> 必选，表示一个路径的字符串
    - ext <string> 可选，输入文件拓展名后，返回的结果会将匹配到的拓展名舍去
- path.extname(path)
  - 获取路径中的拓展名部分

## 模块化

#### 介绍

模块化就是将一个大的功能拆分为一个一个小的模块，通过不同的模块的组合来实现一个大功能

- 在node中一个js文件就是一个模块
- 模块内部代码对于外部来说都是不可见的，通过向外部暴露来让外部调用

#### 模块暴露与引入

- 用于暴露模块的两个对象

  1. moudule.exports
  2. exports

- 模块暴露的原理

  - module.exports与exports默认指向了同一个内存地址，该地址中的默认内容为{}，一个空的对象
  - 引入js模块时，node.js会寻找module.exports指向的内存块
  - 因此module.exports可以直接赋值，改变其内存地址，但会使得module.exports与exports指向不同的内存块

- 引入模块的方式

  - const 变量 = require('模块所在的js文件')

- 引入模块注意事项

  - 支持的格式：

    - .js 

      fs模块同步读取文件编译执行

    - .json

      fs模块同步读取，并同步解析成js对象

    - .node

      c/c++便携的扩展文件，通过dlopen()方法编译

  - 其他拓展名会被当作js文件载入 

  - 如果路径为文件夹，默认会加载该文件夹下 package.json 文件中 main 属性对应的文件（入口文件）

    如果 main 属性对应的文件不存在，则会自动找 index.js index.json

  - npm引入包时，如果当前文件夹下的 node_modules 文件夹中没有，则会自动向上查找