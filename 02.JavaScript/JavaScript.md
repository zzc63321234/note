## JS书写位置

1. 行内式

   ```html
   <input type="button" value="点我试试" onclick="alert('Hello World')" />
   ```

2. 内嵌式

   ```html
   <script>
       alert('Hello World')
   </script>
   ```

3. 外部文件

   ```html
   <script src="my.js"></script>
   ```

- 当需要将script写在body之前时

  - 可以将代码写在window.onload的回调函数中，使得页面加载完毕后再执行代码

  - 可以在映入外部js文件时，在script标签上添加defer属性

    ```html
    <script defer src='...'></script>
    ```

## window方法

- alert()

  浏览器弹出警示框

- prompt()

  浏览器弹出输入框，用户可以输入

- confirm()

  浏览器弹出确认框，根据用户的选择返回布尔值

- 定时器

  - 间歇函数

    - let intervalID = setInterval(回调函数, 延迟的毫秒数)

    - 关闭方法

      clearInterval(intervalID)

  - 延时函数

    - let timeoutID = setTimeout(回调函数, 延迟的毫秒数)
    - 关闭方法
    - clearTimeout(timeoutID)

## 字面量、变量、常量

- 字面量
  - 字面量就是一个一个的值，如1、2、'hello'、true、NaN ...
  - 所有的字面量在JS中都可以直接使用，但是一般不会这么做
- 变量
  - 变量用来存储值，一个变量可以用来存储任意值
  - 变量中存储的值可以任意修改
  - JS中的变量并没有直接存储值，而是存储内存地址
  - JS中的变量更像是一个值的别名
- 变量的使用
  - 声明变量
    - let 变量名
    - var 变量名
  - 为变量赋值
    - 变量名 = 值
  - 声明和赋值同时进行
    - let 变量名 = 值
    - var 变量名 = 值
  - 声明常量
    - const 常量名 = 值
      - 常量只能进行一次赋值
- 标识符
  - 在JS中所有可以自主命名的内容都可以认为是标识符
    - 如：变量名、函数名、类名、属性名
  - 规范
    1. 标识符中可以含有字母、数字、_、$，但是不能以数字开头
    2. 标识符不能是JS中的关键字和保留字
    3. 采用驼峰命名法
       - 小驼峰
         - 首字母小写，每个单词开头大写，其余小写
       - 大驼峰（用于类名）
         - 首字母大写，每个单词开头大写，其余小写

## 数据类型

- JS把数据类型分为 

  - 简单数据类型（Number,String,Boolean,Undefined,Null）
  - 复杂数据类型（Object,Array等）

- 简单数据类型（基本数据类型）

  - string（字符串）

    - JS中的字符串使用引号包含起来，单双引号都行

    - 同类型的引号不要发生嵌套

    - 转义字符串

      | 写法 | 转义为 |
      | :--: | :----: |
      | \\'  |   ''   |
      | \\"  |   \    |
      |  \t  | 制表符 |
      |  \n  |  换行  |
      | \\\  |   \\   |

  - number（数值）

    - 在JS中的所有整数和浮点数（小数）都是number类型
    - 特殊的数字
      - Infinity（无穷）
      - NaN（非法数字Not a Number）
    - 其它进制的数字
      - 二进制：0b开头
      - 八进制：0o开头
      - 十六进制：0x开头
    - JS中可以保证大部分的整数运算取得一个精确的结果，小数运算可能会得到一个近似值，因此不要直接在js中进行精度要求高的运算
    - 大整数（bigint）
      - ES2020中新推出的数值类型，大整数要以n结尾
      - 使用typeof检查大整数时，会返回bigint

  - boolean（布尔值）

    - 布尔值用来进行逻辑判断，布尔值只有true和false

  - null（空值）

    - 空值用来表示一个空的对象，使用typeof检查空值时会返回object

  - undefined（未定义）

    - 未定义用来表示声明但没有赋值的变量，一般不会主动使用undefined

- 类型转换

  - 隐式转换

    某些运算符被执行时，系统内部自动将数据类型进行隐式转换

    规则：

    - +号两边只要有一个是字符串，都会把另一个转换成字符串

    - 除了+号以外的算数运算符，都会把数据转换成数字类型

    - 当+号作为正号解析时可以把数据转换成数字类型

    - 为任意值加上一个空串，即可将其转换为字符串

      ```javascript
      let a = 10
      a = a + ''
      ```

    - 为任意值取两次反，可将其转换为布尔值

  - 显式转换

    - Number()

      - 将字符串类型转换为数字型
      - 不同的情况
        - 字符串
          - 如果一个字符串时合法的数字，则直接转换为对应的数字
          - 如果不是合法数字，则转换为NaN
          - 如果是空串或空格串，则转换为0
        - 布尔值
          - true转换为1
          - false转换为0
        - null转换为0
        - undefined转换为NaN

    - `parseInt(string, radix)`

      - 解析一个字符串，返回一个整数
      - 必须是数字开头，否则返回 `NaN`
      - 参数
        - `string`
          - 要被解析的值，如果参数不是一个字符串，则会将其转换为字符串，字符串开头的空白符将会被忽略
        - `radix`
          - 表示进制的基数
          - 范围 `2-36`
          - 如果 `radix` 没有指定或者为 `0`，参数会被假定以 `10` 为基数来解析，如果数值以字符对 `0x` 或 `0X` 开头，会假定以 `16` 为基数来解析

    - parseFloat()

      - 解析一个字符串，返回一个浮点数
      - 必须是数字开头，否则返回NaN
    
    - String()
    
      - 转换为字符串
    
    - 变量.toString(进制)
    
      - 转换为表示为对应进制的字符串
    
    - boolean()
    
      - 值为false的情况
    
        0、NaN、null、undefined、' '
      
    - JSON字符串转换
    
      - JSON.stringify()
    
        将复杂数据类型转换成JSON字符串
      
      - JSON.parse()
      
        将JSON字符串转换成复杂数据类型

## 运算符

- 数学运算符也叫算数运算符

  - +：求和
  - -：求差
  - *：求积
  - /：求商
  - %：取模（取余）

- 赋值运算符

  赋值运算符要求左边必须是一个容器，将右边的值计算后赋予左边

  - =
  - +=
  - -=
  - *=
  - /=
  - %=

- 自增与自减运算（一元运算符）

  - 前置自增：如++i

    先自增再参与运算

  - 后置自增：如i++

    先参与运算再自增

- 比较运算符

  - 比较运算符的使用
    - &gt;：左边是否大于右边
    - &lt;：左边是否小于右边
    - &gt;=：左边是否大于等于右边
    - &lt;=：左边是否小于等于右边
    - ==：左右两边的值是否相等
    - !=：左右两边的值是否不相等
    - ===：左右两边的值和类型是否都相等
    - !==：左右两边是否不相等
  - 比较的结果为boolean类型，只会得到true或false
  - 字符串的比较，是比较字符对应的ASCII码
    - 从左往右依次比较
    - 第一位一样再比较第二位，以此类推
  - NaN不等于任何值，包括它本身
  - 尽量不要比较小数，存在精度问题
  - 不同类型之间的比较会发生隐式转换

