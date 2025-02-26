# CSS基础

### CSS属性顺序推荐

- 按此顺序写，浏览器执行效率更高
  | 顺序 |     类别      |                         属性                          |
  | :--: | :-----------: | :---------------------------------------------------: |
  |  1   |   布局属性    | display、position、float、clear、visibility、overflow |
  |  2   | 盒子模型+背景 |  width、height、margin、padding、border、background   |
  |  3   | 文本内容属性  | color、font、text-decoration、text-align、line-height |
  |  4   |   点缀属性    |    cursor、border-radius、text-shadow、box-shadow ​    |

### 选择器种类

1. 标签选择器

2. 类选择器

3. id选择器

4. 通配符选择器

   ~~~css
   *{
       margin: 0;
       padding: 0;
   }
   ~~~

   - 通配符选择器的作用：找到页面中所有的标签，设置样式

5. 属性选择器

   - 场景：通过元素上的HTML属性来选择元素，常用用于选择input标签

   - 选择器语法

     |    选择器     |                   功能                   |
     | :-----------: | :--------------------------------------: |
     |    E[attr]    |         选择具有attr属性的E元素          |
     | E[attr="val"] | 选择具有attr属性并且属性值等于val的E元素 |

### 字体样式

- font-size 字体大小

  取值：数字+px

- font-weight 字体粗细

  取值：

  - 关键字：
    - normal 正常
    - bold 加粗
  - 纯数字：100~900的整百数
    - 400 正常
    - 700 加粗
  - 注意：不是所有字体都提供了九种粗细，因此部分取值页面中无变化，实际开发中，使用关键字取值较多

- font-style 字体样式（是否倾斜）

  - 取值
    - normal 正常（默认值）
    - italic 倾斜

- 常见字体系列

  - 无衬线字体（sans-serif）
    - 特点：文字笔画粗细均匀，并且首尾无装饰
    - 场景：网页中大多采用无衬线字体
    - 常见该系列字体：黑体、Arial
  - 衬线字体（serif）
    - 特点：文字笔画粗细不均，并且首位有笔锋装饰
    - 场景：报刊书籍中应用广泛
    - 常见该系列字体：宋体、Times New Roman
  - 等宽字体（monospace）
    - 特点：每个字母或文字的宽度相等
    - 场景：一般用于程序代码编写，有利于代码的阅读和编写
    - 常见该系列字体：Consolas、fira code

- 字体系列font-family

  - 属性名：font-family
  - 常见取值：具体数字1，具体数字2，……，字体系列
  - 渲染规则
    1. 从左往右按照顺序查找，如电脑中未安装该字体，则显示下一个字体
    2. 如果都不支持，此时会根据操作系统，显示最后字体系列的默认字体
  - 注意点：
    1. 如果字体名称中存在多个单词，推荐使用引号包裹
    2. 最后一项字体系列不需要引号包裹
    3. 网页开发时，尽量使用系统常见自带字体，保证不同用户浏览网页都可以正确显示

- 字体复合属性

  - font: <u>style</u> <u>weight</u> <u>size</u> <u>family</u>；
  - 只能省略前两个，如果省略了相当于设置了默认值

### 文本样式

- 文本缩进
  - 属性名：text-indent
  - 取值：
    - 数字+px
    - 数字+em（推荐，1em=当前标签的font-size的大小）

- 文本水平对齐方式
  - 属性名：text-align
  - 取值：
    - left 左对齐
    - center 居中对齐
    - right 右对齐
  - 对以下元素的父元素设置text-align: center，可使它们水平居中
    - 文本
    - span标签
    - a标签
    - input标签
    - img标签

- 文本修饰

  - 属性名：text-decoration


  - 取值：
    - underline 下划线
    - line-through 删除线
    - overline 上划线
    - none 无装饰线

