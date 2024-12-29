# 移动web

## 主流移动端分辨率

- 参考机型 iphone6/7/8
- 物理分辨率 750*1334
- 逻辑分辨率 375*667

## 视口

```html
<meta mane="viewport" content="width=device-width, initial-scale=1.0">
```

- viewport 视口
- width=device-width 视口宽度=设备宽度
- initial-scale=1.0 缩放1倍（不缩放）

## Flex布局

- [flex 布局的基本概念 - CSS：层叠样式表 | MDN (mozilla.org)](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Flexible_Box_Layout/Basic_Concepts_of_Flexbox)

- 设置方式

  - 父元素添加 `display: flex`;  
  - 此时父元素成为弹性容器
  - 其子元素成为弹性盒子

- 组成部分

  - 弹性容器(`flex container`)
  - 弹性盒子(`flex item`)
  - 主轴
  - 纵轴（交叉轴、侧轴）

- 应用在弹性盒子上的属性

  - `flex-flow`
  - `flex-direction`
  - `flex-wrap`
  - `flex-flow`
  - `justify-content`
  - `align-items`
  - `align-content`

- 应用在弹性盒子上的属性

  - `flex-grow`
  - `flex-shrink`
  - `flex-basis`
  - `flex`
  - `order`
  - `align-self`

- 主轴方向对其方式

  - 属性：`justify-content`

  |    属性值     |                        作用                        |
  | :-----------: | :------------------------------------------------: |
  |  flex-start   |              默认值，起点开始依次排列              |
  |   flex-end    |                  终点开始依次排列                  |
  |    center     |                   沿主轴居中排列                   |
  | space-around  | 弹性盒子沿主轴均匀排列，空白间距均分在弹性盒子两侧 |
  | space-between | 弹性盒子沿主轴均匀排列，空白间距均分在相邻盒子之间 |
  | space-evenly  | 弹性盒子沿主轴均匀排列，弹性盒子与容器之间间距相等 |

- 纵轴方向对齐方式

  - `align-items`

    添加给弹性容器

  - `align-self`

    添加给弹性盒子

  |   属性值   |                  作用                  |
  | :--------: | :------------------------------------: |
  | flex-start |            元素位于容器开头            |
  |  flex-end  |            元素位于容器结尾            |
  |   center   |             沿纵轴居中排列             |
  |  stretch   | 默认值，弹性盒子沿纵轴被拉伸至铺满容器 |

- 伸缩比

  - 语法

    - `flex: flex-grow flex-shrink flex-basis`
      - 以下说到的大小，皆指元素沿主轴方向上的宽度
      - 该属性是一个复合属性，包含
        - `flex-grow`
          - 该属性定义了 `flex` 元素的占据额外空间的能力
          - 如果所有元素的 `flex-grow` 属性都为 `1`，那么当有额外的空间时，所有元素将等分这个额外的空间
          - 如果某个元素的 `flex-grow` 属性为 `2`，其余元素的 `flex-grow` 属性都为 `1`，那么当有额外的空间时，该元素将获得 `2` 倍于每个其它元素的额外空间
          - 默认值为 `0`，即默认不会占据额外空间
          - 如果一个 `flex` 元素设置了 `max-width` 或 `max-height`，会限制它占据额外空间后的大小
        - `flex-shrink`
          - 该属性定义了 `flex` 元素压缩自身空间的能力
          - 该属性会与 `flex-basis` 一起决定 `flex` 元素的缩小权重，它影响 `flex` 元素缩小能力的方式为：
            - 每个元素的缩小权重为 `flex-basis * flex-shrink`
            - 每个元素的缩小权重相加得到总的缩小权重
            - 每个元素需要缩小的大小为
              - `该元素的缩小权重 / 总的缩小权重 * 总共超出的大小`
            - 特殊情况
              - 如果每个元素的 `flex-basis` 都为 `0`，那么 `flex-shrink` 会被忽略，由 `flex-shrink` 单独决定权重
          - 默认值为 `1`，即默认会缩小
          - 如果一个 `flex` 元素设置了 `min-width` 或 `min-height`，会限制它缩小后的大小
        - `flex-basis`
          - 该属性定义了 `flex` 元素的基础大小
          - 可以为其设置为具体的值以取代元素本身的大小
          - 单独的 `flex-basis` 属性的默认值为 `auto`，此时元素的基础大小取决于它本身的大小
          - 而在 `flex` 复合写法中，省略了的 `flex-basis` 属性会被默认设置为 `0`

- 主轴方向

  - 属性：`flex-direction`

  - 属性值

    |     属性值     |        说明        |
    | :------------: | :----------------: |
    |      row       | 行，水平（默认值） |
    |     column     |      列，垂直      |
    |  row-reverse   |    行，从右向左    |
    | column-reverse |    列，从下向上    |

