- mockJS可以在前端中模拟一些后台数据

- 使用方法

  1. 在`src/mock`中创建json文件，准备json数据

  2. 将mock数据中需要用到的资源（图片等）放置在`public`目录下（因为public文件夹在打包的时候会把相应资源原封不动地打包到`dist`文件夹中）

  3. 创建`src/mock/mockServe.js`

     ~~~javascript
     // 引入mockjs模块
     import Mock from 'mockjs'
     
     // 引入json格式的数据
     import banner from './banner.json'
     import floor from './floor.json'
     ......
     
     // 配置模拟的后端路由
     // mock方法的两个参数，1.请求的url，2.响应的数据
     Mock.mock('/mock/banner', {code: 200, data: banner})
     Mock.mock('/mock/floor', {code: 200, data: floor})
     ~~~

  4. 在`src/main.js`中引入`src/mock/mockServe.js`

     ~~~javascript
     // mopckServe.js中并没有暴露内容，这里只需将js文件引入，让js代码执行一遍即可
     import '@/mock/mopckServe.js'
     ~~~

  5. 创建`src/api/mockRequest.js`，封装一个用于请求mock数据的axios请求，将axios配置项中的`baseURL`指定为`'/mock`