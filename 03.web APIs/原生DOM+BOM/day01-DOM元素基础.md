# DOM元素基础

## Web API 基本认知

### 1. 作用和分类

- 作用：使用JS去操作HTML和浏览器

- 分类：DOM（文档对象模型）、BOM（浏览器对象模型）

  ```mermaid
  graph TB
  A(JavaScript)
  B(ECMAScript = JS基础语言)
  C(Web APIs)
  D(DOM = 页面对象模型)
  E(BOM = 浏览器对象模型)
  A-->B
  A-->C
  C-->D
  C-->E
  ```


### 2. DOM

- DOM(Document Object Mode——文档对象模型l)是用来呈现以及与任意HTML或XML文档交互的API

- 即，DOM是浏览器提供的一套专门用来操作网页内容的功能

- DOM的作用：

  > 开发网页内容特效和实现用户交互



### 3. DOM树

- 什么是DOM树

  >将HTML文档以树状结构直观的表现出来，我们称之为文档树或DOM树
  >
  >描述网页内容关系的名词
  >
  >作用：DOM树直观地体现了标签与标签之间的关系

  ![DOM树](.\image\DOM树.gif)

### 4. DOM对象

- DOM对象：浏览器根据HTML标签生成的JS对象

  >所有的标签属性都可以在这个对象上面找到
  >
  >修改这个对象的属性会自动映射到标签身上

- DOM的核心思想：

  > 把网页元素当作对象来处理

- document对象

  > 是DOM里提供的一个对象
  >
  > 它提供的所有属性和方法都是用来访问和操作网页内容的
  >
  > 例:
  >
  > > document.write()
  > > document.querySelector()
  >
  > 网页的所有内容都在document对象里

## 获取DOM对象

### 1. 根据CSS选择器来获取DOM元素

#### 1.1 选择匹配的第一个元素

- 语法：

  ><u>element</u> = document.querySelector('<u>selectors'</u>)



- 参数：

  > 包含一个或多个有效的CSS选择器字符串^（因此必须加引号）^

- 返回值：

  > 一个<u>Element</u>对象，内容是document中与指定的CSS选择器匹配的第一个元素
  >
  > 如果没有匹配到，则返回<u>null</u>
  >
  > CSS伪类不会返回任何元素

- 例：

  ~~~javascript
  //获取HTML页面中第一个<img>标签中的元素，并将其赋值于名为‘pic’的对象中
  let pic = document.querySelector('img')
  ~~~

  


#### 1.2 匹配选择的多个元素

- 语法：

  > <u>elementList</u> = document.querySelectorAll('<u>selectors'</u>)

- 参数：

  > 包含一个或多个有效的CSS选择器字符串^（因此必须加引号）^

- 返回值：

  > 一个静态的<u>NodeList</u>（对象节点的集合），包含document中所有与指定选择器匹配的元素
  >
  > 在没有匹配的情况下返回一个空<u>Nodelist</u>
  >
  > 如果<u>selectors</u>参数中包含CSS为元素，则返回的列表始终为空
  >
  > <u>NodeList</u>是伪数组
  >
  > > 有长度和索引号
  > >
  > > 没有pop()、push()等数组方法

- 例：

  ```javascript
  //获取HTML页面中所有<img>标签中的元素，并将其赋值于名为‘pics’的对象集合中
  let pics = document.querySelectorAll('img')
  ```

### 2. 其他获取DOM元素的方法

- 根据id获取一个元素

  > document.getElementById（‘<u>idName</u>’）

- 根据标签获取一类（符合标签名的所有）元素

  > document.getElementsByTagName('<u>tagName</u>')

- 根据类名获取一类（符合类名的所有）元素

  > document.getElementsByClassName('<u>cName</u>')

  

## 操作DOM对象

### 1. 前提

- DOM的核心思想是将网页元素当作对象来处理，因此，当获取到HTML元素后，需要将其赋值到JS的对象中，而后通过操作对象的方式来对网页元素进行修改

  用法：

  > let <u>object</u> = document.querySelector('<u>selectors'</u>)

  > let <u>NodeList</u> = document.querySelectorAll('<u>selectors'</u>)

### 2. 设置/修改DOM元素内容

- document.write()

  > 只能将文本内容追加到</body>前面的位置
  >
  > 文本中包含的标签会被解析

- <u>通过修改HTMLElement</u>.innerText属性的值

  >将文本内容添加/更新到任意标签位置
  >
  >文本中包含的标签不会被解析
  >
  >用法：
  >
  >> <u>object</u>.innerText = value
  >
  >例：
  >
  >~~~javascript
  >//1.获取HTML页面中class为'info'的第一个<p>元素，并将其赋值于名为‘info’的对象中
  >let info = document.quertSelector('p.info')
  >//2.将改标签的内容改为“这是一段信息”
  >info.innerText = '这是一段信息'
  >~~~
  >
  >


- <u>通过修改element</u>.innerHTML属性的值

  > 将文本内容添加/更新到任意标签位置
  >
  > 文本中包含的标签会被解析
  >
  > 用法:
  >
  > > <u>object</u>.innerHTML = value
  >
  > 例：
  >
  > ```javascript
  > //1.获取HTML页面中class为'info'的第一个<p>元素，并将其赋值于名为‘info’的对象中
  > let info = document.quertSelector('p.info')
  > //2.将改标签的内容改为加重的“这是一段信息”
  > info.innerText = '<strong>这是一段信息</strong>'
  > ```

### 3. 设置/修改DOM元素属性

