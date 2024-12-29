# DOM节点操作

## 节点操作

### 1. DOM节点

- DOM树里的每一个内容都称之为节点

  ![DOM](.\image\BOM.webp)

- 节点类型

  - 元素节点（最常用）
    - 所有的标签，如body、div等
    - html是根节点
  - 属性节点
    - 所有的属性，如href、class等
  - 文本节点
    - 所有的文本，比如标签里面的文字
  - 其他

### 2. 查找节点

- 节点关系

  - 父节点
  - 子节点
  - 兄弟节点

- 父节点查找

  - <u>Node</u>.parentNode

    返回指定节点在DOM树中最近一级的父节点，如果没有则返回null

- 子节点查找

  - <u>Node</u>.childNodes

    返回指定节点的所有子节点的集合，该集合为实时更新的集合

  - <u>Element</u>.children（重要）

    返回指定元素的子元素节点集合

- 兄弟节点查找

  - 下一个兄弟节点

    <u>Node</u>.nextElementSibling

  - 上一个兄弟节点

    <u>Node</u>.previousElementSibling

### 3. 增加节点

- 新增节点操作：
  - 创建一个新的节点
  - 把创建的新节点追加到指定的元素内部

#### 3.1 创建节点

- 新建节点

  - 语法：

    let <u>newElement</u> = document.createElement('<u>tagName</u>')

  - 参数：

    - newElement

      用以存放新增的元素节点

    - tagName

      标签名

  - 返回值：创建的新元素

- 克隆节点

  - 语法：

    let <u>dupNode</u> = <u>node</u>.cloneNode(<u>booleanValue</u>)

  - 参数：

    - node

      将要被克隆的节点

    - dupNode

      用以存放克隆生成的副本节点

    - booleanValue

      布尔值，若为true，则该节点后代也会被克隆，若为false，则只克隆该节点本身

#### 3.2 追加节点

- 插入到父元素的末尾

  - 语法：

    <u>parentNode</u>.appendChild(<u>newNode</u>)

  - 参数:

    parentNode：新插入节点的父节点

    newNode：用于插入的节点

  - 返回值：

    被追加的子节点

- 插入到父元素的某个子元素前

  - 语法：

    <u>parentNode</u>.insertBefore(<u>newNode</u>, <u>referenceNode</u>)

  - 参数：

    - parentNode

      新插入节点的父节点

    - newNode

      用于插入的节点

    - referenceNode

      newNode将要插入到这个节点之前

  - 返回值：
  
    被追加的子节点
  
  - 注意：
  
    如果referenceNode为null，则newNode将被插入到子节点的末尾

### 4. 删除节点

- 语法：

  let <u>oldChild</u> = <u>node</u>.removeChild(<u>child</u>)

- 参数：

  - child

    要被移除的节点

  - node

    child的父节点

  - oldChild

    用来存放被删除的节点

- 返回值：

  被删除的节点

- 在JavaScript原生DOM操作中，要删除元素必须通过父元素删除

- 被移除的节点仍然存在于内存中，只是没有添加到当前文档的DOM树中，若需要把这个节点重新添加回文档中，可以用oldChild来存放它，否则被移除的节点会很快被内存管理回收

## 时间对象

### 1. 实例化

- new运算符创建一个用户定义的对象类型的实例或具有构造函数的内置对象的实例

- 语法：

  new <u>constructor</u>(<u>arguments</u>)

- 参数：

  - constructor

    一个指定对象实例的类型的类或函数

  - arguments

    一个用于被constuctor调用的参数列表

- 实例化时间对象

  ~~~javascript
  // 获得当前时间
  let date = new Date()
  // 获得指定时间
  let date = new Date(‘2020-10-01 12:00:00’)
  ~~~

### 2. 时间对象方法

- 时间对象返回的数据无法直接使用，需要用时间对象方法转换为常用格式

- 语法：时间对象.时间对象方法

  | 方法          | 作用           | 说明                                           |
  | :------------ | -------------- | ---------------------------------------------- |
  | getFullYear() | 获取年份       | 获取四位数字的年份                             |
  | getMonth()    | 获取月份       | 取值为0~11（使用时需要给返回值+1获得正确月份） |
  | getDate()     | 获取月份中的天 | 取值为1~31                                     |
  | getDay()      | 获取星期中的天 | 取值为0~6（0代表周日）                         |
  | getHours()    | 获取小时       | 取值为0~23                                     |
  | getMinutes()  | 获取分钟       | 取值为0~59                                     |
  | getSeconds()  | 获取秒         | 取值为0~59                                     |
  
- 时间对象动态化方法：

  new Date().toLocaleString

#### 3. 时间戳

- 时间戳时从1970-01-01 00:00:00起至某个时间点的毫秒数，是一种特殊的计量时间的方式

- 获取时间戳的三种方式

  ~~~javascript
  // 1. 使用getTime()方法
  let now = new Date()
  console.log(now.getTime())
  let date = new Date(‘2020-10-01 12:00:00’)
  console.log(date.getTime())
  // 2. +new Date()
  console.log(+new Date())
  console.log(+new Date(‘2020-10-01 12:00:00’))
  // 3. 使用Date.now()
  console.log(Date.now())
  //后两种无需实例化，前两种可以返回指定时间的时间戳，因此最推荐使用第二种
  ~~~

## 重绘和回流

1. 浏览器是如何进行界面渲染的

   ![浏览器渲染过程](.\image\Render.jpeg)

   - 解析（Parser）HTML，生成DOM树
   - 同时解析CSS，生成样式规则（Style Rules）
   - 根据DOM树和样式规则，生成渲染树（Render Tree）
   - 进行布局Layout（回流/重排）：根据生成的渲染树，得到节点的几何信息（位置，大小）
   - 进行绘制Painting（重回）：根据渲染树以及回流得到的几何信息，得到节点的绝对像素
   - Display：展示在页面上

2. 重绘和回流（重排）

   - 回流（重排）

     当渲染树中部分或者全部的的尺寸、结构、布局等发生改变时，浏览器就会重新渲染部分或全部文档，这个过程称为回流（重排）

   - 重绘

     当节点（元素）的样式的改变并不影响它在文档流中的位置和文档布局时（比如：color、outline等），称为重绘

   - 重绘不一定会引起回流，而回流一定会引起重绘className