- 行高

  - 属性名：line-height

  - 作用：控制一行的上下间距

  - 取值：

    - 数字+px
    - 倍数（当前标签font-size的倍数）

  - 应用：

    - 让单行文本垂直居中可以设置line-height: 文字父元素高度
    - 网页精准布局时，会设置line-height: 1 来取消上下间距

  - line-height可以写在font复合属性中：

    font: <u>style</u> <u>weight</u> <u>size</u>/l<u>ine-height</u> <u>family</u>;

### 颜色常见取值

|  颜色表示方式  |            表示含义             |             属性值举例             |
| :------------: | :-----------------------------: | :--------------------------------: |
|     关键词     |         预定义的颜色名          |          red、green、blue          |
|   rgb表示法    | 红绿蓝三原色，每项取值范围0~255 |    rgb(0,0,0)、rgb(255,255,255)    |
|   rgba表示法   | rgb+表示透明度的a，a取值范围0~1 |       rgba(255,255,255,0.5)        |
| 十六进制表示法 | #开头，将数字转换成十六进制表示 | #000000、#ff0000，简写：#000、#f00 |

### 选择器进阶

- 复合选择器

  - 后代选择器：空格

    - 语法：选择器1 选择器2

    - 后代包括下面的每一代

  - 子代选择器：>

    - 语法：选择器1 > 选择器2

    - 子代只包括下一代
  
- 并集选择器

  - 作用：同时选择多组标签，设置相同的样式
  - 语法：选择器1,选择器2
  - 并集选择器中的每组选择器可以是基础选择器或者复合选择器
  - 并集选择器中的每组选择器通常一行写一个，提高代码的可读性
  
- 交集选择器

  - 作用：选中页面中同时满足多个选择器的标签
  - 语法：选择器1选择器2
  - 交集选择器找到页面中既能被选择器1选中，又能被选择器2选中的标签
  - 交集选择器中如果有标签选择器，标签选择器必须写在最前面
  
- 伪类选择器

  - 伪类选择器选中的是元素的某种状态
  - hover伪类选择器

    - 作用：选中鼠标悬停在元素上的状态，设置样式
    - 语法：选择器:hover
  
- 选中下一个紧挨着的兄弟元素

  - 语法：选择器1+选择器2

  - 作用：当选择器2选中的元素是选择器1选中的元素的下一个紧挨着的兄弟元素时，选中符合这个条件的选择器2选中的元素

- 选中之后的兄弟元素

  - 语法：选择器1~选择器2
  - 作用：当选择器2选中的元素，是在选择器1选中的元素之后的兄弟元素时，选中符合这个条件的选择器2选中的元素
  - 当需要选中选择器1选中的元素之后的所有兄弟元素时，可写成“选择器1\~”或“选择器1\~*“

### emmet语法

- 通过简写语法，快速生成代码

  |        生成内容        |    写法示例     |
  | :--------------------: | :-------------: |
  |         标签名         |       div       |
  |   div标签+class属性    |      .red       |
  |     div标签+id属性     |      #one       |
  | 根据交集选择器生成内容 |    p.red#one    |
  | 根据子代选择器生成内容 |      ul>li      |
  |     标签+内部文本      | ul>li{文本内容} |
  | 创建多个+内部数字序号  |   ul>li{$}*3    |
  |        同级标签        |      h2+p       |


### 背景

- 背景色
  - 属性名background-color
  - 默认值是透明：rgba(0,0,0,0)、transparent
  
- 背景图片
  - backgroun-image: url('路径');
  - url中的引号可以省略
  - 默认是在水平和垂直方向平铺的
  - 背景图片不能撑开盒子

- 背景平铺
  - 属性名：background-repeat
  - 属性值：
    - repeat 水平和垂直方向都平铺（默认值）
    - no-repeat 不平铺
    - repeat-x 沿水平方向平铺
    - repeat-y 沿垂直方向平铺
  
- 背景位置
  - background-position: 水平方向位置 垂直方向位置;
  - 属性值：
    1. 方位名词（最多只能表示9个位置）
       - 水平方向：left center right
       - 垂直方向：top center bottom
    2. 坐标（数字+px），表示图片左上角
  - 方位名词取值和坐标取值可以混用