- 逻辑运算符

  - 逻辑短路

    逻辑短路只存在于&&和||中，当满足一定条件会让右边代码不执行

    - 当&&左边为false时发生逻辑短路，返回false
    - 当||左边为true时发生逻辑短路，返回true

- 运算符优先级

  | 优先级 | 运算符类型 |        运算符         |
  | :----: | :--------: | :-------------------: |
  |   1    |   小括号   |          ()           |
  |   2    | 一元运算符 |        ++ -- !        |
  |   3    | 算数运算符 |     先* / %后+ -      |
  |   4    | 关系运算符 | &gt; &gt;= &lt; &lt;= |
  |   5    | 相等运算符 |     == != !== ===     |
  |   6    | 逻辑运算符 |      先&&后\|\|       |
  |   7    | 赋值运算符 |           =           |
  |   8    | 逗号运算符 |           ,           |

## 语句

- 语句和表达式

  - 表达式

    表达式是一组代码的集合，JavaS解释器会将其计算出一个结果

  - 语句

    JS整句或命令，JS语句时以分号结束（可以省略）

    如：if语句 for循环语句

  - 区别

    表达式计算出一个值，语句使得某件事情发生

    但某些情况也可以将表达式理解为语句，因为计算结果也是在做事

- 程序三大流程控制语句

  - 顺序结构
  - 分支结构
  - 循环结构

- 分支语句

  1. if分支语句

     - 单分支

       ```javascript
       if (条件) {
           满足条件时执行的代码
       }
       ```

       - 当小括号内条件为true时，进入大括号执行代码
       - 小括号内的结果不是布尔类型时，将隐式转换为布尔类型

     - 双分支

       ```javascript
       if (条件) {
           满足条件时执行的代码
       } else {
           不满足条件时执行的代码
       }
       ```

     - 多分支

       ```javascript
       if (条件1) {
           代码1
       } else if (条件2) {
           代码2
       } else if (条件3) {
           代码3
       } else {
           条件全部不满足时执行的代码
       }
       ```

  2. 三元运算符（三元表达式）

     ```javascript
     条件 ? 满足条件时执行的代码 : 不满足条件时执行的代码
     ```

     - 一般用来取值
     - 也属于双分支语句

  3. switch语句

     ```javascript
     switch (数据) {
         case 值1:
             代码1
             break
         case 值2:
             代码2
             break
         default:
             代码n
             break
     }
     ```

     - 找到和小括号里的数据全等的case值，执行里面对应的代码
     - 若没有全等的值则执行default里的代码
     - 一般用于等值判断，不适合于区间判断
     - 需要配合break关键字使用，否则会造成case穿透（继续往下执行遇到break之前的所有代码）
     - 可以利用case穿透来实现在两种不同的情况下执行相同的代码

- 循环语句

  - 循环三要素

    1. 变量起始值
    2. 终止条件
    3. 变量变化量

  - while循环

    ```javascript
    while (循环条件) {
        循环体
    }
    ```

    - 小括号里的循环条件为true时进入执行代码
    - 大括号里的代码执行完毕后不会跳出，而是继续回到小括号里判断条件是否满足，若满足则继续执行大括号里的代码

  - do-while循环

    ```javascript
    do{
        循环体
    }while（
        循环条件
    ）
    ```

    - 先执行循环体
    - 执行完毕后判断循环条件，如为false则语句结束，如为true则继续执行循环体
    - do-while循环可以确保至少执行一次，其他时候和while使用上没有区别

  - for循环

    - for循环语法

      ```javascript
      for (声明记录循环次数的变量; 循环条件; 变化值) {
          循环体
      }
      ```

  - 循环结束

    - continue：结束本次循环，继续下次循环
    - break：跳出所在的循环

  - 测试程序执行事件

    - console.time()开始计时
    - console.timeEnd()结束计时

## 对象(object)

- 对象

  - 对象是一种复合数据类型，在对象中可以存储其他的数据
  - 对象实际上就是数据的容器
  - 对象中所存储的数据称为属性
  - 对象的两个层面
    - 狭义：在JS中对象指Object
    - 广义：在JS中所有东西都是对象

- 对象声明语法

  ```javascript
  let 对象名 = {
      属性名: 属性值,
      方法名: 函数
  }
  ```

  - 多个属性或对象之间用逗号分隔
  - 属性名或对象名可以加单引号或双引号，一般情况下省略，除非属性名中遇到特殊符号，如空格、中横线等

- 对象属性访问

  - 方式一

    对象名.属性名

  - 方式二

    对象名['属性名']

- 对象方法访问

  - 对象名.方法()

- 操作对象

  - 添加新属性/修改对象

    - 对象名.属性名 = 属性值
      - .后面的属性名只能是字面量
    - 对象名[属性名] = 属性值
      - 若[]中的内容为字符串字面量，则字符串的内容被定义为属性名
      - 若[]中的内容是一个装有字符串的变量，则变量中字符串的内容被定位为属性名

  - 添加新方法

    对象名.方法名 = function() {函数体}

  - 删除属性

    delete 对象名.属性名

- 可选链操作符(`?.`)和空值合并运算符(`??`)

  - 例

    ~~~javascript
    const animal = {
        cat: {
            name: 'kitty',
            color: 'black'
        },
        dog: {
            name: 'doggie'
        }
    }
    
    let catName 
    catName = animal && animal.cat && animal.cat.name
    // 等价于
    catName = animal?.cat?.name
    
    const dogColor = animal?.dog?.color??'white'
    ~~~

    - `?.` 不能只能用于访问，不能将其赋值

  - `??` 与 `||` 的区别

    ~~~javascript
    console.log(1 || "xx") 			//1
    console.log(0 || "xx") 			//xx
    console.log(null || "xx")		//xx
    console.log(undefined || "xx")  //xx
    console.log(-1 || "xx") 		//-1
    console.log("" || "xx") 		//xx
    
    console.log(1 ?? "xx")			//1
    console.log(0 ?? "xx") 			//0
    console.log(null ?? "xx") 		//xx
    console.log(undefined ?? "xx")  //xx
    console.log(-1 ?? "xx") 		//-1
    console.log("" ?? "xx") 		//''
    
    // 对于 ?? 来说，只有 null 和 undefined 才是假值
    ~~~

- 遍历对象

  - 对象没有length属性，无法确定长度

  - 对象里是无序的键值对，没有序号

  - ```javascript
    let obj = {
        uname: 'andy',
        age: 18,
        gender: '男'
    }
    for (let k in obj) {
        console.log(k) // 打印属性名
        console.log(obj[k]) // 打印属性值
    }
    ```

- JS对象的分类

  1. 内置对象
     - 由ES标准所提供的对象
     - 如：Object Function String Boolean Number Array Math Date Json...
  2. 宿主对象
     - 由JS运行环境所提供的对象
     - 如：由浏览器提供的BOM对象Window Console Document ...，以及DOM对象
  3. 自定义对象

