- 在对axios进行二次封装时，可使用nprogress进度条插件，用以显示请求处理的进度

- 使用

  - 安装并引入nprogress

    ~~~javascript
    // 引入nprogress
    import nprogress from 'nprogress'
    
    // 引入进度条样式
    import 'nprogress/nprogress.css'
    ~~~

  - 在`nprogress/nprogress.css`中可修改进度条样式

    - `#nprogress .bar` 更改进度条相关样式

  - 使用进度条

    - `nprogress.start()`进度条开始
    - `nprogress.done()`进度条结束
    - 可以让这两个方法分别在axios的请求拦截器和响应拦截器中执行