- 背景图大小

  - background-size: 宽度 高度;

  - 取值

    - 数字+px

    - 百分比

      相对于当前盒子自身的宽高百分比

    - contain

      包含，将背景图片等比例放大，直到某一边刚好达到盒子的100%

    - cover

      覆盖，将背景图片等比例放大，直到刚好填满整个盒子没有空白

- 背景复合属性

  - background: <u>color</u> <u>image</u> <u>repeat</u> <u>position</u>/<u>size</u>;
  - 可以省略，可以不分先后顺序
  - 在pc端，如果盒子大小和背景图片大小一样，可以直接写background: url()；

### 元素显示模式

- 块级元素block
  - 独占一行
  - 宽度默认是父元素的宽度，高度默认由内容撑开
  - 可以设置宽高
- 行内元素inline
  - 一行可以显示多个
  - 宽度和高度默认由内容撑开
  - 不可以设置宽高
- 行内块元素inline-block
  - 一行可以显示多个
  - 可以设置宽高
  - 代表标签：input、textarea、button、select……
  - 特殊情况：img标签属于替换元素，在chrome调试工具中显示结果是inline，具有行内块元素的特点
- 元素显示模式转换
  - 属性：display
  - 属性值：
    - block
    - inline-block
    - inline
- HTML嵌套规范注意点
  1. 块级元素一般作为大容器，可以嵌套：文本、块级元素、行内元素、行内块元素等
     - 但是p标签中不要嵌套div、p、h等块级元素
  2. a标签内部可以嵌套任意元素
     - 但是a标签不能嵌套a标签

- CSS特性
  - 继承性
    - 子元素有默认继承父元素样式的特点（文字控制属性都可以继承）
    - 可以通过调试工具判断是否可以继承，亮显的样式名表示可以继承
    - 继承失败的特殊情况，如a标签的color属性，h系列标签的font-size属性，其实属性被继承，但是被浏览器对其标签设置的默认样式给覆盖掉了
  - 层叠性
    - 特性
      1. 给同一个标签设置不同的样式→此时样式会层层叠加→会共同作用在标签上
      2. 给同一个标签设置相同的样式→此时样式会层叠覆盖→最终写在最后的样式会生效
    - 当样式冲突时，只有当选择器优先级相同时，才能通过层叠性判断结果
  - 优先级
    - 优先级公式：继承<通配符选择器<标签选择器<类选择器<id选择器<行内样式<!important
      - !important写在属性值的后面，分号的前面
      - !important无法提升继承样式的优先级，继承样式的优先级永远最低
      - !important谨慎使用
    - 权重叠加计算
      - 如果是复合计算器，则需要通过权重叠加计算方法，判断最终哪个选择器优先级最高
      - 权重叠加计算公式：（行内样式个数，id选择器个数，类选择器个数，标签选择器个数）
      - 比较规则：按顺序比较每一级，直到某一级个数有差别，则个数多的那个权重高，如果所有数字相同，则比较层叠性

### 盒子模型

- 盒子概念

  - 页面中的每一个标签，都可以看作一个盒子
  - 浏览器渲染页面时，会将网页中的元素看作一个个矩形区域，即盒子

- 盒子模型组成

  - 内容区域 content
  - 内边距区域 padding
  - 边框区域 border
  - 外边距区域 margin

- border的属性

  |   作用   |    属性名    |         属性值          |
  | :------: | :----------: | :---------------------: |
  | 边框粗细 | border-width |         数字+px         |
  | 边框样式 | border-style | solid、dashed、dotted等 |
  | 边框颜色 | border-color |          颜色           |

  复合属性如：border: 10px solid red;

- 盒子模型自动內减：box-sizing: border-box;