- JS内置对象

  - Math对象

    - 提供一系列数学常量属性和运算方法

    - 属性举例

      - Math.PI：圆周率

    - 方法举例

      - random()： 生成0-1之间的随机数（包含0不包含1）
      - ceil()：向上取整
      - floor()：向下取整
      - round()：四舍五入取整
      - max()：返回多个数值中的最大值
      - min()：返回多个数值中的最小值
      - pow(x, y)：幂运算，返回x的y次幂
      - aqrt()：求平方根
      - abs()：绝对值

    - 生成整数N-M之间之间的随机整数(包含N和M)

      ```javascript
      Math.floor(Math.random()*(M-N+1))+N
      ```
    
  - eval()函数

    - eval() 函数会将传入的字符串当做 JavaScript 代码进行执行

- in运算符

  - 用来检查对象中是否含有某个属性，返回布尔值

    ```javascript
    '属性名' in 对象
    ```

  - 如果属性存在于对象原型中，也会返回true

  - 当in运算符运用在数组中时，则会检查数组是否含有该索引

    ```javascript
    索引 in 数组
    ```

## 数组(array)

- 数组用来存储一组有序的数据

- 数组中存储的数据，称为元素(Element)

  - 数组元素会根据存储的顺序保存在数组中
  - 数组中的每个元素都有一个唯一的索引(index)
  - 索引由0开始

- Array()是一种构造函数，使用Array()创建的实例对象就是数组

- 当使用typeof检查一个数组时，会返回object

  可以使用类方法（静态方法）Array.isArray(对象)检查一个对象是否为数组

- 数组里的元素可以是任意数据类型

  但是在使用数组时，尽量存放相同的数据类型

- 数组中包含数组称之为二维数组，继续套娃称之为多维数组

- 创建数组对象

  ```javascript
  // 声明一个新数组arr
  let arr = new Array()
  // 向数组arr中添加元素
  arr[0] = 10
  arr[1] = 20
  ```

- 创建一个指定长度的数组

  ```javascript
  let 数组名 = new Array(长度)
  ```

- 创建数组的同时往里添加元素

  ```javascript
  let 数组名 = [元素1, 元素2, …, 元素n]
  ```

  ```javascript
  let 数组名 = new Array(元素1, 元素2, …, 元素n)
  ```

- 读取语法

  ```javascript
  数组名[下标]
  ```

- Array.prototype.length

  - length用来获取数组内的元素个数
  - length的值实际上是数组的最大索引+1
  - length的值可以手动修改
    - 如果length改小，多余的元素会被删除
    - 如果length改大，多出的元素是空元素

  ```javascript
  // 向数组的末尾添加元素
  数组.[数组.length] = 值
  ```

- 遍历数组

  - for循环遍历数组

    ```javascript
    // 正序遍历
    for (let i = 0; i < 数组名.length; i++) {
        console.log(数组名[i])
    }
    // 倒序遍历
    for (let i = arr.length - 1; i >= 0; i--){
        console.log(数组名[i])
    }
    // for in
    for (let i in 数组名){
        console.log(数组名[i])
    }
    // for of
    for (let v of 数组名){
        console.log(v)
    }
    // i指代索引，v指代值
    ```

  - 遍历数组方法

    - forEach()方法

      - forEach()方法用于调用数组的每个元素，并将元素传递给作为参数的回调函数，并执行该回调函数

      ```javascript
      const arr = ['red', 'green', 'pink']
      arr.forEach(function(item,index) {
              console.log(item) // 数组元素
              console.log(index) // 索引号
                  })
      ```

      - 数组元素item必须要写，index可以省略
      - 常用于遍历数组对象
      - forEach()方法本身不会返回值
      - 参数
        - 当前被遍历的元素
        - 当前遍历的元素的索引
        - 当前正在被遍历的数组（一般用不到）

    - map()方法

      - map()方法创建一个新的数组，其结果是原数组中每个元素调用一次回调函数后的返回值

      ```javascript
      const arr = [1, 2, 3, 4]
      const newArr = arr.map(function(item, index) {
          console.log(item) // 数组元素
          console.log(index) // 索引号
          return item + 1
      })
      console.log(arr) // [1, 2, 3, 4]
      console.log(newArr) // [2, 3, 4, 5]
      ```

      - map()方法会返回值
    
    - filter()方法
    
      - filter()方法创建一个新的数组，新数组中的元素是通过检查指定数组中符合条件的所有元素
      - 主要使用场景：筛选数组符合条件的元素，并返回筛选之后元素的新数组
    
      ~~~javascript
      const arr = [10, 20, 30]
      // 筛选出大于等于20的单元值，返回给新数组
      const newArr = arr.filter(function(item, index) {
          return item >= 20
      })
      console.log(newArr) // [20, 30]
      
      // 使用箭头函数表达
      const newArr = arr.filter(item => item >= 20)
      ~~~

- 操作数组

  - 增加数据

    1. arr.push(元素1, …, 元素n)
       - 将一个或多个元素添加到数组的末尾，并返回该数组的新长度
    2. arr.unshift(元素1, …, 元素n)
       - 将一个或国歌元素添加到数组的 开头，并返回该数组的新长度

  - 删除数据

    1. arr.pop()

       - 从数组中删除最后一个元素，并返回该元素的值

    2. arr.shift()

       - 从数组中删除第一个元素，并返回该元素的值

    3. arr.splice(start, deleteCount, item1, …,itemN)

       - 删除数组中的指定元素，可在该位置插入新元素，返回被删除的元素
       - start
         - 表示移除的数组元素的起始位置的下标
       - deleteCount
         - 表示移除的数组元素的个数
         - 如果省略，则从指定的起始位置删除到最后
       - item1, …,itemN
         - 要插入的元素

    4. arr.slice(begin, end)

       - 用来截取数组，将结果作为返回值返回，不会影响原来的数组

       - 参数

         - 截取的起始索引（包含起始位置）

         - end

           截取的结束索引（不包含结束位置）

         - 当第一个参数是负值时，从后往前计算，-1表示倒数第一个

         - 第二个参数可以省略，会直接截取到最后一个元素

       - arr.slice()方法，设置第一个参数为0，省略第二个参数，可以实现数组的浅拷贝（如果数组的元素都是非引用类型，则和深拷贝没有区别）
    
  - 改变数组顺序

    1. arr.reverse()
       - 将数组反转
       - 破坏性方法，会改变原数组

    2. arr.sort(compareFn)

       - 对数组进行排序

       - 破坏性方法

       - 参数

         - conpareFn（可选）

           用来指定按某种顺序进行排列的回调函数，如果省略，元素按照转换为字符串的Unicode位点进行排序

       - 用法

         ```javascript
         let arr = [1, 100, 20]
         
         // 升序
         arr.sort(function (a, b) {
             return a - b
         })
         console.log(arr) // [1, 20, 100]
         
         // 降序
         arr.sort(function (a, b) {
             return b - a
         })
         console.log(arr) // [100, 20, 1]
         
         // 乱序
         arr.sort(function (a, b) {
             return Math.random() - Math.random()
         })
         ```

         - a和b是两个将要比较的元素
         - 如果回调函数返回的值大于0，那么b会被排列到a之前
         - 如果回调函数返回的值小于0，那么a会被排列到b之前
         - 如果回调函数返回的值等于0，那么a和b的相对位置不变
         - 回调函数必须总是对相同的输入返回相同的结果，否则排序的结果将不确定


