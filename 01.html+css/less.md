- less

  - less是一种动态样式语言，属于css预处理语言的范畴，它扩展了CSS语言

  - 增加了变量、Mixin（混合）、函数等特性，使CSS更易维护和扩展

  - less既可以在客户端上运行，也可以借助Node.js在服务端运行

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

          @{变量名}

    
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
  
- 变量的延迟加载
  
  ~~~less
    @var: 0;
    .wrap{
        @var: 1;
        .box{
            @var: 2;
            three: @var; // 3
            @var: 3;
        }
        one: @var; // 1
    }
    ~~~
  
  - 查找变量时，如果当前大括号内存在该变量，则直接使用最后一个，不分远近
  
    如果自身不存在，则向上一个大括号查找，与第一种情况同理
  
- 混合(Mixin)
  
  - 定义语法
  
    - .混合名称(形参){CSS代码}
  
  - 调用混合
  
    - .混合名称(实参);
  
    - 相当于在调用的位置写了混合里的代码
  
    - 结尾必须加分号
  
  - 混合允许传递参数，语法和声明变量相同
  
    - 在定义参数时可以直接通过冒号设置参数默认值，相当于给变量赋值
      - 混合参数在没有设置默认值的情况下，调用混合参数的个数必须对等，定义几个就要传递几个
  
  ~~~less
    // 定义混合
    .allCenter(@w, @h, @bg: yellow){
        position: absolute;
        top: 0;
        bottom: 0;
        left: 0;
        right: 0;
        margin: auto;
        width: @w;
        height: @h;
        background: @bg;
    }
    // 调用混合
    .wrap{
        .allCenter(@w: 400px,@h: 400px;@bg: pink);
    }
    ~~~
  
  ~~~~less
    // 使用混合的方式设置清除浮动，无需给标签添加新类名
    ~~~html
    <div class="box1">
        <div class="box2"></div>
    </div>
    ~~~
    
    ~~~less
    .clearFix(){
        *zoom: 1;
        &::before,
        &::after{
            content: '';
            display: table;
        }
        &::after{
            clear:both;
        }
    }
    
    .box1{
        border: 1px solid #000;
        .clearfix(){};
        box2{
            width: 200px;
            height: 200px;
            background: pink;
            float: left;
        }
    }
    ~~~
    ~~~~
  
  