#### 使用connect-history-api-fallback中间件

- 安装`connect-history-api-fallback`中间件

- ~~~javascript
  const express = require('express')
  const history = require('connect-history-api-fallback')
  const app = express()
  app.use(history()) // 使用该中间件必须在托管静态资源根目录之前
  app.use(express.static(__dirname + '/static'))
  ......
  ~~~