- 数组的其他方法

  - arr.concat()

    - 用来合并多个数组
    - 非破坏性方法，不会影响原数组

    ```javascript
    let arr = [1,2,3]
    let arr2 = [4,5]
    let result = arr.concat(arr2, [6,7,8], 9)
    console.log(result) // [1, 2, 3, 4, 5, 6, 7, 8, 9]
    ```

  - arr.indexOf(searchElement, fromIndex)

    - 返回在数组中可以找到的给定元素的第一个索引，如果不存在，则返回-1

    - 参数

      - searchElement

        要查找的元素

      - fromIndex（可选）

        开始查找的位置，如果是负数则表示从倒数第几位开始查找，但查找顺序仍是从前往后

  - arr.lastIndexOf(searchElement, fromIndex)

    - 返回在数组中可以找到的给定元素的最后一个索引，如果不存在，则返回-1
    - 除了查找方向与indexOf()相反。其余使用方法与indexOf()相同
  
  - arr.join(separator)

    - 将数组中的所有元素连接成一个字符串来返回结果

    - 非破坏性方法

    - 参数

      - separator（可选）

        指定一个字符串来分隔数组的每个元素，如果是空字符串' '，则元素之间没有字符分隔，默认为','

    ```javascript
    let arr = [1, 2, 3]
    console.log(arr.join('.')) // 1.2.3
    ```
  
  - arr.find(callback)/arr.findIndex(callback)

    - 返回数组中满足条件（回调函数返回值为true）的第一个元素，没有则返回undefined
  
    - 参数
  
      - callback（必选）
        - 回调函数中可传入三个参数
          1. item
            - 当前被遍历的元素，必选
          2. index
            - 当前索引，可选
          3. array
            - 当前数组，可选
        
      - this (可选)
  
        - 执行回调时用作this的对象

    - 用法
  
      ~~~javascript
      const array = [10, 11, 3, 20, 5]
      const greaterThanTen = array.find(item => item > 10)
      console.log(greaterThanTen) // 11
      ~~~
  
  - arr.includes(searchElement, fromIndex)
  
    - 判断一个数组是否包含一个指定的值，返回布尔值
    - 参数
      - searchElement
        - 需要查找的元素值，必选
      - fromIndex
        - 从指定序号开始查找，可以为负值，默认为0
  
  - arr.reduce(callback, initialValue)
  
    - 遍历数组时执行回调函数，返回值为最后一次执行的回调的返回值
  
    - 参数
  
      - callback
  
        - 回调函数中可传入四个参数
  
          1. preValue
  
             - 第一次执行回调时
               - 如果有传initialValue，preValue为initialValue
               - 如果没有传initialValue，preValue为数组的第一个元素，且遍历会从第二个元素开始
             - 之后执行回调时
               - preValue为上一次执行回调的返回值
          2. current
             - 当前被遍历的元素
          3. index
             - 当前被遍历的元素的序号
          4. arr
             - 被遍历的数组本身
      - initialValue
        - 初始值
      
    - 用法
    
      ~~~javascript
      const arr = [1,2,3,4,5,6,7,8,9,10]
      
      // 求数组中所有偶数的和（条件计算）
      arr.reduce((prev, current) => prev + (current % 2 === 0 ? current : 0), 0)
      
      // 求数组中偶数的个数（条件统计）
      arr.reduce((prev, current) => prev + (current % 2 === 0 ? 1 : 0), 0)
      
      // 求数组中所有偶数的乘积（条件计算）
      arr.reduce((prev, current) => prev * (current % 2 === 0 ? current : 1), 1)
      
      // 求数组中的最小值（筛选最值）
      arr.reduce((prev, current) => Math.min(prev, current))
      ~~~
    
  
- `Array.from(arrayLike[, mapFn][, thisArg])`

  - 从可迭代或类数组对象创建一个新的浅拷贝的数组实例
  - 参数
    - `arrayLike`
      - 想要转换成数组的类数组或可迭代对象。
    - `mapFn`（可选）
      - 调用数组每个元素的函数，将返回值替换到数组中，用法类似 `Array.prototype.map`
    - `thisArg`（可选）
      - 执行 `mapFn` 时用作 `this` 的值

## 函数(function)

- 函数声明语法

  ```javascript
  function 函数名(形参) {
      函数体
  }
  ```

- 函数调用语法

  ```javascript
  函数名(实参)
  ```

- 函数设置了形参，相当于在函数中声明了一个局部变量

- 函数命名规范

  - 和变量命名基本一致

  - 小驼峰命名法

  - 前缀为动词

    | 动词 |          含义          |
    | :--: | :--------------------: |
    | can  | 判断是否可执行某个动作 |
    | has  |   判断是否含有某个值   |
    |  is  |    判断是否为某个值    |
    | get  |       获取某个值       |
    | set  |       设置某个值       |
    | load |      加载某些数据      |

- 函数返回值

  - 函数体中使用return关键字能将内部执行结果交给函数外部使用
  - return之后的代码不会再被执行
  - 函数体中没有return时，返回值为undefined

- 作用域

  1. 全局作用域

     在整个script中有效

  2. 局部作用域

     在函数内部有效

  3. 块级作用域

     在{}内有效

- 立即执行函数

  ```javascript
  // 方式一
  (function () {函数体})();

  // 方式二
  (function () {函数体}());

  //多个立即执行函数要用;隔开
  ```

- 指定函数的this的方法

  - call()
    - 使用call()方法可以调用函数
    - call()的第一个参数可以指定函数的this
    - 如需传递函数本身的参数，可将其跟在call()的第一个参数后面
    
  - apply()
    - apply()和call()作用相同
    - apply()的实参需要通过数组来传递
    
    ~~~javascript
    let obj = {a:1}
    function fn (a, b) {
        console.log(this,a, b)
    }
    
    fn.call(obj, 1, 2) // {a:1} 1 2
    fn.apply(obj, [1, 2]) // {a:1} 1 2
    ~~~
  
  - bind()
  
    - 当一个函数调用bind()方法时，该方法会返回一个新的函数对象
    - 返回的函数对象功能和原来的函数一样，不同点在于新函数的this绑定了bind()方法的第一个参数
  
    ```javascript
    let obj = {a:1}
    function fn (a, b) {
        console.log(this,a, b)
    }
    let fn2 = fn.bind(obj, 1, 2)
    fn2() // {a:1} 1 2
    ```


- 递归

  - 递归就是函数自己调用自己

  - 递归的核心思想在于将大问题拆分成小问题

    - 递归能做的循环也能做，循环能做的递归也能做
    - 由于递归需要开辟新的内存空间，因此性能比循环差

  - 递归的两个条件

    1. 基线条件

       递归停止条件

    2. 递归条件

       如何对问题进行拆分

  ```javascript
  // 求任意数的阶乘
  function factorial (num) {
      
      // 基线条件
      if (num === 1){
          return 1;
      }
      
      // 递归条件
      return num * factorial(num - 1)
  }
  ```

  ```javascript
  // 快速排序
  let arr = [4, 8, 7, 6, 5, 2, 9, 3, 1]
  function quickSort(a) {
      if (a.length <= 1) {
          return a
      }
      let left = [], right = []
      for (let i = 1; i < a.length; i++) {
          if (a[i] <= a[0]) {
              left.push(a[i])
          } else {
              right.push(a[i])
          }
      }
      return quickSort(left).concat(a[0], quickSort(right))
  }
  console.log(quickSort(arr));
  ```