- 弹性盒子多行显示

  - 显示模式

    - 属性

      `flex-wrap`
  
    - 属性值
  
      |    属性值    |                 说明                 |
      | :----------: | :----------------------------------: |
      |    nowrap    |          单行显示（默认值）          |
      |     wrap     |               多行显示               |
      | wrap-reverse | 多行显示，cross-start与cross-end互换 |
  
  - `flex-flow` 属性
  
    - `flex-direction` 与 `flex-wrap` 的复合属性
  
  - 多行对齐方式
  
    - 属性
  
      `align-content`

## rem

- rem单位

  - 相对单位
  - rem单位是相对于HTML标签的字号计算结果
  - 1rem = 1HTML字号大小

- rem移动适配-媒体查询

  - 写法

    ```css
    @media (媒体特性) {
        选择器{
            CSS属性
        }
    }
    ```

  - 目前rem布局方案中，将网页等分成10份，HTML标签的字号设置为视口宽度的1/10

    ```css
    @media (width: 320px) {
        HTML{
            font-size: 32px;
        }
    }
    @media (width: 375px) {
        HTML{
            font-size: 37.5px;
        }
    }
    @media (width: 414px) {
        HTML{
            font-size: 41.4  px;
        }
    }
    ```

  - rem适配原理

    1. 根据视口宽度，设置不同的HTML标签字号
    2. 书写代码，尺寸是rem单位
       - 确定设计稿对应的设备的HTML标签字号
         - 查看设计稿宽度→确定参考设备宽度（视口宽度）→确定基准根字号（1/10视口宽度）
         - rem单位的尺寸 = px单位数值/基准根字号

  - 使用flexible.js配合rem实现在不同宽度的设备中，网页元素尺寸等比缩放效果

## Less语法

- Less语法中的注释，只有多行注释会被写入css，单行注释只在Less文件中显示

- 运算

  - 加、减、乘直接书写计算表达式

  - 除法需要添加小括号或在/前添加.，例

    ```less
    width: (100 / 37.5rem);
    height: 100 ./ 37.5rem;
    ```

  - 表达式存在多个单位以第一个单位为准

- 嵌套

  - 使用Less嵌套可快速生成后代选择器

    ```less
    .父级选择器 {
        // 父级样式
        .子级选择器 {
            // 子级样式
        }
    }
    ```

  - &表示当前选择器，通常配合伪类或伪元素使用

    ```less
    .父级选择器 {
        // 父级样式
        &:hover{
            //伪元素样式
        }
    }
    ```

- Less变量

  - 语法
    - 定义变量：@变量名: 值；
    
    - 使用变量：
    
      - 作为属性值来使用
    
        CSS属性: @变量名;
    
      - 作为属性名来使用
    
        @{变量名}: 属性值;
    
      - 作为选择器名来使用
    
        @{选择器变量名}
    
        选择器@{变量名}

  ```less
  // 定义
  @fontColor: pink;
  @c:color
  @b:.box
  
  // 使用
  @{b} {
      @{c}: @fontColor;
  }
  
  a {
      @{c}: @fontColor;
  }
  ```

- Less文件中导入其他文件

  ```less
  // 如果导入的是less文件，可以省略后缀
  @import 'base.less';
  ```

- 使用Less语法导出CSS文件

  - 方法一：配置easy less的json文件

  - 方法二：在Less文件第一行添加代码,可自定义文件名，结尾没有分号

    ```less
    // out: ./css/
    ```

    ```less
    // out: ./css/common.css
    ```

  - 可禁止导出css文件

    ```less
    // out: false
    ```

## vw/vh

- 相对单位

  vw（viewport width）

  vh（viewport height）

  1vw = 1/100视口宽度

  1vh = 1/100视口高度

## 文字省略效果

- 单行省略

  ```css
  overflow:hidden;
  text-overflow:ellipsis;
  white-space:nowrap;
  ```

- 多行省略

  ```css
  overflow:hidden;
  text-overflow:ellipsis;
  display:-webkit-box;/*将对象作为弹性伸缩盒子模型显示*/
  -webkit-line-clamp: 3/*限制块元素显示的文本的行数*/
  -webkit-box-orient:vertical;/*设置或检测伸缩盒子对象的子元素的排列方式*/
  ```

## 媒体查询

- 完整写法

  ```css
  @media 逻辑符 媒体类型 and（媒体特性）{
      CSS代码
  }
  ```

- 常用写法

  ```css
  @media （媒体特性）{
      CSS代码
  }
  ```

- 外链式CSS引入

  ```html
  <link rel="stylesheet" media="关键词 媒体类型">
  ```

  

