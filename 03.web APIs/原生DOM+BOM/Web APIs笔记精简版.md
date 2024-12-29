# DAY01-DOM元素基础

- 获取DOM元素

  1. 选择匹配的第一个元素

     - 语法

       ~~~javascript
       document.querySelector('CSS选择器')
       ~~~

     - 返回值为CSS选择器匹配的第一个元素，一个HTMLElement对象，如果没有匹配到对象，则返回null

  2. 选择匹配的多个元素

     - 语法

       ~~~javascript
       document.querySelectorAll('CSS选择器')
       ~~~

     - 返回值为CSS选择器匹配的NodeList对象集合

     - NodeList对象集合是一个伪数组，有长度有索引号，但无法使用操作数组的方法

- 设置/修改DOM元素内容

  1. document.write() 方法
     - 标签会被解析
  2. 元素.innerText 属性
     - 标签不会被解析
  3. 元素.innerHTML 属性
     - 标签会被解析

- 设置/修改DOM元素属性

  - 语法

    ~~~javascript
    // 修改标签元素属性
    object.属性 = 值
    
    // 修改CSS样式属性
    // 1.通过style属性操作CSS
    object.style.样式属性 = 值
    
    // 2.通过操作类名(className)操作CSS
    object.className = '类名'
    ~~~

  - 如果css样式属性有-连接符，需转换为小驼峰命名法

- classList的运用

  - 追加一个类

    <u>object</u>.classList.add('<u>cName</u>')

  - 删除一个类

    <u>object</u>.classList.remove('<u>cName</u>')

  - 切换一个类（没有就添加，有就删除）

    <u>object</u>.classList.toggle('<u>cName</u>')

  - 判断是否包含一个类（如果包含则返回true，否则返回false）

    <u>object</u>.classList.contains('<u>cName</u>')

- 表单元素中，值为布尔值的属性：disable、checked、selected等

- 定时器

  - 间歇函数

    let <u>timer</u> = setInterval(<u>func</u>, <u>delay</u>)

    clearInterval(<u>timer</u>)

  - 延时函数

    let <u>timer</u> = setTimeout(<u>func</u>, <u>delay</u>)

    clearTimeout(<u>timer</u>)

# DAY02-DOM事件基础

- 事件监听：

  <u>target</u>.addEventListener('<u>event</u>', <u>func</u>)

- 移除事件监听

  <u>target</u>.removeEventListener('<u>event</u>', <u>func</u>)

  需移除的事件监听内的函数不能是匿名函数

- 环境变量this，粗略规则为【谁调用，this就指代谁】，在定时器函数中的this指代window

- 事件类型

  1. 鼠标事件
     - click 鼠标点击
     - mouseenter 鼠标经过（没有冒泡效果）
     - mouseleave 鼠标离开（没有冒泡效果）
     - mouseover 鼠标经过（有冒泡效果）
     - mouseout 鼠标离开（有冒泡效果）
  
  2. 焦点事件
     - focus 获得焦点
     - blur 失去焦点
  
  3. 键盘事件
     - keydown 键盘按下触发
     - keyup 键盘抬起触发
  
  4. 文本事件
     - input 用户输入触发
  
  5. 滚动事件
  
     - scroll 元素或页面滚动
  
  6. 加载事件
  
     - load 资源加载完毕
  
       给window添加load事件，监听页面所有资源加载完毕
  
     - DOMContentLoaded 初始HTML文档加载完毕
  
       给document添加DOMContentLoaded事件，监听页面DOM加载完毕
  
  7. 窗口事件
  
     - resize
  
       窗口尺寸改变时触发的事件，添加给window

# DAY03-DOM节点操作

- 查找节点

  - 父节点

    - <u>Node</u>.parentNode

  - 子节点

    - <u>Node</u>.childNodes

      返回指定节点的所有子节点的集合

    - <u>Element</u>.children

      返回指定元素的子元素节点集合

  - 兄弟节点

    - <u>Node</u>.nextElementSibling

      下一个兄弟节点

    - <u>Node</u>.previousElementSibling

      上一个兄弟节点

- 增加节点

  - 新建节点

    let <u>newElement</u> = document.creatElement('<u>tagName</u>')

  - 克隆节点

    let <u>dupNode</u> = <u>node</u>.cloneNode(<u>booleanValue</u>)

    若布尔值为true，则该节点后代也会被克隆，若为false则只克隆该节点本身

  - 追加节点

    - <u>parentNode</u>.appendChild(<u>newNode</u>)

      插入到父节点的末尾

    - <u>parentNode</u>.insertBefore(<u>newNode</u>，<u>referenceNode</u>)

      插入到父节点的某个子节点前

- 删除节点

  let <u>oldChild</u> = <u>node</u>.removeChild(<u>child</u>)

  删除节点必须通过其父节点删除

  返回值为被删除的节点，因此如果之后需要加回这个节点，可以拿一个变量来存放它