## 术语解释

|      术语      |                             解释                             |                       举例                        |
| :------------: | :----------------------------------------------------------: | :-----------------------------------------------: |
|     关键字     |                    在JS中有特殊意义的词汇                    | let、var、function、if、else、switch、case、break |
|     保留字     |      在目前JS中没有意义，但未来可能会具有特殊意义的词汇      |              int、short、long、char               |
| 标识（标识符） |                  变量名、函数名的另一种叫法                  |                                                   |
|     表达式     |              能产生值的代码，一般配合运算符出现              |               10 + 3、age &gt;= 18                |
|      语句      | 一句代码也称之为一条语句，一般按用途还可以分为：输出语句、声明语句、分支语句 |                                                   |

## 堆栈

- 简单数据类型又叫基本数据类型或值类型，复杂数据类型又叫引用类型
- 值类型在存储时，变量中存储的是值本身，存储在栈中
- 引用类型在存储时，变量在栈中存储的是引用的地址，真正的数据内容存储在堆中

## 作用域(scope)

### 全局作用域和局部作用域

- 作用域规定了变量能够被访问的范围
- 作用域在定义的时候已经被确定了
- 作用域分为全局作用域和局部作用域
- 局部作用域分为函数作用域和块作用域
  - 函数作用域：在函数内部声明的变量只能在函数内部被访问，外部无法直接访问
  - 块作用域：在JavaScript中使用{}包裹的代码称为代码块，代码块内用let声明的变量和用const声明的常量在外部无法被访问
    - let声明的变量会产生块作用域，var不会产生块作用域
    - const声明的常量也会产生块作用域
- 全局作用域（global）
  - 全局作用域在页面加载时创建，在页面关闭时销毁
  - 全局作用域中有一个全局对象（global object），称之为window，winbdow对象代表浏览器的窗口
  - 在全局中使用var声明的变量都会作为window对象的属性保存，函数会作为window对象的方法保存
  - 函数中未使用任何关键字声明的变量是全局作用域，不推荐使用
  - 尽可能减少声明全局变量，防止全局变量被污染

### 作用域链

- 作用域链本质上是底层的变量查找机制

  - 当函数被执行时，会优先查找当前函数作用域中查找变量
  - 如果当前作用域查找不到则会依次逐级查找父级作用域直到全局作用域 

- 嵌套关系的作用域串联起来形成了作用域链

- ```javascript
  let a = 1
  function fn() {
      console.log(a)
  }
  function fn2() {
      let a = 2
      fn()
  }
  fn2() // 1
  // JS中的作用域是词法作用域（静态作用域），由写代码时将变量和块作用域写在哪里来决定，和在哪调用函数无关
  // 因此在函数fn中查找a时，其父级作用域是全局作用域，而不是函数fn2作用域
  ```

  ```javascript
  const name = '小明'
  const obj = {
      name:'张三',
      fn1() {
          console.log(name)
      },
      fn2() {
          console.log(this.name)
      }
  }
  obj.fn1() // 小明
  obj.fn2() // 张三
  // 对象obj中的方法里调用的name是变量，而不是obj的属性name，当函数被执行时，函数作用域内没有name变量，会沿着作用域链找到父级window，若需要调用对象自身的属性，需要用this
  ```

### 垃圾回收机制(Garbage Collection)

- 不再用到的内存，没有及时释放，称之为内存泄漏

- 内存的生命周期

  1. 内存分配：声明变量、函数、对象的时候，系统会自动为它们分配内存
  2. 内存使用：即读写内存，也就是使用变量、函数等
  3. 内存回收：使用完毕，由垃圾回收自动回收不再使用的内存
  4. 说明：
     - 全局变量一般不会回收，直到关闭页面
     - 一般情况下局部变量的值，不用了就会自动被回收

- 垃圾回收算法

  核心思想是如何判断内存是否已经不再会被使用，如果是，则释放掉

  - 引用计数法

    IE采用的引用技术法，定义“内存不再使用”的标准是看一个对象是否有指向它的引用

    - 算法

      1. 跟踪记录每个值被引用的次数（引用数据类型）
      2. 如果这个值被引用了依次，那么就记录次数为1
      3. 多次引用会累加
      4. 如果减少一个引用就-1
      5. 如果引用次数是0，就释放内存

    - 缺陷：嵌套引用

      如果两个对象互相引用 ，尽管它们已不再使用，也不会被回收，导致内存泄漏

  - 标记清除法

    现代浏览器通用的大多是基于标记清除法的改进算法，总体思想是一致的

    - 核心
      1. 标记清除法将“不再使用的对象”定义为“无法达到的对象”
      2. 从根部（JS中是全局对象）出发定时扫描内存中的对象
      3. 从根部出发无法触及到的对象被标记为不再使用，稍后进行回收

### 闭包

- 概念

  - 一个函数对周围状态的引用捆绑在一起，内层函数中访问到其外层函数的作用域
  - 简单理解：闭包 = 内层函数 + 外层函数的变量

- 闭包的作用

  1. 封闭数据，实现数据私有，外部也可以访问函数内部的变量
  2. 允许将函数与其所操作的某些数据（环境）关联起来

- 闭包构成要素

  1. 闭包必须要有函数的嵌套
  2. 内部函数必须要访问外部函数的变量
  3. 必须将内部函数作为返回值返回

- 闭包的常见写法

  ```javascript
  function outer() {
      let a = 10
      function fn() {
          a++
          console.log(a)
      }
      return fn // 返回fn这个函数
  }
  const fun = outer() // outer()的执行结果是outer内部的fn这个函数，因此现在fun和outer内的fn等价
  fun() // 相当于执行outer内的fn
  // outer内定义的变量a只有fn可以访问
  const fun2 = outer() // fun和fun2产生了两个独立的闭包，它们内部的a互不影响
  ```

  ```javascript
  // 当只需要产生一个闭包时，可将外部函数写成立即执行函数，直接用函数表达式赋值给一个变量
  const fun = (function outer() {
      let a = 10
      return function () {
          a++
          console.log(a)
      } 
  })();
  fun()
  ```

- 闭包的生命周期

  1. 当外部函数被调用时，闭包产生
     - 外部函数每当被调用一次，就会产生一个闭包
     - 不同的变量引用了内部函数，会产生独立的闭包
  2. 只要内部函数还被引用，闭包就一直存在
  3. 当内部函数不再被引用时，闭包就被销毁

- 闭包的应用：实现数据的私有

  ```javascript
  // 统计函数被调用了几次  
  function fn() {
      let count = 1
      function fun() {
          count++
          console.log(`函数被调用了${count}次`)
      }
      return fun
  }
  const result = fn()
  result()
  // count变量在函数中被声明，无法在全局中被修改，实现了数据的私有
  // global→result→fn→count，全局对象可以扫描到count，因此count不会被回收
  ```

  - 过度使用闭包会造成内存泄漏

### 变量提升

- 变量提升是JS中的一种缺陷，它允许变量在声明之前就被访问（仅存在于var声明变量）
- var声明的变量，会将声明提升到当前作用域的最前面，只提升声明，不提升赋值

## 函数进阶

### 函数提升