- 外边距

  - 正常情况

    - 水平布局的盒子，左右的margin正常，两者距离为盒子之间的margin的和

  - 合并现象

    - 垂直布局的块级元素，上下的margin会合并，两者间的距离为较大一方的margin值

  - 塌缩现象

    - 互相嵌套的块级元素，子元素的margin-top会作用在父元素上，使得父元素会一起跟随往下移动
    - 解决方法：
      1. 给父元素设置border-top或者padding-top（分隔父子元素的margin-top）
      2. 给父元素设置overflow: hidden
      3. 转换成行内块元素
      4. 设置浮动

  - 给行内元素设置margin和padding时，水平方向有效，垂直方向无效

    如果要改变行内元素垂直方向的位置，可以通过设置line-height样式

### 伪类与伪元素

- 结构伪类选择器

  - 作用：根据元素在HTML中的结构关系查找元素

  - 优势：减少对于HTML中类的依赖，有利于保持代码整洁

  - 场景：常用于查找某父级选择器中的子元素

  - 选择器

    |           选择器           |                          说明                           |
    | :------------------------: | :-----------------------------------------------------: |
    |       E:first-child        |  匹配E元素的父元素中第一个子元素，并且该子元素是E元素   |
    |        E:last-child        | 匹配E元素的父元素中最后一个子元素，并且该子元素是E元素  |
    |   E:nth-child(<u>n</u>)    |   匹配E元素的父元素中第n个子元素，并且该子元素是E元素   |
    | E:nth-last-child(<u>n</u>) | 匹配E元素的父元素中倒数第n个子元素，并且该子元素是E元素 |
    |  E:nth-of-type(<u>n</u>)   |      匹配E元素的父元素中，与E元素同类的第n个子元素      |

    - 括号内可以是正整数，也可以替换成包含n的公式，当括号内为包含n的公式时（n要在最前面，如表示选中前两个，则公式为：-n+2），n从0开始取值自然数，选择器选中所有符合条件的子元素

- 链接伪类选择器

  - 作用：常用于选中的超链接的不同状态

  - 选择器

    |  选择器   |            功能             |
    | :-------: | :-------------------------: |
    |  a:link   | 选中a链接的未被访问过的状态 |
    | a:visited | 选中a链接的已被访问过的状态 |
    |  a:hover  | 选中a链接的鼠标悬停时的状态 |
    | a:active  | 选中a链接的鼠标按下时的状态 |

    - 如果需要同时修改以上四种伪类状态的效果，需要按LVHA的顺序书写

- 焦点伪类选择器

  - 作用：用于选中元素获取焦点时的状态，常用于表单控件
  - 选择器：
    - input:focus
  - 默认效果
    - 表单控件获取焦点时，默认会显示外部轮廓线outline

- 伪元素

  - 一般页面中的非主体内容可以使用为元素

  - 区别：

    - 元素：HTML设置的标签
    - 伪元素：由CSS模拟出的标签效果

  - 种类：

    - ::before

      在父元素内容的最前添加一个伪元素

    - ::after

      在父元素内容的最后添加一个伪元素

  - 伪元素必须设置content属性才能生效

  - 伪元素默认是行内元素

### 浮动

- 浮动的作用：
  - 早期作用：文字环绕
  - 现在作用：网页布局
  
- 属性：
  - float: left; 左浮动
  - float: right; 右浮动
  
- 特点：
  - 浮动元素会脱离标准流（脱标），在标准流中不占位置
  - 浮动元素比标准流高半个级别，可以覆盖标准流中的元素（可以盖住标签元素，但不能盖住文字）
  - 相邻的元素在往方向浮动，会顶对齐紧挨着一起浮动（若要改变垂直位置需要设置margin属性）
  - 浮动元素有特殊的显示效果
    - 一行可以显示多个
    - 可以设置宽高
  