- 设置/修改元素常用属性

  > 通过操作DOM元素属性，来设置/修改HTML标签的常用属性，如src、href、title等
  >
  > 语法：
  >
  > > <u>object</u>.<u>attribute</u> = <u>value</u>
  > >
  > > <u>对象</u>.<u>属性</u> = <u>值</u>
  >
  > 例：
  >
  > ```javascript
  > //1.获取HTML页面中第一个<img>元素，并将其赋值于名为‘pic’的对象中
  > let pic = document.querySelector('img')
  > //2.通过操作pic对象的属性来更改这个img标签中的图片源和标题
  > pic.src = './images/1.jpg'
  > pic.title = '这是一个图片'
  > ```
  >
  > ```javascript
  > //1.获取HTML中所有img标签中的元素，并将其赋值于名为‘pics’的对象集合中
  > let pics = document.querySelectorAll('img')
  > //2.通过操作pics对象集合中的第一个对象pics[0]的属性来更改HTML页面中第一个img标签中的图片源和标题
  > pics[0].src = './images/1.jpg'
  > pics[0].title = '这是一个图片'
  > ```

- 设置/修改表单元素属性

  > - 表单标签元素属性有很多情况，比如切换密码框中的密码显示方式，是通过将表单元素的type属性切为‘text’或‘password’来实现的。正常的有取值的属性的操作方法，和其他常用的标签元素属性没有区别
  >
  > - 在HTML的表单标签中有些属性，被添加时会产生效果，移出后则为默认效果，这些属性在DOM元素中一律使用布尔值来表示，如果值为‘true’，则代表添加了该属性，如果值为‘false’，则代表移除了该属性
  >
  >   比如：disabled、checked、selected

- 设置/修改元素样式属性

  > 通过操作DOM元素属性，来设置/修改HTML标签的CSS样式属性
  > >
  > > 1. 通过style属性操作CSS
  > >
  > >    语法：
  > >
  > >    > <u>object</u>.style.<u>style property</u> = <u>value</u>
  > >    >
  > >    > <u>对象</u>.style.<u>样式属性</u> = <u>值</u>
  > >
  > >    例：
  > >
  > >    ~~~javascript
  > >    //1.获取HTML页面中class为box的第一个<div>元素，并将其赋值于名为‘box’的对象中
  > >    let box = document.querySelector('div.box')
  > >    //2.修改这个<div>元素的背景颜色，宽度和上边缘距离
  > >    //注意：如果样式属性中有“-”连接符，需要转换为小驼峰命名法
  > >    box.style.backgroundColor = 'red'
  > >    box.style.width = '300px'
  > >    box.style.marginTop = '50px'
  > >    ~~~
  > >
  > > 2. 通过操类名（className）操作CSS
  > >
  > >    如果修改的样式比较多，直接用style属性修改比较繁琐，可以在CSS样式表中预先设置一个class选择器的样式，并修改元素的class属性的值以匹配这个样式
  > >
  > >    语法：
  > >
  > >    > <u>object</u>.className = '<u>cName</u>'
  > >    >
  > >    > <u>对象</u>.className = '<u>类名</u>'
  > >
  > >    注意：
  > >
  > >    > 由于class在JS中是关键字，所以用className来表示class属性
  > >    >
  > >    > 给className属性赋值会覆盖之前的class属性的值，如果需要添加一个类，则需要保留之前的类名
  > >
  > > 3. 通过classList操作类控制CSS
  > >
  > >    为了避免操作className属性导致的覆盖旧类名问题，可以通过classList方式追加和删除类名
  > >
  > >    语法：
  > >
  > >    > //追加一个类
  > >    >
  > >    > <u>object</u>.classList.add('<u>cName</u>')
  > >    >
  > >    > //删除一个类
  > >    >
  > >    > <u>object</u>.classList.remove('<u>cName</u>')
  > >    >
  > >    > //切换一个类（没有就添加，有就删除）
  > >    >
  > >    > <u>object</u>.classList.toggle('<u>cName</u>')

## 间歇函数

- 功能：每隔一段时间自动执行一段代码

- 开启定时器

  > 语法：
  >
  > > let <u>intervalID</u> = setInterval(<u>func</u>,<u>delay</u>)
  >
  > 参数：
  >
  > > **func**
  > >
  > > 要重复调用的函数，每经过<u>delay</u>毫秒后执行一次，第一次调用发生在delay毫秒之后
  > >
  > > **delay**
  > >
  > > 每次延迟的毫秒数，如果未指定，则其默认值为0
  >
  > 返回值：
  >
  > > 返回值<u>intervalID</u>是一个非零数值，用来标识通过setInterval()创建的定时器，这个值可以用来作为clearInterval()的参数来清除对应的定时器
  >
  > 例1：
  >
  > ~~~javascript
  > //每隔1000ms打印一次
  > setInterval(function(){
  > console.log('这是一段文字')
  > },1000)
  > ~~~
  >
  > 例2：
  >
  > ~~~javascript
  > //定义函数repeat
  > function repeat(){
  > console.log('这是一段文字')
  > }
  > //注意，此处函数名后面不能加括号，否则函数会立即执行，间歇函数失效
  > setInterval(repeat,1000)
  > ~~~

- 关闭定时器

  > 语法：
  >
  > > clearInterval(<u>intervalID</u>)
  >
  > 例：
  >
  > ~~~javascript
  > //clearInterval()可以用serInterval()的返回值作为参数来清除对应的定时器，因此可以将setInterval()的返回值赋值给一个变量，再将这个变量作为clearInterval的参数来关闭对应的定时器
  > let timer1 = setInterval(function(){
  > console.log('这是一段文字')
  > },1000)
  > clearInterval(timer1)
  > ~~~