- 函数声明会被提升到当前作用域最前面
- 函数表达式不存在函数提升现象

### 函数参数

- 参数默认值

  ```javascript
    // 设置参数默认值
    function sayHi(name="小明", age=18) {
      document.write(`<p>大家好，我叫${name}，我今年${age}岁了。</p>`);
    }
    // 调用函数
    sayHi();
    sayHi('小红');
    sayHi('小刚', 21);
  ```

  - 声明函数时若为形参赋值，该值即为参数的默认值
  - 参数未自定义默认值时，默认值为undefined
  - 调用函数时没有传入对应实参时，参数默认值作为实参传入函数

- 动态参数

  ```javascript
  function getSum() {
      console.log(arguments) // 打印结果为[1, 2, 3]
  }
  getSum(1, 2, 3)
  ```
  
  
  - 参数名：arguments
  
  
  
  - 只存在于函数中
  
  
  
  - 是一个伪数组，包含函数的所有实参
  
  
  
  - 可以动态获取函数的实参
  


- 剩余参数

  ```javascript
  function getSum(a, b, ...others) {
      console.log(others) // 打印结果为[3]
  }
  getSum(1, 2, 3)
  ```

  - ...是语法符号，置于最末函数形参之前，用于获取剩余的实参
  - 借助...获取的剩余实参，是一个真数组

- 拓展：展开运算符

  ```javascript
  // 1.求数组最大值
  const arr1 = [1, 2, 3] // ...arr等价于1, 2, 3
  console.log(Math.max(...arr1)) // 打印结果为3
  
  // 2.合并数组
  const arr2 = [4, 5, 6]
  const arr3 = [...arr1,...arr2] // arr3 === [1, 2, 3, 4, 5, 6]
  ```

  - 展开运算符(...)，将一个数组进行展开

### 箭头函数

- 更简短的函数写法

- 适用于那些本来需要匿名函数的地方

- 基本写法

  ```javascript
  const fn = (形参) => {函数体}
  ```
  
- 简写情况

  - 不写小括号：当形参有且只有一个时
  - 不写大括号：当代码体只有一条语句时，且语句的执行结果会作为函数返回值

  ```javascript
  // 当只有一个形参时，可以省略小括号（没有形参时可以写空小括号）
  const fn = x => {
      console.log(x)
  }
  fn(1)
  ```

  ```javascript
  // 当只有一行函数体时，可以省略大括号
  const fn = x => console.log(x)
  fn(1)
  ```

  ```javascript
  // 当只有一行函数体时，无需写return可以直接返回值
  const fn = x => x + x // 相当于 return x + x
  ```

  ```javascript
  // 作为匿名函数使用
  const form = document.querySelector('form')
  form.addEventListener('click', e => e.preventDefault())
  ```

  ```javascript
  // 直接返回一个对象
  const fn = (name) => ({uname: name}) // 大括号表示这是一个对象，为避免大括号与函数体大括号混淆，在外包裹一个小括号
  console.log(fn('小明')) 
  ```

- 箭头函数没有arguments动态参数，但是有剩余参数

- 箭头函数不创建this，会从自己作用域链的上一层沿用this

  ```javascript
  const obj = {
      traceThis: () => console(this) // 此处的this沿用了obj的this
  } 
  obj.traceThis() // this指向obj的调用者window
  ```

  ```javascript
  function fn() {
      console.log(this)
  }
  let fn3 = () => console.log(this);
  
  let obj = {
      fn: fn,
      fn2: function () {
          console.log(this);
  
          function fn3() {
              console.log(this)
          }
  
          let fn4 = () => console.log(this)
  
          fn3()
          fn4()
      }
  }
  obj.fn() // this指向obj
  obj.fn2() // fn2中的this指向obj，fn3中的this指向window,fn4中的this沿用了fn2的this指向了obj
  // 虽然fn3是在由obj调用的fn2方法中被定义和调用，但实际上时fn2方法中的代码让window调用了fn3，因此fn3的this指向window
  ```


  - 在DOM事件回调函数中若需要使用this，不建议使用箭头函数

## 构造函数(constructor)

- 构造函数就是专门用来创建对象的函数
- 构造函数的定义方式和普通函数唯一的不同点在于构造函数的名字首字母要大写
- 构造函数需要使用new关键字来调用
- 构造函数的执行流程
  1. 构造函数执行时，会先创建一个新的对象
  2. 将这个新的对象设置为函数中的this
  3. 执行函数中的代码
  4. 将创建的对象返回
- 一个构造函数也称为一个类（class）
  - 通过构造函数所创建的对象，称之为该类实例
  - 通过同一个类创建的对象，称为同类对象
- instanceof运算符
  - 作用：检查一个对象是否是一个类的实例
  - 语法：对象 instanceof 类

```javascript
function Person(name, age){
    this.name = name,
    this.age = age,
    this.sayHello = function() {
        console.log(`大家好，我是${this.name}`)
    }
}
let person = new Person('张三', 18)
console.log(person)
console.log(person instanceof Person) // true
```

- 实例中的方法
  - 构造函数每执行一次，都会创建一个新的函数对象
  - 直接定义在构造函数中的方法，每次被实例化到对象中都会被创建一次
  - 这些方法的功能一样，却创建了不同引用地址，浪费大量内存空间
  - 如果将方法定义为全局函数，再赋值到构造函数中，会污染全局的命名空间（namespace）
  - 可以使用原型对象添加方法

## 原型(prototype)

- 对象的两个层面

  - 狭义：在JS中对象指Object
  - 广义：在JS中所有东西都是对象

- 原型

  - 每一个函数都有一个属性叫做prototype（显式原型），该属性指向的是一个对象，这个对象就是原型对象
  - 如果函数作为一个构造函数去调用，那么它所创建的实例中会有一个隐含的属性\_\_proto\_\_（隐式原型），该属性指向该构造函数的prototype（显式原型） 
  - 使用.prototype获取构造函数（类）的原型，使用.\_\_proto\_\_获取实例对象的原型
  - 原型对象相当于一个公共区域，可以被所有的该类实例所共享，可以将实例中共有的属性和方法统一存储到原型对象中

- 原型链

  1. JS中当访问一个对象的属性时，会首先在对象自身中寻找
  2. 如果找到了则使用，如果没找到，则去对象的原型中寻找
  3. 如果在原型中没有找到，则继续去原型的原型中寻找
  4. 直到找到Object的原型，它的原型是null，如果依然没有找到则返回undefined

  - Object是所有对象的原型，是根对象
  - porototype也是由new Object()构造函数创建的
  - instanceof运算符就是检查对象的原型链上是否有类存在，因此任意实例使用instanceof检查Object都会返回true
  - constructor是构造函数显式原型对象上的一个属性，指向这个原型对象对应的构造函数 

- constructor属性

  - 构造函数（类）的prototype对象中的constructor属性，指向了构造函数（类）本身
  - 可以通过检查实例对象的隐式原型的constructor属性，来检查实例对象的类
  - 实例对象.\_\_proto\_\_.constructor指向了实例对象的类
  