- 清除浮动

  -  如果子元素浮动了，此时子元素不能撑开标准流的块级父元素，因此需要给父元素添加高度以清除浮动，从而不影响其他网页元素的布局


  -  清除浮动的CSS设置 ，将clearfix类添加给需要清除浮动的标签
     ~~~css
      .clearfix:before
     .clearfix:after{
         content:'';
         display:table;
     }
     .clearfix.after{
         clear:both;
     }
     ~~~

     - clear原理：设置了clear属性的元素，比如clear: left，则该元素左边不能有浮动元素，因此会将自己移动至下一行。若是clear: both，则只要左边或者右边出现浮动元素都会移动自己到下一行。因此，若使用伪元素在浮动元素的父元素末尾加上一个带有clear属性的内容为空的块级元素，该伪元素会移动自己到float元素的下一行，使得父元素被撑开，达到清除浮动的效果。
     - displat:table;原理见[(6条消息) 前端三剑客之CSS---浮动的难点解析三（使用clear清除浮动和伪元素after清除浮动以及终极解决浮动的.clearfix类)_Calarqiang的博客-CSDN博客](https://blog.csdn.net/Calarqiang/article/details/108303961)

### BFC

-  Block Formatting Context 块格式化上下文

- BFC是Web页面的可视CSS渲染的一部分，是块盒子的布局过程发生的区域，也是浮动元素与其他元素交互的区域
- BFC布局规则
  - BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素，反之也如此（根据此条规则，可利用BFC避免margin塌缩）
  - 内部的盒子会在垂直方向，一个接一个地放置
  - 盒子垂直方向的距离由margin决定，属于同一个BFC的两个相邻盒子的margin会发生重叠
  - 每个盒子（块盒与行盒）的margin-box的左边，与包含块border-box的的左边相接触（对于从左往右的格式化，否则相反），即使存在浮动也是如此
  - BFC的区域不会与float-box重叠
  - 计算BFC高度时，浮动元素也参与计算（根据此条规则，可利用BFC清除浮动）
- 创建BFC的方法
  - 根元素（HTML）
  - 浮动元素（float除none以外的值）
  - 绝对定位元素：position（absolute、fixed）
  - display值为flow-root的元素
  - 弹性元素（display为flex或inline-flex元素的直接子元素）
  - 网格元素（display为grid或inline-grid元素的直接子元素）
  - 行内块元素（元素的display为inline-block）
  - 表格单元格（元素的display为table-cell，HTML表格单元格默认为该值）
  - 表格标题（元素display为table-caption，HTML表格标题默认为该值）
  - 表格元素（元素的display为table、table-row、table-row-group、table-header-group、table-footer-group（分别是table、row、tbody、thead、tfoot的默认属性）或inline-table）

### 定位

- 网页的常见布局方式
  1. 标准流
     - 块级元素独占一行→垂直布局
     - 行内元素/行内块元素一行显示多个→水平布局
  2. 浮动
     - 可以让原本垂直布局的块级元素变成水平布局
  3. 定位
     - 可以让元素自由的摆放在网页的任意位置
     - 一般用于盒子之间的层叠情况

- 使用定位

  - 设置定位

    - 属性名 position

    - 常见属性值

      | 定位方式 |  属性值  |
      | :------: | :------: |
      | 静态定位 |  static  |
      | 相对定位 | relative |
      | 绝对定位 | absolute |
      | 固定定位 |  fixed   |

  - 设置偏移值

    - 属性名：left、right、top、bottom
    - 属性值：
      - 数字+px
      - 百分比
    - 例：left:10px; 即距离左边10px的距离

- 静态定位 static

  - 静态定位是默认值，即标准流
  - position: static;
  - 注意点：
    - 静态定位就是标准流，不能通过方位属性进行移动

- 相对定位 relative （相对自己）

  - 相对于自己之前的位置进行移动
  - position: relative;
  - 特点：
    - 需要配合方位属性实现移动
    - 相对于自己原来的位置进行移动
    - 在页面中仍占有原本的位置，没有脱标
  - 注意点：
    - relative移动元素后，该元素在标准流中所占的位置没有改变
    - 水平或垂直方向同时设定两个方向的属性值时，水平方向只生效left，垂直方向只生效top

- 绝对定位 absolute （相对非静态定位的父元素）

  - 相对于最近的非静态定位的父元素进行定位移动
  - position: absolute;
  - 特点
    - 需要配合方位属性实现移动
    - 当没有非静态定位的父元素时，则相对于body进行移动
    - 在页面中不占位置，脱标
    - 会改变元素的显示模式特点，使其具备行内块的特点

- 固定定位 fixed

  - 相对于浏览器进行可视区域进行定位移动
  - position: fixed;
  - 特点
    - 需要配合方位属性实现移动
    - 相对于浏览器可视区域进行移动
    - 在页面中不占位置，脱标
    - 会改变元素的显示模式特点，使其具备行内块的特点

- 元素层级关系

  - 不同布局方式元素的层级关系
    - 标准流<浮动<定位
  - 不同定位之间的层级关系
    - 相对、绝对、固定默认层级相同
    - 在HTML中写在下面的元素层级更高，会覆盖上面的元素
  - 更改定位元素的层级（需要元素有定位）
    - 属性名：z-index
    - 属性值：数字（数字越大，层级越高）
  
- 包含块

  - 简单理解：包含快是包含了当前元素的块元素
  - 根据不同情况，元素的包含快也不同
    - 对于未设置绝对定位的元素，它的包含快就是离他最近的祖先块元素
      - body下一级的子元素的包含块是body
      - body的包含块是html
      - html的包含块称为初始包含块
    - 对于设置了绝对定位的元素，它的包含块是离它最近的开启了定位的祖先元素
      - 如果所有祖先元素都没有开启定位，则它的包含块就是初始包含块（不是html或body）

### 显示效果

- 行内元素垂直对齐方式属性 vertical-align
  - 基线类对齐
    - 属性值：baseline（默认）、top、middle、bottom、数字+%、数字+px
    - 文字/行内元素的定位线有：top line、middle line、base line、bottom line，从top line到bottom line的距离就是line-height的属性值
    - 基线对齐方式即为：
      - top：使元素顶端与行中最高元素的顶端对齐
      - middle：把元素放置在文字line-height（父元素的文本内容的高度）的中部
      - bottom：使元素及其后代元素的底部（如果后代元素底部超出元素本身底部）与整行的底部对齐
      - 百分比：把元素的baseline移动到行盒baseline以上的距离（距离为行盒line-height的百分比）
      - 数字+px：把元素的baseline移动到行盒baseline以上的距离
    - 行内块元素若标签里面不包含行内元素，则其base line的位置和bottom line一样；若其标签里面存在行内元素，则其base line的位置按照标签内的行内元素（经测试的初步结论）
    - 注意行高line-height的影响
  - 文本类对齐
    - 属性值：text-top、text-bottom
    - 文本类对齐方式即为：使得元素顶部/底部和父级内容区域的顶部/底部对齐
  
- 光标类型
  - 属性名：cursor
  - 常见属性值：
    - default 默认值
    - pointer 小手效果，提示可以点击
    - text 工字型，提示可以选择文字
    - move 十字光标，提示可以移动
  
- 边框圆角
  - 属性名：boeder-radius
  - 常见取值：
    - 数字+px
    - 百分比
  
- 溢出部分显示效果
  - 属性名 overflow
  - 常见属性值：
    - visible 默认值，溢出部分可见
    - hidden 溢出部分隐藏
    - scroll 无论是否溢出，都显示滚动条
    - auto 根据是否溢出，自动显示或隐藏滚动条
  
- 元素可见性
  - 隐藏元素
    - visibility: hidden;（不脱标）
    - display: none;（脱标）
  - 元素整体透明度
    - 属性名：opacity
    - 属性值为0~1之间的数字，1表示完全不透明，0表示完全透明
  
- 边框合并
  - border-collapse: collapse;
  - 加给table标签
  
- CSS画三角形（面试题）
  - 实现原理：利用盒子边框
  - 实现步骤
    1. 设置一个盒子
    2. 设置边框厚度
    3. 将盒子宽高设置为0，仅保留边框
    4. 得到四个三角形（边框），将不需要的边框设置成透明色
  
- 盒子阴影

  - 属性名：box-shadow

  - 属性值

    | 参数类型 |           作用           |  取值   |
    | :------: | :----------------------: | :-----: |
    | h-shadow |     必须，水平偏移量     | 数字+px |
    | v-shadow |     必须，垂直偏移量     | 数字+px |
    |   blur   |       可选，模糊度       | 数字+px |
    |  spread  |      可选，阴影扩大      | 数字+px |
    |  color   |      可选，阴影颜色      |  颜色   |
    |  inset   | 可选，阴影设置为内部阴影 |  inset  |

- 过渡

  - 属性名：transition

  - 常见取值

    |  参数类型  |                             取值                             |
    | :--------: | :----------------------------------------------------------: |
    | 过渡的属性 | all：所有能过渡的属性都过渡；具体属性名，如width：只有width有过渡 |
    | 过渡的时长 |                            数字+s                            |
    |  运动曲线  | linear 匀速<br>ease 逐渐减慢<br>ease-in 加速<br>ease-out 减速<br>ease-in-out 先加速后减速 |

  - transition: 过渡的属性 过渡的时长 运动曲线 延迟时间;

- CSS的letter-spacing与PS的字间距换算

  letter-spacing = font-size/1000*PS字间距

### 网页制作

- 根目录：网站的第一级文件夹

  - 图片文件夹：images
  - 样式文件夹：CSS
  - 首页：index.html
  - 首页样式：index.css

- SEO（Search Engine Optimization 搜索引擎优化）

  - 作用：让网站在搜索引擎上的排名靠前

  - 提升SEO的常见方法：

    - 竞价排名
    - 将网页制作成html后缀
    - 语义化标签（在合适的地方使用合适的标签）

  - SEO三大标签

    1. title：网页标题标签
    2. description：网页描述标签
    3. keywords：网页关键词标签

    - 例

    ~~~html
        <title>京东(JD.COM)-正品低价、品质保障、配送及时、轻松购物！</title>
        <meta name="description" content="京东JD.COM-专业的综合网上购物商城，为您提供正品低价的购物选择、优质便捷的服务体验。商品来自全球数十万品牌商家，囊括家电、手机、电脑、服装、居家、母婴、美妆、个护、食品、生鲜等丰富品类，满足各种购物需求。"/>
        <meta name="Keywords" content="网上购物,网上商城,家电,手机,电脑,服装,居家,母婴,美妆,个护,食品,生鲜,京东"/>
    ~~~

- ico图标设置

  ~~~html
  <link rel="shortcut icon" href="ico图标路径" type="images/x-icon">
  ~~~

- 精灵图

  - 场景：项目中多张小图片，合成成一张大图片，这张大图片称之为精灵图
  - 优点：减少服务器发送次数，减轻服务器的压力，提高页面加载速度
  - 精灵图使用步骤
    1. 创建一个盒子
    2. 通过PxCook量取小图片大小，将小图片的宽高设置给盒子
    3. 将精灵图设置为盒子的背景图片
    4. 通过PxCook测量小图片左上角坐标，分别取负值设置给盒子的background-position: x y;

# CSS3新特性

### 新增选择器

- 属性选择器

  - 场景：通过元素上的HTML属性来选择元素，常用用于选择input标签

  - 选择器语法

    |    选择器     |                   功能                    |
    | :-----------: | :---------------------------------------: |
    |    E[att]     |          选择具有att属性的E元素           |
    | E[att="val"]  |  选择具有att属性并且属性值等于val的E元素  |
    | E[att^="val"] |    匹配具有att属性且值以val开头的E元素    |
    | E[att$="val"] |    匹配具有att属性且值以val结尾的E元素    |
    | E[att*="val"] |     匹配具有att属性且值含有val的E元素     |
    | E[att~="val"] | 匹配具有att属性且值含有独立单词val的E元素 |

- 伪类选择器

  - 结构伪类选择器

    - 作用：根据元素在HTML中的结构关系查找元素

    - 优势：减少对于HTML中类的依赖，有利于保持代码整洁

    - 场景：常用于查找某父级选择器中的子元素

    - 选择器

      |           选择器           |                           说明                            |
      | :------------------------: | :-------------------------------------------------------: |
      |       E:first-child        |  匹配E元素的父元素中，第一个子元素，并且该子元素是E元素   |
      |        E:last-child        | 匹配E元素的父元素中，最后一个子元素，并且该子元素是E元素  |
      |   E:nth-child(<u>n</u>)    |   匹配E元素的父元素中，第n个子元素，并且该子元素是E元素   |
      | E:nth-last-child(<u>n</u>) | 匹配E元素的父元素中，倒数第n个子元素，并且该子元素是E元素 |
      |  E:nth-of-type(<u>n</u>)   |       匹配E元素的父元素中，与E元素同类的第n个子元素       |
      |        E:only-child        |             匹配E元素的父元素中，唯一的子元素             |
      |       E:only-of-type       |         匹配E元素的父元素中，唯一是E元素的子元素          |
      |          E:empty           |          匹配内容（文本、标签、空格）为空的E元素          |
      
      -  括号内可以是正整数，也可以替换成包含n的公式，当括号内为包含n的公式时，n从0开始取值自然数，选择器选中所有符合条件的子元素

  - 链接伪类选择器

    - 作用：常用于选中的超链接的不同状态

    - 选择器

      |  选择器   |            功能             |
      | :-------: | :-------------------------: |
      |  a:link   | 选中a链接的未被访问过的状态 |
      | a:visited | 选中a链接的已被访问过的状态 |
      |  a:hover  | 选中a链接的鼠标悬停时的状态 |
      | a:active  | 选中a链接的鼠标按下时的状态 |

      - 如果需要同时修改以上四种伪类状态的效果，需要按LVHA的顺序书写

  - focus伪类选择器

    - 作用：用于选中元素获取焦点时的状态，常用于表单控件
    - 选择器：
      - input:focus
    - 默认效果
      - 表单控件获取焦点时，默认会显示外部轮廓线outline
    
  - target伪类选择器

    - 作用：选中锚点连接的目标
    - 选择器
      - :target

### 函数属性值

- filter: blur(数字+px)

  数值越大越模糊

-  width: calc(50px+80px)

   括号内内容可用+-*/进行计算

### 去除表单默认样式

~~~css
input,
textarea {
  outline:0;
  border:0;
  -webkit-appearance: none;
  -moz-appearance:none;  
  appearance: none;
  line-height:normal;
}
// input::-webkit-input-placeholder{
//   line-height: normal;  //解决不垂直居中的问题
// }
input::-webkit-input-placeholder{
  line-height: normal;
}
input::-moz-placeholder{
  line-height: normal;
}
input::-ms-input-placeholder{
  line-height: normal;
}
select{
  /*去除外边框*/
  border: none; 
  /*适应safari浏览器内容不跑偏*/
  line-height: 1;
  width: 100%;
  /*将默认的select选择框样式清除*/  
  appearance:none;  
  -moz-appearance:none;  
  -webkit-appearance:none;  
  /*在选择框的最右侧中间显示小箭头图片*/  
  background: url("~@/assets/images/btn_pulldown.png") right center/25px 10px no-repeat ;
  /*为下拉小箭头留出一点位置，避免被文字覆盖*/  
  padding-right: 30px;
  /*去除选中时样式*/
  outline: none;
  /*去除点击一闪而过的灰色背景*/
  -webkit-tap-highlight-color:rgba(0,0,0,0);
~~~

