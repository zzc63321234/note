# HTML

- 文本格式化（语义化的文本格式化标签）

  |  标签  |               说明               |
  | :----: | :------------------------------: |
  | strong |               加粗               |
  |  ins   | 下划线（inserted text 嵌入文本） |
  |   em   |      倾斜（emphasis 强调）       |
  |  del   |              删除线              |

- hr标签为分割线

- a标签的常见属性

  - href 跳转的路径，若值为#则为空链接，会返回网页顶部

  - target

    默认值为_self，在当前窗口中跳转

    值为_blank时，在新窗口中跳转

- 列表标签

  - ul标签、ol标签中只允许嵌套li标签

  - li标签中可以嵌套任意内容

  - 自定义列表

    - 使用场景：通常用于实现网页的底部导航

    - 标签组成

      | 标签名 |                  说明                   |
      | :----: | :-------------------------------------: |
      |   dl   | 表示自定义列表的整体，用于包裹dt/dd标签 |
      |   dt   |          表示自定义列表的主题           |
      |   dd   |     表示自定义列表主题的每一项内容      |

    - dd会默认显示缩进效果
    
    - dl标签只允许包含dt/dd标签
    
    - dt/dd标签可以包含任意内容
  
- 表格标签

  - 嵌套关系：table>tr>td

  - 其他标签

    - caption

      表格大标题，写在table标签内部

    - th

      表头单元格，写在tr标签内部

  - 常见属性

    | 属性名 | 属性值 |   效果   |
    | :----: | :----: | :------: |
    | border |  数字  | 边框宽度 |
    | width  |  数字  | 表格宽度 |
    | height |  数字  | 表格高度 |

  - 表格结构标签

    让表格内容分组，使语义更加清晰

    - thead 表格头部
    - tbody 表格主体
    - tfoot 表格底部

  - 合并单元格

    - 使用属性实现合并单元格
    - 属性名
      - rowspan 跨行合并
      - colspan 跨列合并
    - 属性值为合并的个数
    - 不能跨结构标签合并

- 表单标签

  - input标签

    - input标签可以通过type属性值的不同，展示不同的效果

      | type属性值 |                 说明                 |
      | :--------: | :----------------------------------: |
      |    text    |       文本框，用于输入单行文本       |
      |  password  |         密码框，用于输入密码         |
      |   radio    |          单元框，用于多选一          |
      |  checkbox  |          多选框，用于多选多          |
      |    file    |      文件选择，用于之后上传文件      |
      |   submit   |          提交按钮，用于提交          |
      |   reset    |          重置按钮，用于重置          |
      |   button   | 普通按钮，默认无功能，配合JS添加功能 |

    - placeholder属性：占位符，提示用户输入内容的文本

    - name和value属性

      - name属性：当前控件的含义，提交之后可以告诉后端发送过去的数据是什么含义
      - value属性：用户输入的内容，提交之后会发送给后端服务器
      - 后端接收到的数据格式是：name属性值=value属性值

    - 单选框 radio

      - name属性对于单选框有分组功能，有相同name属性值的单选框为一组，一组中同时只能由一个被选中
      - checked表示默认选中

    - 文件选择 file

      - multiple属性：多文件选择

    - 按钮 submit reset botton

      - 要实现按钮功能，需要配合form标签使用，用form标签把表单标签一起包裹起来即可实现按钮功能

      - value属性可更改按钮内的文字

      - button也可以作为标签出现，是双标签，更便于包裹其他内容

        button通过type属性值更改标签的功能，属性值同input

        谷歌浏览器中botton默认是提交按钮

  - select选择菜单标签

    - 标签组成
      - select标签：下拉菜单的整体
        - multiple属性：多选菜单
      - option标签：下拉菜单的每一项
        - selected属性：下拉菜单的默认选中
      
    - 示例

      ~~~html
      <select>
        <option value="" selected disabled hidden>请选择...</option>
        <option value="option1">Option 1</option>
        <option value="option2">Option 2</option>
        <option value="option3">Option 3</option>
      </select>
      ~~~

  - textarea文本域标签

    - 在网页中提供可输入多行文本的表单控件
    - 常见属性
      - cols：规定了文本域的可见高度
      - rows：规定了文本域的可见行数

  - label标签

    - 常用于绑定内容与表单标签的关系
    - 使用方法1：
      1. 把label标签把内容包裹起来
      2. 在表单标签上添加id属性
      3. 在label标签中添加for属性，属性值对应表单的id值
    - 使用方法2：直接用label标签把内容和表单标签一起包裹起来（推荐）

- 字符实体
  - 在网页中展示特殊符号效果时，需要使用字符实体替代
  
  - 结构：&英文字母;
  
  - 常见字符实体
    - & n b s p ; 
    
      空格
    
    - & g t ; 
    
      大于号 >
    
    - & l t ;
    
      小于号 < 

# HTML5新特性

### 语义化标签布局

- 在HTML5新版本中，推出了一些有语义的布局标签供开发者使用(常用于手机端)

- 标签：

  | 标签名  |    语义    |
  | :-----: | :--------: |
  | header  |  网页头部  |
  |   nav   |  网页导航  |
  | footer  |  网页底部  |
  |  aside  | 网页侧边栏 |
  | section |  网页区块  |
  | article |  网页文章  |

  - 以上标签显示特点和div一致，但是比div多了不同的语义

### 媒体标签

- audio标签和video标签的常见属性

  - src 路径
  - controls 控件
  - autoplay 自动播放（一些网页不支持，在谷歌浏览器中，autoplay属性配合muted属性可以实现自动静音播放）
  - loop 循环播放

### 表单

- 新增的表单元素
  - type值：
    - date 日期类型
    - month 月类型
    - week 周类型
    - time 时间类型
    - number 数字
    - search 搜索框
    - range 滑块
    - color 颜色
    - datetime-local 年月日时分秒
  
- 新增的表单属性

  |     属性     |    值    | 说明                                                         |
  | :----------: | :------: | :----------------------------------------------------------- |
  |   required   |    /     | 内容不能为空                                                 |
  | placeholder  | 提示文本 | 占位符，如果表单存在默认值                                   |
  |  autofocus   |    /     | 页面加载完成后，自动聚焦到该表单                             |
  | autocomplete |  off/on  | 键入时，浏览器根据之前键入过的值，显示出在字段中填写的选项<br>默认已打开，需要表单有name属性（建议关闭） |
  |   multiple   |    /     | 可以多选文件提交                                             |
  
- 表单实例

  ~~~html
  <input type="number" name="num" min="10" max="30" step="5">
  <input type="range" name="r" min="10" max="30" step="5">
  ~~~

  