- hasOwnProperty()方法

  - 用来检查一个属性是否存在于对象自身中

  - 语法

    ```javascript
    console.log(对象名.hasOwnProperty('属性名'))
    ```

  - ```javascript
    function Person(name, age){
        this.name = name,
        this.age = age
    }
    Person.prototype.sayHello = function () {console.log(`大家好，我是${this.name}`)}
    let person1 = new Person('张三', 18)
    let person2 = new Person('小明', 16)
    console.log(person1.hasOwnProperty('name')) // true
    console.log(person1.hasOwnProperty('sayHello')) // false
    console.log(person1.__proto__.hasOwnProperty('sayHello')) // true
    ```

## 解构赋值

### 数组解构

- 数组解构时将数组的单元值快速批量赋值给一系列变量的简洁语法

- 基本写法

  ```javascript
  const [a, b, c] = [100, 60, 80]
  consolr.log(a) // 100
  consolr.log(b) // 60
  consolr.log(b) // 80
  ```

- 数组解构可以使用剩余参数

- 应用：变量值交换

  ```javascript
  let a = 1
  let b = 2
  ;[a, b] = [b, a] // 当数组在一行开头时，会被认为与上一行代码在同一行，因此需与之前代码之间加分号
  console.log(a) // 2
  console.log(b) // 1
  ```

- 设置默认值

  ```javascript
  const  [a=1, b=2, c=3] = [4, 5]
  console.log(a) // 4
  console.log(b) // 5
  console.log(c) // 3
  // 若没有给c设置默认值，则c的值会变成undefined
  ```

- 按需导入赋值

  ```javascript
  const [a, , c] = [1, 2, 3]
  console.log(a) // 1
  console.log(c) // 3
  ```

- 多维数组

  ```javascript
  const arr = [1, 2, [3, 4]]
  console.log(arr[0]) // 1
  console.log(arr[1]) // 2
  console.log(arr[2]) // [3, 4]
  console.log(arr[2][0]) // 3
  console.log(arr[2][1]) // 4
  ```

- 多维数组解构

  ```javascript
  const [a, b, [c, d]] = [1, 2, [3, 4]]
  console.log(a) // 1
  console.log(b) // 2
  console.log(c) // 3
  console.log(d) // 4
  ```

### 对象解构

- 对象解构是将对象属性和方法快速批量赋值给一系列变量的简洁语法

- 基本写法

  ```javascript
  const {a, b} = {a: 1, b: 2} // 相当于 const a = obj.a, b = obj.b
  ```

  - 对象属性的值被赋值给与属性名相同的变量
  - 注意解构的变量名不要和当前作用域内的变量名冲突
  - 解构的变量名在对象中找不到对应属性名时，值为undefined

- 对象解构的变量名重新改名

  ```javascript
  const a = 1 //当要解构的对象的属性名在定义域内有同名变量时，可以改变对象解构的变量名
  const {a: A, b} = {a: 1, b:2}
  console.log(A) // 1
  ```

- 数组对象的解构

  ```javascript
  const arr = [
  {
        uname: '小明'，
        age: 18
   }
  ]
  const [{uname, age}] = arr
  console.log(uname) // 小明
  console.log(age) // 18

  ```

- 多级对象解构

  ```javascript
  const pig = {
      name: '佩奇',
      family: {
          mother: '猪妈妈',
          father: '猪爸爸',
          sister: '乔治'
      },
      age: 6
  }
  const{name, family: {mother, father, sister}, age} = pig
  ```

  ```javascript
  const msg = {
      "code": 200
      "msg": "获取新闻列表成功"
      "data": [
      {
          "id": 1,
          "title": "新闻标题1"
          "count": 58
      },
      {
          "id": 2,
          "title": "新闻标题2"
          "count": 56
      }
      ]
  }
  
  // 打印msg对象中的data对象
  function render({data}) {
      console.log(data)
  }
  render(msg)
  // 送入msg对象到函数中，利用函数的形参做对象解构
  
  //以下是简化前的写法
  const [{data}] = arr // msg传给arr，实现对象解构
  function render(arr) {
      consolr.log(arr)
  }
  render(msg)
  ```

## 实例化对象

### Date

- Date是表示日期的对象，在JS中所有的时间相关的数据都通过Date来表示

- 创建对象

  - 创建一个表示当前日期的对象

    ```javascript
    let d = new Date()
    ```

  - 创建一个指定日期的对象

    ```javascript
    let d = new Date('四位数年份-月-日 时:分:秒')
    ```

- 实例方法

  |     方法     |        作用        |               注意               |
  | :----------: | :----------------: | :------------------------------: |
  | getFullYear  |    获取当前年份    |                                  |
  | getMounth()  |    获取当前月份    | 返回值0-11，返回值+1代表当前月份 |
  |  getDate()   | 获取当前日期是几号 |                                  |
  |   getDay()   |   获取当前是周几   |       返回值0-6，0代表周日       |
  |  getHours()  |   获取当前小时数   |            返回值0-23            |
  | getMinutes() |   获取当前分钟数   |                                  |
  | getSeconds() |    获取当前秒数    |                                  |

  - 获取今天是周几，并用中文表示

    ```javascript
    unconst weekName = ["周日", "周一", "周二", "周三", "周四", "周五", "周六"]
    let day = new Date().getDay()
    console.log(weekName[day])
    ```

  - 总秒数换算

    - d = parseInt(总秒数/60/60/24); // 计算天数
    - h = parseInt(总秒数/60/60%24); // 计算小时数
    - m = parseInt(总秒数/60%60); // 计算分钟数
    - s = parseInt(总秒数%60); // 计算秒数

- 获取时间戳（从1970-01-01 00:00:00开始到某时刻的总毫秒数，计算机底层所有的时间都是以时间戳的形式存储）

  - \+ new Date()

    获取当前的时间戳

  - \+ new Date('四位数年份-月-日 时:分:秒')

    获取指定时间的时间戳

  - d.getTime()

    获取实例对象的时间戳

  - Date.now()

    获取当前时间戳

### 包装类

- JS中提供了三个包装类：String、Number、Boolean

- 包装类可以用来创建基本数据类型的对象，但是在开发中不要自己创建这些对象

  ```javascript
  let num = new Number(10)
  let num2 = new Number(10)
  console.log(num === num2) // false
  // 由于num和num2都是对象，在进行全等比较时，比较的时对象指向的内存地址，因此num和num2不全等
  ```

- 当调用字符串、数值、布尔值的属性或方法时，浏览器会通过包装类临时将其转换为对象，然后调用该对象的属性或方法

  ```javascript
  let num = 10
  console.log(num.toString()) // 打印结果为字符串的10
  // 在给基本数据类型num调用toString()方法时，num被临时转换为Number的实例对象，从而可以条用方法Number.prototype.toString()
  ```

