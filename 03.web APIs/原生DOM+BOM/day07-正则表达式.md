# 正则表达式

## 什么是正则表达式

- 正则表达式（Regular Expression）是用于匹配字符串中字符组合的模式。在JavaScript中，正则表达式也是一种对象


- 正则表达式在JavaScript中的使用场景：
  - 表单验证，限定表单中可以输入的内容（匹配）
  - 过滤掉表单内容中的一些敏感词（替换）
  - 从字符串中获取我们想要的特定部分（提取）

## 语法

- 定义正则表达式
  - let <u>变量名</u> = /<u>表达式</u>/
  - 其中 /<u>表达式</u>/ 是正则表达式字面量
- 判断是否由符合规则的字符串
  - <u>正则表达式</u>.test(<u>被检测的字符串</u>)
  - 如果正则表达式与指定的字符串匹配，返回true，否则返回false
- 检索（查找）符合规则的字符串
  - <u>正则表达式</u>.exec(<u>被检测的字符串</u>)
  - 如果匹配成功，exec()方法返回一个数组，否则返回null

## 元字符

- 正则表达式中的普通字符与元字符

  - 普通字符

    大多数的字符仅能够描述它们本身，这些字符称作普通字符，也就是说普通字符只能够匹配字符串中与它们相同的字符

  - 元字符（特殊字符）

    一些具有特殊含义的字符，可以极大提高匹配的灵活性

- 元字符

  元字符分类

  - 边界符（表示位置）

    正则表达式中的边界符（位置符）用来提示字符所处的位置，主要有两个字符

    - ^放在开头，表示匹配行首的文本
    - $放在结尾，表示匹配行尾的文本
    - 如果开头和结尾同时有^和$，则表示精确匹配

  - 量词（表示重复次数）

    - *重复零次或以上
    - +重复一次或以上
    - ？重复零次或一次
    - {n}重复n次
    - {n,}重复n次或以上
    - {m,n}重复m到n次

  - 字符类

    - -表示连字符
    
      如[a-z]表示小写字母a-z，[a-zA-Z0-9]表示所有大小写英文字母和数字
    
    - ^表示取反符
    
      [^a-z]表示除了小写字母以外的字符
    
    - .匹配除换行符之外的任何单个字符
    
    - 预定义
    
      | 预定义 | 说明                                     |
      | ------ | ---------------------------------------- |
      | \d     | 匹配0-9之间的任一数字                    |
      | \D     | 匹配所有0-9以外的字符                    |
      | \w     | 匹配任意的字母、数字和下划线             |
      | \W     | 匹配所有字母、数字和下划线以外的单个字符 |
      | \s     | 匹配空格，包括换行符、制表符、空格符等   |
      | \S     | 匹配非空格的字符                         |

## 修饰符

- 修饰符约束正则执行的某些细节行为，如是否区分大小写、是否支持多行匹配等

- 语法：

  /<u>表达式</u>/<u>修饰符</u>

- 修饰符i是单词ignore的缩写，正则匹配时字母不区分大小写

- 修饰符g时单词global的缩写，匹配所有满足正则表达式的结果

- 替换匹配内容

  - 语法

    <u>字符串</u>.replace(<u>正则表达式</u>, '<u>替换的文本</u>')