- 时间对象

  - 实例化事件对象

    - let date = new Date()

      获取当前时间

    - let date = new Date('2000-01-01 00:00:00')

      获取指定时间

    - 时间对象方法

      | 方法          | 作用           | 说明                                               |
      | :------------ | -------------- | -------------------------------------------------- |
      | getFullYear() | 获取年份       | 获取四位数字的年份                                 |
      | getMonth()    | 获取月份       | ==取值为0~11（使用时需要给返回值+1获得正确月份）== |
      | getDate()     | 获取月份中的天 | 取值为1~31                                         |
      | getDay()      | 获取星期中的天 | 取值为0~6（0代表周日）                             |
      | getHours()    | 获取小时       | 取值为0~23                                         |
      | getMinutes()  | 获取分钟       | 取值为0~59                                         |
      | getSeconds()  | 获取秒         | 取值为0~59                                         |
    
    - 时间对象动态化方法
    
      ~~~javascript
      let date = new Date('2000-01-01 00:00:00')
      date.toLocaleString() // 2000/1/1 00:00:00
      date.toLocaleString('en') // 1/1/2000, 12:00:00 AM
      ~~~
    
    - 时间戳
    
      ~~~javascript
      // 1. getTime()方法
      let date = new Date('2000-01-01 00:00:00')
      date.getTime()
      // 2. +new Date()方法
      +new Date() // 获取当前时间的时间戳
      +new Date('2000-01-01 00:00:00') // 获取指定时间的时间戳
      // 3. 使用 Date.now()
      Date.now() // 只能获得当前时间的时间戳
      ~~~
      
      - 通过时间戳得到的是毫秒，需要转换为秒再计算
      - 转换公式：
        - d = parseInt(总秒数/60/60/24); // 计算天数
        - h = parseInt(总秒数/60/60%24); // 计算小时数
        - m = parseInt(总秒数/60%60); // 计算分钟数
        - s = parseInt(总秒数%60); // 计算秒数
    

# DAY04-DOM事件高级

- 事件对象的获取

  ~~~javascript
  document.addEventListener('click',function (e) {
  console.log(e)
  })
  ~~~

  在事件监听中的回调函数的第一个形参就是事件对象，一般命名为e或ev或event

- 事件对象常用属性

  - e.type

    获取当前事件类型

  - e.clientX/e.clientY

    获取光标相对于浏览器可见窗口左上角的位置

  - e.offsetX/e.offsetY

    获取光标相对于当前DOM元素左上角的位置

  - e.pageX/e.pageY

    获取光标相对于网页页面（HTML）左上角的位置

  - e.key

    用户按下的按键的值

  - e.target

    触发事件的元素

    - e.target.<u>tagName</u>

      触发事件的元素的标签名（值为大写）

    - e.target.<u>id</u>

      触发事件的元素的id名

    - e.target.dataset.<u>自定义属性名</u>

      触发事件的元素的data-自定义属性的值
  
- 自动触发某事件方法

  事件目标.事件名称()

- 事件流

  - 事件冒泡
    - 当一个元素触发后，同样的事件再该元素的所有祖先元素中依次被触发
    - 事件冒泡默认存在

  - 事件捕获

    - 从DOM的根元素开始去执行对应的事件

    - ~~~javascript
      DOM.addEventListener(事件类型, 事件处理函数, 是否使用捕获机制) // 默认不使用捕获机制
      ~~~

  - 阻止事件流动

    - 阻止事件流动，就是将事件限制在当前元素内

    - 语法

      e.stopPropagation()

  - 阻止事件的默认行为

    - 如：链接点击不跳转，表单域不提交

    - 语法

      e.preventDefault()

- 事件委托

  - 当一系列子元素需要绑定事件时，可将事件绑定给其父元素，利用事件冒泡的特点，子元素也可触发该事件
  - 利用 e.target 属性可以获得真正触发事件的子元素

# DAY05-网页特效

- document.documentElement的返回对象为HTML元素

- 获取元素大小和位置的属性

  - scroll家族

    - scrollWidth/scrollHeight

      元素内容的宽高（不含滚动条，包括不可见部分）

    - scrollLeft/scrollTop

      元素内容因滚动条而滚出显示区域的距离
      
      可以修改

  - offset家族

    - offsetWidth/offsetHeight

      元素自身的宽高（包含padding和border）

    - offsetLeft/offsetTop

      元素距离自己最近一级有定位的父级元素的距离

  - client家族

    - clientWidth/clientHeight

      元素可见部分的宽高（不包含border、滚动条等）

    - clientLeft/clientTop
  
      元素border宽度

# DAY06-BOM操作浏览器

- location.herf获取完整的URL地址，对其赋值用于地址跳转

- localStorage

  - localStorage.setItem(key, value)

    存储数据

  - localStorage.getItem(key)

    获取数据

  - localStorage.removeItem(key)

    删除数据

- JSON字符串

  - JSON.stringify()

    将复杂数据类型转换成JSON字符串

  - JSON.parse()

    将JSON字符串转换成复杂数据类型
  
- JS执行机制

  - JavaScript是单线程的
  - 同步任务
    - 同步任务都在主线程上执行，形成一个执行栈
  - 异步任务
    - JS的异步任务是通过回调函数实现的
    - 一般而言，异步任务有以下三种类型
      1. 普通事件，如click、resize等
      2. 资源加载，如load、error等
      3. 定时器，包括setInterval、setTimeout等
    - 异步任务相关添加到任务队列（消息队列）中
  - 执行过程
    1. 先执行执行栈中的同步任务
    2. 异步任务放入任务队列中，进入等待状态
    3. 一旦执行栈中的所有同步任务执行完毕，系统依次读取任务队列中的异步任务，被读取的异步任务结束等待状态，进入执行栈开始执行

# DAY07-正则表达式

- 判断是否由符合规则的字符串

  <u>正则表达式</u>.test(<u>被检测的字符串</u>)

  匹配则返回true，否则返回false

- 检索符合规则的字符串

  <u>正则表达式</u>.exec(<u>被检测的字符串</u>)

  匹配成功则返回一个数组，否则返回null

- 替换匹配内容

  <u>字符串</u>.replace(<u>正则表达式</u>, <u>替换的文本</u>)

- 修饰符

  i：字母不区分大小写

  g：匹配所有满足正则表达式的结果

  