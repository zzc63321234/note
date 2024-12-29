

## jQuery核心函数

- jQUery函数介绍

  - jQuery的核心函数就是$即jQuery，是一个全局函数
  -  $ === jQuery
  - 可以作为一般函数调用，且根据传递的参数类型、格式不同，功能就会完全不同
  - 可以作为对象调用其定义好的方法

- jQuery函数的使用

  - 当成一般函数使用：

    $(参数)
  
    - 参数为函数：相当于在DOM加载完成后运行该函数
      - 相当于$(document).ready(函数)
    - 参数为选择器字符串：查找所有匹配的DOM元素，返回包含所有DOM元素的jQuery对象
    - 参数为DOM元素：将DOM元素对象包装为jQuery对象返回，如$(this)，$(document)
    - 参数为标签字符串：创建标签DOM元素对象，并包装为jQuery对象返回
  
  - 当成对象使用（工具方法）：
  
    $. xxx
  
    - $.each()
  
      - 遍历对象或数组
    
        ~~~javascript
        $.each(要遍历的数组, function(index, item){
            console.log(index, item)
        })
        $.each(要遍历的对象, function(key, value){
            console.log(key, value)
        })
        ~~~
    
    - $.trim(字符串)
    
      - h去除字符串前后空格，将结果返回
      
    - $.type()
    
      - 检测数据类型
    
    - $.isArray()
    
      - 检测当前值是否为数组
    
    - $.isFunction()
    
      - 检测当前值是否为函数
    
    - $.parseJSON()
    
      - 将json字符串转化为复杂数据类型

## jQuery核心对象

- jQuery核心对象介绍
  - 即执行jQuery核心函数返回的对象
  - jQuery对象是内部包含任意个DOM元素对象的伪数组对象
  - jQuery对象包含很多属性和方法，可以用来更方便地操作DOM
- jQuery对象命名规范
  - 通常通过jQuery获取的元素集合，称作jQuery对象，在命名的时候会在前边添加一个$

### jQuery核心对象的属性和方法

- 读写合一

  - 单参数方法：传值为写，不穿值为读
  - 双参数方法：传两个值为写，传一个值为读

- 内置隐式遍历

  - 设置内容时，会自动遍历
  - 读取内容时，只读取第一个DOM元素

- jQuery对象的方法执行结束时，默认会将执行后的jQuery对象返回

#### 属性和文本方法

- length/size()

  - 获取到的DOM元素的个数

- get(index)

  - 获取jQuery对象中对应序号的DOM元素

- each(function(index, item){})

  - 遍历jQuery对象中的DOM元素
  - 回调函数中的this指代每一次当前正在被遍历的DOM元素，因此可以使用DOM的属性

- index()

  - 获取当前第一个DOM元素在所有兄弟元素中的索引号