- String

  - 字符串的本质就是一个字符数组

    ```javascript
    let str= 'Hello' // 在内存中的存储形式等同于['H','e','l','l','o']
    console.log(str[0]) // 'H'
    ```

  - 字符串可以调用一些数组方法，但是破坏性的方法不可用

  - 字符串可以调用的一些方法（全是非破坏性方法，返回值，不改变原字符串）

    - `String.prototype.charAt()`

      - 根据索引获取字符串中的字符

    - `String.prototype.charCodeAt()`

      - 根据索引获取字符串中的字符的编码

    - String.prototype.String.fromCharCode()

      - 根据编码返回字符

    - `String.prototype.indexOf()`/`String.prototype.lastIndexOf()`

      - 返回子串在字符串中第一次/最后一次出现的位置，没有则返回-1
      - 如果查找的元素为一个空字符''，则会返回0
    
    - `String.prototype.search()`
    
      - 返回子串在字符串中第一次出现的位置，没有则返回-1
      - 参数可以使用正则表达式
    
    - `String.prototype.slice()`
    
      - 截取字符串
    
    - `String.prototype.split()`
    
      - 根据参数中指定的字符串作为分隔符，将一个字符串拆分为一个数组，可以看作数组方法join()的逆向操作
      - 参数可以使用正则表达式
    
      ```javascript
      let str = 'a, b, c'
      let arr1 = String.prototype.split(',')
      let arr2 = String.prototype.split('b')
      let arr3 = String.prototype.split(' ')
      console.log(arr1) // ['a', 'b', 'c']
      console.log(arr2) // ['a, ', ', c']
      console.log(arr3) // ['a', ',', 'b', ',', 'c']
      ```
    
    - `String.prototype.toUpperCase()`/`String.prototype.toLowerCase()`
    
      - 将字符串转换为大写/小写
    
    - `String.prototype.trim()`/`String.prototype.trimStart()`/`String.prototype.trimEnd()`
    
      - 去除前后空格/去除开始的空格/去除结束的空格
    
    - `String.prototype.startsWith()`/`String.prototype.endsWith()`
    
      - 检查字符串是否以指定内容开头/结尾
    
    - `String.prototype.repeat(n)`
    
      - 将一个字符串重复n次，拼接后返回
      
    - `String.prototype.substring(startIndex, endIndex)`
    
      - 从字符串中提取子字符串
    
      - 参数
        - `startIndex`
          - 要提取的子字符串的起始索引位置
          - 默认为0
        - `endIndex`
          - 要提取的子字符串的结束索引位置
          - 默认为字符串的长度
      - 返回一个新的字符串，包含从起始索引到结束索引之间的字符
      - 如果 `startIndex` 大于 `endIndex`，该方法会自动交换这两个参数的值
      - 如果任一参数为负数或 `NaN`，则会将其视为 `0`
      - 非破坏性方法
    
    - `String.prototype.padEnd(targetLength, padString)`
    
      - 将当前字符串的末尾填充到指定的长度
      - 参数
        - `targetLength`
          - 要填充到的目标长度
          - 如果当前字符串小于目标长度，它将被填充到指定的长度
        - `padString`
          - 用于指定填充字符串的内容
          - 默认使用空格字符进行填充
  
- Number

  - Number.prototype.toFixed(digits)
    - 使用定点表示法来格式化一个数值
    - 参数
      - digits
        - 小数点后数字的个数
        - 介于0到20（包括）之间，默认为0
  - Number.prototype.toString(radix)
    - 返回指定数值的字符串表示形式
    - 参数
      - radix
        - 指定要用于转换的进制数（2到36）
        - 默认为10


## 正则表达式(regular expression)

- 正则表达式就是用来定义一个字符串的规则

  - 计算机可以根据正则表达式来判断一个字符串是否符合规则
  - 也可以用来将符合内容的字符串从一段内容中提取出来

- 创建一个正则表达式对象

  ~~~javascript
  // 使用构造函数创建
  let re = new RegExp('正则表达式'，'匹配模式')
  let re = new RegExp(/正则表达式/，'匹配模式')
  
  // 使用字面量语法创建
  let re = /正则表达式/匹配模式
  
  console.log(typeof re) // object
  ~~~
  
- 匹配模式

  - i
    - 单词ignore的缩写，表示匹配时字母不区分大小写
  - g
    - 单词global的缩写，全局匹配，表示匹配所有满足正则表达式的结果
  
- 正则表达式对象的方法

  - re.test(字符串)
    - 检查一个字符串是否符合正则表达式，返回布尔值
  
- 字符串中和正则表达式相关的方法

  - String.prototype.search(/正则表达式/)
  
    - 返回正则表达式匹配的内容在str中第一次出现的位置，没有则返回-1

  - String.prototype.split(/正则表达式/)
  
    - 根据正则表达式匹配的字符来将字符串拆分成数组
  
    ~~~javascript
    let str = 'a1b23c'
    let re = /\d+/
    console.log(String.prototype.split(re)) // ['a', 'b', 'c']
    ~~~
  
  - String.prototype.replace(/正则表达式/, '替换的内容')
  
    - 使用一个新的内容替换字符串中被正则表达式匹配到的第一个内容，将结果返回
    - 使用匹配模式g，则可替换被正则表达式匹配到的所有内容
  
  - String.prototype.match(/正则表达式/)
  
    - 将字符串中被正则表达式匹配到的内容保存到数组中返回
    - 默认匹配第一个符合的内容，使用匹配模式g匹配所有符合的内容
  
- 正则表达式语法

  ~~~Proxyjavascript
  // 检查一个字符串中是否含有a
  console.log(/a/.test('aabc')) // true
  console.log(/a/.test('AAbc')) // false
  
  /*
  - 量词
   - {n} 出现n次
   - {m,n} 出现m-n次
   - {n,} 至少出现n次
   - + 至少出现1次，相当于{1,}
   - ? 出现0-1次，相当于{0,1}
   - * 出现0-多次，相当于{0,}
  - 或
   - |表示或
    - /a|b/ 表示a或b
   - []中的内容都表示或
    - /[ab]/ 表示a或b
    - /[a-z]/ 表示a-z中的任意字母
    - /[^a-z]/ 表示除了a-z中的任意字母以外的内容
  - 边界符
   - /^a/ 表示以a开头
   - /a$/ 表示以a结尾
   - /^a$/ 表示完全匹配'a'
  - 小数点
   - /./ 默认匹配除换行符外的任何单个字符
  - 转义符
   - 在正则表达式中用\作为转义字符
   - 如果正则表达式写在字符串中，由于\在字符串中也是转义字符，因此需要使用两次
   - /\./ 表示匹配小数点，在字符串中的写法为'/\\./'
   - /\w/ 表示任意字母、数字、'_'字符，相当于/[a-zA-Z0-9_]/
   - /\W/ 表示任意除了字母数字_字符以外的字符，相当于/[^a-zA-Z0-9_]/
   - /\d/ 表示任意数字，相当于/[0-9]/
   - /\D/ 表示任意除了数字以外的字符，相当于/[^0-9]/
   - /\s/ 表示空格
   - /\S/ 表示除了空格以外的字符
  */
  
  // 检查一个字符串是否含有连续的4个b
  console.log(/b{4}/.test('abbbbbc')) // true
  // 检查一个字符串是否含有连续的1个a加1-3个b加1个c
  console.log(/ab{1,3}c/.test('abc')) // true
  console.log(/ab{1,3}c/.test('abbbc')) // true
  console.log(/ab{1,3}c/.test('abbbbc')) // false
  // 检查一个字符串是否含有连续的1-3个ab加1个c
  console.log(/(ab){1,3}c/.test('ababc')) // true
  ~~~

## 补充

###### 查看当前函数内的全部代码

console.log(arguments.callee.toString())

