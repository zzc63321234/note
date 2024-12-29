

## 路由

- 前端路由
  - 由vue-router实现

  - 前端路由由key-value键值对组成
    - key：URL
    - value：相应的路由组件



- 如何指定params参数为可传可不传
  - 如果路由要求传递params参数，但是没有传递params参数，则跳转的URL中的路径会丢失
  - 在path中配置路由并带params占位时，在params占位后加上一个问号，代表该params参数可传可不传



- 如果指定了params参数可传可不传，如何处理params参数传递的是空串时的情况

  - 当指定了params参数可传可不传，并传递了一个空串作为params时，跳转的URL中的路径会丢失
  - 如果传递的params是空串时，需要将空串改为undefined




- 如何给路由组件传递props

  - 路由中设置props配置项，三种写法
    1. 值为一个对象，里面的键值对作为props传给组件
    2. 值为true，将params参数作为props传给组件
    3. 值为一个函数，接收$route作为参数，函数返回的对象中的键值对作为props传给组件



- 编程式路由跳转到当前路由（参数不变），多次执行会抛出NavigationDuplicated的警告错误

  - 声明式导航不会由这个问题

  - 报错原因

    - 使用编程式路由导航的方法（比如push方法）时，会返回一个promise

    - push方法的简单理解

      ~~~javascript
       function push(){
          return new Promise((resolve, reject) => {
              ...... 
          })
      }
      ~~~

    - 因为调用push方法时没有传递相应的成功、失败的回调函数，所以跳转失败的情况没有被捕获到，会直接被抛出错误

  - 几种解决方法

    1. 在调用push|replace方法时传入成功和失败的回调作为参数，接收错误信息，或使用catch抓住错误信息

    2. 改写push|replace方法，以push为例

       ~~~javascript
       // /src/router/index.js
       ......
       // 将VueRouter原型对象上的push方法先保存一份
       const originPush = VueRouter.prototype.push
       // 重写push方法
       VueRouter.prototype.push = function(location, resolve, reject){
           if(resolve && reject){
               // 如果已经传入了成功和失败的回调函数，即使用了第一种解决方法，则调用原本的push方法并传入参数即可
               // 但经过保存后的push方法直接调用时的上下文是undefined，在这里调用push方法需将其上下文执向重新指定
               originPush.call(this, location)
           }else{
               // 如果没有同时传入成功和失败的回调函数，则需要在这里将错误信息捕获
               originPush.call(this, location).catch(err => err)
           }
       }
       ~~~

       - 复习补充：call方法与apply方法的区别
         - 相同：都可以调用函数一次，并篡改函数的上下文（this）
         - 区别：call传递的多个参数用逗号隔开，apply传递的多个参数要放在一个数组中



- 路由跳转时如何选择使用声明式导航还是编程式导航
  - 声明式路由导航本身是组件实例，如果出现太多会出现卡顿

## 模块

- 模块开发一般顺序
  1. 拆分静态页面和静态组件
  2. 整理请求API
  3. vuex三连环
  4. 组件获取仓库数据，动态展示数据

## Vuex

- Vuex与多页面问题
  - Vuex中存储的数据可以在同一个Vue实例中共享
  - 如果开启新标签页则是创建了另一个Vue实例
  - 因此开启新标签页后会重新走一遍Vue生命周期，Vuex中的数据会重新生成

## 请求

- axios二次封装

  - 为什么要二次封装axios

    - 为了设置请求、响应拦截器
      - 请求拦截器可以在发送请求之前处理一些事情
      - 响应拦截器可以在当服务器返回数据后处理一些事情

  - `/src/api/request.js`中放置一些二次封装的axios

    ~~~javascript
    // 引入axios
    import axios from 'axios'
    
    // 利用axios对象的方法create，创建一个新的axios实例
    const request = axios.create({
        baseURL:'/api',
        timeout:5000,
    })
    
    // 配置请求拦截器
    request.interceptors.request.use((config) => {
        ......
        return config
    })
    
    // 配置响应拦截器
    request.interceptors.response.use(
        response => {
            ......
            return response.data
        },
        error => {
            ......
            return new Promise(() => {})
        }
    )
    
    // 暴露requests
    export default request
    ~~~

    

- api管理

  - 当项目比较小时，可以在组件的生命周期函数中发送请求

  - 当项目比较大时，api接口需要统一管理

  - 在`/src/api/index.js`中统一管理api

    ~~~javascript
    // 引入二次封装的axios
    import request from './request'
    
    // 封装一个发送axios请求的函数，将axios返回的promise作为函数返回值，将这个函数暴露
    // 为何要放在函数中并返回promise：因为请求不应该在这里发送，而是当暴露的函数在组件中被调用时再发送请求并得到promise
    export const reqBaseCategoryList = () => {
        return request.get('/product/getBaseCategoryList')
    }
    
    ~~~




## 分页器

- 分页器需要的数据
  - 当前页码
  - 总页数
    - 总数据数
    - 每页展示数据数
  - 分页器中间的连续展示页码数
  
- 写分页器思路

  - 模板中将分页器分为几个部分
    - 上一页/下一页
    - 第一页/最后一页
    - 省略号
    - 中间的连续编号（遍历）

  - 设置变量以存储连续页的开始编号和结束编号，根据不同的情况矫正起止编号，因此在computed中设置
  - 根据不同的情况决定模板中的`第一页/最后一页`和`省略号`是否显示