- attr(‘属性名', '属性值')

  - 传一个值：获取第一个DOM元素的属性值

  - 传两个值：设置DOM元素的属性值
  - attr用于操作属性值可自己定义的属性

- prop('属性名', 布尔值)

  - prop用于操作属性值固定的属性（布尔值属性），如checked、disabled

- removeAttr('属性名')

  removeProp('属性名')

  - 删除DOM元素的属性

- addClass('类名')

  - 添加类名

- removeClass('类名')

  - 移除类名

- html('文本内容')

  - 传值：设置标签内的文本内容

  - 不传值：获取标签内的文本内容

- val('文本内容')

  - 传值：设置文本框中的文本

  - 不传值：获取文本框中的文本

- click(函数)

  - 绑定点击事件

#### 修改样式方法

- style

  - css('样式名')

    - 根据样式名得到对应的样式属性值
    - 读取的是最终生效的样式

  - css('样式名', '属性值')

    - 设置一个样式

  - css(样式对象)

    - 设置多个样式

    ~~~javascript
    $('.box').css({
        'width':'200px',
        'height':'200px'
        'color':'#000',
    })
    ~~~

- 位置坐标

  - offset()
    - 返回一个对象，包含left和top两个属性，表示元素相对于页面左上角的位置的像素数值
    - 可在参数中传递一个对象来改变元素的位置
  - position()
    - 返回一个对象，包含left和top两个属性，表示元素相对于其包含块左上角的位置的像素数值
  - scrollTop(数值)/scrollLeft(数值)
    - 读写元素的滚动条坐标

- 尺寸

  - width()/height()
    - 元素content的宽高
  - innerWidth()/innerHeight()
    - 元素content+padding的宽高
  - outerWidth(布尔值)/outerHeight(布尔值)
    - 元素content+padding+border(+margin)的宽高
    - 允许传递一个布尔值参数，默认false，如果为true，则在原有的基础之上包含margin的值

#### 过滤筛选方法

- 与过滤选择器的区别

  - 过滤选择器，是在元素查询的阶段，传入到jQuery核心函数当中，作为匹配元素的规则
  - 过滤筛选方法，是对现有的jQuery对象里面的元素进行筛选

- 过滤

  在jQuery对象内部的元素中找出匹配的元素，封装成新的jQuery对象返回

  - first()
  - last() 
  - eq(序号)
  - filter('选择器')
    - 筛选出符合选择器的元素
  - has('选择器')
    - 筛选有符合选择器的子元素的元素
  - not('选择器')
    - 排除符合选择器的元素

- 查找

  查找jQuery对象内部元素的子孙/兄弟/父母元素，封装成新的jQuery对象返回

  如果查找结果对jQuery对象内部的不同元素的结果不同，则分别获取这些元素

  - children('选择器')
    - 获取子元素集合，如果传入选择器，则用选择器过滤这个子元素集合
  - find('选择器')
    - 获取后代元素集合，如果传入选择器，则用选择器过滤这个后代元素集合
  - parent()
    - 获取父级元素集合
  - prevAll('选择器')/nextAll('选择器')
    - 获取之前/之后的兄弟元素集合，如果传入选择器，则用选择器过滤这个元素集合
  - prev()/next()
    - 获取前一个/后一个兄弟元素
  - siblings('选择器')
    - 获取所有兄 //弟元素集合，如果传入选择器，则用选择器过滤这个元素集合

#### 文档处理(CUD)方法

- 增加
  - 内部插入（作为子元素插入）
    - $('div').append('<span>新增的span<span>')
      - 将新增的span插入div中最后的位置
    - $('<span>新增的span<span>').appendTo('div')
      - 将新增的span插入div中最后的位置
    - $('div').preppend('<span>新增的span<span>')
      - 将新增的span插入div中最前的位置
    - $('<span>新增的span<span>').preppendTo('div')
      - 将新增的span插入div中最前的位置
  - 外部插入（作为兄弟元素插入）
    - $('div').before()('<span>新增的span<span>')
      - 将新增的span插入div之前
    - $('div').after()('<span>新增的span<span>')
      - 将新增的span插入div之后
- 移除
  - remove()
    - 删除元素
  - empty()
    - 将自己的子元素删除 
- 更新
  - replaceWith()

#### 事件方法

- 绑定事件

  jQuery中所有事件都是使用dom2级事件绑定方式

  - click()/mouseenter()/mouseleave()/mouseover()/mouseout()/focus()/blur()
  - hover(悬浮时的回调函数, 取消悬浮时的回调函数)
    - 如果只传一个回调函数，则鼠标移入和移除都会执行这个回调函数
  - on('事件名 事件名2 ... 事件名n',回调函数)
    - 通用事件绑定方法
    - 可同时给多个事件绑定一个回调函数

- 解绑事件

  - off('事件名 事件名2 ... 事件名n'')
    - 如果不传值，则解绑该jQuery对象的所有事件

- 事件委托

  - 绑定
    - 父元素.delegate('子元素选择器', '事件名', 回调函数)
      - 回调函数中的this指向子元素
    - 父元素.on('事件名', '子元素选择器', 回调函数)
      - 回调函数中的this指向子元素
      - 和上一种实现方式完全相同，解绑方式也相同

  - 解绑
    - 父元素.undelegate('事件名')
    - 父元素.off('事件名')

#### 动画

- faseOut(毫秒数)/fadeIn(毫秒数)/fadeToggle(毫秒数)

  - 淡出/淡入/切换淡入淡出

- show(毫秒数)/hide(毫秒数)/toggle(毫秒数)

  - 显示/隐藏/切换显示隐藏
  - 如果不传递时间参数，默认没有过渡效果

- 自定义动画

  - animate(css字符串对象, 毫秒数)

    - 连续执行animate()方法时，会将动画放入动画队列，依次执行

  - stop(布尔值1, 布尔值2)

    - 立即停止当前动画
    - 布尔值1代表是否清空动画队列，默认为false
    - 布尔值2代表当前动画是否执行完整，false为停留在当前位置，true为停留在当前动画结束位置，默认为false
    - 在连续执行自定义动画时插入stop()来清空原先动画队列，可防止动画队列堆积

  - 移动到left:200px的位置

    - animate({

      'left':‘200px’

      },2000)

  - 基于当前位置向右移动200

    - animate({

      'left':’+=200‘

      },2000)

## jQuery选择器

- 基本
  * #id
  * tagName/*
  * .class
  * selector1,selector2,selector3: 并集
  * selector1selector2selector3: 交集
- 层次
  * 找子孙后代, 兄弟元素
  * selector1>selector2: 子元素
  * selector1 selector2: 后代元素
- 过滤
  * 在原有匹配元素中筛选出其中一些
  * :first
  * :last
  * :eq(index)
  * :lt
  * :gt
  * :odd
  * :even
  * :not(selector)
  * :hidden
  * :visible
  * [attrName]
  * [attrName=value]
- 表单
  * :input
    - 获取所有的表单元素：input、按钮、文本域、下拉框
  * :text
  * :checkbox
  * :radio
  * :checked: 选中的

## 扩展方法

- 扩展工具方法
  - $.extend(函数)
- 扩展jQuery对象方法
  - $.fn.entend(函数)
    - this指向调用方法的jQuery对象
- 多库共存
  - jQuery.noConflict()
    - 当$和其他库发生命名冲突时，优先加载jQuery然后调用这个方法，可以让jQuery释放对于$的使用权，之后jQuery核心函数只能用全称