## 文档地址

[Element - 网站快速成型工具](https://element.eleme.cn/#/zh-CN)

## 准备工作

### 安装

- npm安装

  ~~~shell
  npm i element-ui -S
  ~~~

  

### 引入

#### 完整引入

- 引入完整的elementUI

- 在`main.js`中写入

  ~~~javascript
  import Vue from 'vue'
  import App from './App.vue'
  // 引入ElementUI
  import ElementUI from 'element-ui'
  // 引入css文件
  import 'element-ui/lib/theme-chalk/index.css'
  
  // 全局应用ElementUI
  Vue.use(ElementUI)
  
  new Vue({
    el: '#app',
    render: h => h(App)
  })
  ~~~

#### 按需引入

- 只引入需要的组件，以达到减小项目体积的目的

- 步骤

  - 安装`babel-plugin-component`

    ~~~shell
    npm i babel-plugin-component -D
    ~~~

  - 在`babel.config.js`中暴露的对象中添加

    ~~~javascript
    {
      "presets": [["es2015", { "modules": false }]],
      "plugins": [
        [
          "component",
          {
            "libraryName": "element-ui",
            "styleLibraryName": "theme-chalk"
          }
        ]
      ]
    }
    ~~~

  - 在`main.js`中引入需要的组件，如Button和Select

    ~~~javascript
    ......
    
    // 引入Button和Select组件
    import { Button, Select } from 'element-ui'
    
    // 全局应用组件
    Vue.component(Button.name, Button)
    Vue.component(Select.name, Select)
    /* 或写为
     * Vue.use(Button)
     * Vue.use(Select)
     */
    
    ......
    ~~~

- 可能的报错

  - `Error: Cannot find module 'babel-present-es2015`
    - 解决方法
      - 将`babel.config.js`中暴露的对象中`presets`配置项中的`es2015`改为`@babel/preset-env`

## 组件及标签说明

### Menu

- 标签

  - `el-row` 行

  - `el-col` 列

  - `el-menu` 目录外层标签
  - `el-menu-item` 目录项
  - `el-menu-item-group` 目录组外层标签，里面可以包裹`el-menu-item`
  - `el-submenu` 次级目录外层标签



## 一些问题、注意点

### el-menu-item的index报错问题

- index必须为字符串类型
- 如果为number，需转换为字符串

### menu的boder问题

- 场景描述

  - menu放在`el-aside`布局中

  - `el-aside`的宽度小于等于200px
  - 一级菜单的宽度会比二级菜单少1px

- 原因

  - 目录的ul标签默认设置了1px的border
  - 二级菜单的菜单项默认设置了`min-width: 200px`
  - 当`el-aside`的宽度小于等于200px时，ul中的内容会被border吃掉1px宽度，而二级菜单不会小于200px，因此border被二级菜单中的菜单项挡住

- 解决方法

  - 取消ul的border
    - 为`.el-menu`设置*border: none;*
  - 如果需要border，则将对应的border样式设置给`el-aside`



### 为el-card设置样式

- 如果是为`el-card`内部设置布局样式
  - 用sytle属性，在`el-card`标签中设置
  - 不要直接在css中为`.el-card`或`.el-card__body`设置，会被它们的默认样式覆盖掉