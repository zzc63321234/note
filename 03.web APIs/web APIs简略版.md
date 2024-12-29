## DOM

- DOM(Document Object Model)，文档对象模型

  - 文档指整个网页
  - DOM将网页中的所有内容都转换为对象
  - 模型用来体现节点之间的关系

- 网页由三个部分组成：结构（HTML）、表现（CSS）、行为（JavaScript）

  DOM提供了大量的对象，使得可以通过JavaScript来完成对网页的操作

  document是整个DOM数中最顶级的对象，通过document可以获取其他任意的DOM对象

- 节点(Node)

  - 网页中的所有内容都可以作为节点

  - 节点有不同的类型

    - 文档节点 document

      表示整个网页

    - 元素节点 element

      各种标签都属于元素节点

    - 属性节点 attribute

      标签中的属性属于属性节点

    - 文本节点 text

      标签中的文字属于文字节点

### 事件

- 事件是用户和网页的交互瞬间

  #### 绑定事件

  - 绑定事件的方式

    1. ```html
       <button onclick="alert('...');">click</button>
       ```

    2. ```javascript
       let btn = document.querySelector('button')
       btn.onclick = function(){
           alert('...')
       }
       ```

       - 使用null覆盖onclick属性，可实现事件解绑
       - 新绑定的事件会覆盖之前绑定的同名事件
       - 无法在捕获阶段执行

    3. ```javascript
       let btn = document.querySelector('button')
       btn.addEventListener('click', function(e){
           alert('...')
           console.log(e) // e指向绑定的事件
           console.log(e.target) // e.target指向触发事件的元素
       })
       ```

       - 元素.removeEventListener('事件', 回调函数名称)可实现事件解绑，但回调函数不得是匿名函数
       - 第三个实参若为true，则在捕获阶段执行


  #### 事件对象

  - 每当事件的回调函数被调用一次时，浏览器就会传递一个事件对象给回调函数的第一个形参

  - 事件对象存储了事件相关的一切信息

  - 事件对象的属性

    - event.clientX/event.clientY

      事件触发时鼠标指针相对于窗口的坐标

    - event.pageX/event.pageY

      事件触发时鼠标指针相对于页面的坐标

    - event.offsetX/event.offsetY

      事件触发时鼠标指针对于触发事件的元素的位置的相对坐标

  - 默认行为

    - 默认行为指当前事件触发时元素默认会做的事情
    - 阻止默认行为方法
      1. 在事件监听的回调函数的末尾return false
      2. e.preventDefault()

  #### 事件流

  - 事件流分为三个阶段

    1. 事件捕获
       - 指事件从最外层元素开始向内部元素逐级触发事件
       - 默认情况下捕获阶段不会触发事件
       - 如果希望在捕获阶段触发事件，可以将addEventListener()的第三个参数设置为true
    2. 目标元素(触发事件的元素)
    3. 事件冒泡
       - 事件触发时，从目标元素开始向外层元素逐级依次触发同名事件
       - 事件冒泡默认存在

  - 阻止事件流

    - event.stopPropagation()

      阻止事件流

    - event.cancelBubble = true

      阻止事件冒泡


  #### 事件类型

  - 鼠标事件

    - 事件名称

      - 鼠标经过事件

        - mouseover和mouseout有冒泡效果
        - mouseenter和mouseleave没有冒泡效果

      - 鼠标按下/松开

        - mousedown
        - mouseup

      - contextmenu

        - 右键/menu键触发
        - 默认行为是弹出菜单列表

      - wheel

        - 鼠标滚轮滚动时触发

        - 事件对象可获取滚动方向

          - e.deltaY

            垂直滚动距离，向下滚动为正值

  - 键盘事件

    - 键盘事件只能绑定给document、html、body或可以获取焦点的元素
    - 事件对象属性
      - key
        - 获取触发键盘事件的按键名称，字母区分大小写
      - ctrlKey/shiftKey/altKey
        - 检测ctrl/shift/alt是否按下，返回布尔值
    - 事件名称
      - keydown
        - 按键按下触发
        - 如果长按则会连续触发
      - keyup
        - 按键松开触发
      - keypress
        - 当一个能产生字符串的按键被按下时触发

  ### DOM元素

  #### 查询元素

  - 通过document对象查询

    - document.querySelector()

    - document.querySelectorAll()

    - document.documentElement

      获取页面的根元素（HTML）

    - document.body

      获取页面的body元素

  - 通过元素查询

    - element.getElementByTagName()

      根据标签名获取元素中的指定的后代元素

    - element.childNodes

      - 获取元素的所有子节点

    - element.children

      - 获取元素的所有子元素

    - element.firstChild

      - 获取元素的第一个子节点

    - element.firstElementChild

      - 获取元素的第一个子元素

    - element.lastChild

      - 获取元素的最后一个子节点

    - element.lastElementChild

      - 获取元素的最后一个子元素

    - element.parentNode

      - 获取元素的父元素

    - element.previousSibling

      - 获取元素的前一个兄弟节点

    - element.previousElementSibling

      - 获取元素的前一个兄弟元素

    - element.nextSibling

      - 获取元素的后一个兄弟节点

    - element.nextElementSibling

      - 获取元素的后一个兄弟元素

  - HTMLCollection可根据页面中的DOM元素实时更新

  - NodeList是静态的

  #### 创建、插入和删除元素

  - 创建元素

    - document.createElement(标签名)
      - 创建一个新元素
    - document.createTextNode(文本内容)
      - 创建一个新的文本节点

  - 插入元素

    - 父节点.appendChild(要插入的元素)

      - 向父节点的末尾加入一个子节点

    - 元素.insertAdjacentElement(‘位置’,  要插入的元素)

      - 向元素的指定位置插入一个元素

      - 位置参数

        ~~~html
        <!-- beforebegin -->
        <p>
            <!-- afterbegin -->
            text
            <!-- beforeend -->
        </p>
        <!-- afterend -->
        ~~~

    - 元素.insertAdjacentHTML(‘位置’, 'HTML代码')

    - 元素.insertAdjacentText(‘位置’, '文本内容')

    -  父节点.insertBefore(新节点, 父节点的子节点)

       - 将新节点插入到父节点的子节点前面

    - 父节点.replaceChild(新节点, 父节点的子节点)

      - 将新节点替换父节点的子节点

  - 复制节点

    - 节点.cloneNode()
      - 对节点进行浅复制（只复制节点本身）
    - 节点.cloneNode(true)
      - 对节点进行深复制（复制节点本身以及所有的后代节点）

  - 删除元素

    - 子节点.parentNode.removeChild(子节点)
      - 通过父节点删除子节点
    - 子节点.remove()

  #### DOM元素属性

  - 元素的标签属性

    - 元素.属性名

      - ele.name

      - ele.value

      - ele.id

      - ele.className

        class在js中为保留字，需用className代替

    - 表单属性中，当checked属性值为true时，代表选框被选中

      当disabled属性为true时，代表表单不可用

  - 元素的CSS样式属性

    - 元素.style.样式名

      - 元素的行内样式，即写在元素标签style属性上的样式，支持读写
    - getComputedStyle(元素，伪元素字符串)

      - 获取元素当前生效的样式以对象的形式返回，只读
    - 元素.classList
      - 元素.classList.add('类名')
        - 添加一个类
      - 元素.classList.remove('类名')
        - 移除一个类
      - 元素.classList.toggle('类名')
        - 切换一个类（没有就添加，有就移除）
      - 元素.classList.replace('旧类名', ’新类名')
        - 替换一个类
      - 元素.classList.contains('类名')
        - 检查是否包含一个类，返回布尔值
    - 带横杠的样式名需转换成小驼峰命名法

  - client、offset、scroll相关属性

    - client
      - clientWidth/clientHeight
        - 获取当前元素的content和padding的总大小，不包含滚动条
      - clientLeft/clientTop
        - 获取当前元素的border大小
    - offset
      - offsetWIdth/offsetHeight
        - 获取当前元素的content、padding、border的总大小
      - offsetLeft/offsetTop
        - 获取当前元素距离其最近一级有定位的父元素的距离
      - offsetParent
        - 获取当前元素的定位父元素(offsetParent)
        - 如果所有祖先元素都没有定位，则返回body
    - scroll
      - scrollWidth/scrollHeight
        - 获取当前元素内容本身（包含溢出部分）的大小
      - scrollLeft/scrollTop
        - 获取当前元素滚动的距离
        - 可以赋值改变该属性
        - 页面的滚动距离
          - 不同浏览器对检测页面滚动距离使用的对象不同，有的是html，有的是body
    - 在一个有滚动条的元素中，当scrollHeight - scrollTop === clientHeight时，说明垂直滚动到底
    - 为避免页面缩放导致的小数计算精度问题，需将计算结果取整：Math.floor(scrollHeight - scrollTop) === clientHeight

  - 文本节点的nodeValue属性提供了该节点的文字内容

### 自定义属性

- 固有属性

  标签天生自带的属性，如class、id等，可以直接用点语法操作

- 自定义属性

  自己添加的属性，无法直接在DOM对象中找到，需使用对应的API进行操作

  - getAttribute('<u>属性名</u>') // 获取自定义属性
  - setAttribute('<u>属性名</u>', '<u>属性值</u>') // 设置自定义属性
  - removeAttribute('<u>属性名</u>') // 删除自定义属性

- data-自定义属性

  html5中推出的data-自定义属性，为自定义属性定义规则

  属性命名方式：data-<u>自定义属性名</u>

  从DOM中获取属性值的方式：<u>element</u>.dataset.<u>自定义属性名</u>，此处自定义属性名会被转换为全小写

  ### 环境变量补充

  - 以函数形式调用，this是window
  - 以方法的形式调用，this是调用方法的对象
  - 以构造函数的形式调用，this是新建的对象
  - call()，apply()，bind()可以指定函数的this
  - 箭头函数中的this由它外层的作用域决定
  - 事件监听的回调函数中，this是绑定事件的对象

  ## BOM

  - BOM(Browser Object Model) 浏览器对象模型
  - BOM提供了一组对象，用来完成对浏览器的各种操作
  - BOM对象：
    - History
      - 浏览器的历史访问记录（同一标签页内）
    - Location
      - 浏览器的地址栏
    - Navigator
      - 浏览器信息
    - Screen
      - 设备屏幕信息
  - BOM对象都是Window对象的属性
  - History
    - history.length
      - 当前标签内访问过的标签数量
    - history.forward()
      - 相当于浏览器前进按钮
    - history.back()
      - 相当于浏览器后退按钮
    - history.go(数字)
      - 前进/后退指定次数
      - 正数为前进，负数为后退
  - Location
    - 如果直接读取Location，则可以获取到地址栏的url内容
    - 将url字符串赋值给location，可实现页面跳转，通过这种方式跳转页面会留下历史访问记录
    - location.port
      - 获取端口号
    - location.assign(url字符串)
      - 跳转地址，和直接给location赋值的作用一样
    - location.replace(url字符串)
      - 跳转地址，不会产生历史访问记录
    - location.reload()
      - 重新加载网页，相当于刷新按钮
      - 参数为true时，强制刷新网页（重新加载网页所有内容，包括缓存），相当于Ctrl+F
  - Navigator
    - 通过Navigator识别不同的浏览器
    - navigator.userAgent
      - 返回一个表示浏览器信息的字符串offs
