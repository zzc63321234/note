## 简介

- `Axios` 是一个基于 `promise` 的网络请求库
- 可以运行在浏览器和 `node.js` 中
  - 在浏览器中使用 `XMLHttpRequests`
  - 在 `node.js` 中使用`node.js`  原生 `http` 模块

## 引入方式

- `CDN` 引入

  ~~~html
  <script src="https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js"></script>
  ~~~

  ~~~html
  <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
  ~~~

- `npm` 安装

  ~~~bash
  npm install axios
  ~~~

- 为了在 `CommonJS` 中使用 `require()` 导入时获得 `TypeScript` 类型推断（智能感知/自动完成），请使用以下方法

  ~~~typescript
  const axios = require('axios').default;
  
  // axios.<method> 能够提供自动完成和参数类型推断功能
  ~~~

## 用例

- 发起一个 `GET` 请求

  ~~~javascript
  const axios = require('axios');
  
  // 向给定ID的用户发起请求
  axios.get('/user?ID=12345')
    .then(function (response) {
      // 处理成功情况
      console.log(response);
    })
    .catch(function (error) {
      // 处理错误情况
      console.log(error);
    })
    .finally(function () {
      // 总是会执行
    });
  
  // 上述请求也可以按以下方式完成（可选）
  axios.get('/user', {
      params: {
        ID: 12345
      }
    })
    .then(function (response) {
      console.log(response);
    })
    .catch(function (error) {
      console.log(error);
    })
    .finally(function () {
      // 总是会执行
    });  
  
  // 支持async/await用法
  async function getUser() {
    try {
      const response = await axios.get('/user?ID=12345');
      console.log(response);
    } catch (error) {
      console.error(error);
    }
  }
  ~~~

- 发起一个 `POST` 请求

  ~~~javascript
  axios.post('/user', {
      firstName: 'Fred',
      lastName: 'Flintstone'
    })
    .then(function (response) {
      console.log(response);
    })
    .catch(function (error) {
      console.log(error);
    });
  ~~~

- 发起多个并发请求

  ~~~javascript
  function getUserAccount() {
    return axios.get('/user/12345');
  }
  
  function getUserPermissions() {
    return axios.get('/user/12345/permissions');
  }
  
  const [acct, perm] = await Promise.all([getUserAccount(), getUserPermissions()]);
  
  // OR
  
  Promise.all([getUserAccount(), getUserPermissions()])
    .then(function ([acct, perm]) {
      // ...
    });
  ~~~

- 将 `HTML Form` 转换成 `JSON` 格式的请求体进行请求

  ```javascript
  const {data} = await axios.post('/user', document.querySelector('#my-form'), {
    headers: {
      'Content-Type': 'application/json'
    }
  })
  ```

- 将 `JS` 对象转换为 `multipart/form-data` 格式的请求体进行请求

  ~~~javascript
  const {data} = await axios.post('https://httpbin.org/post', {
      firstName: 'Fred',
      lastName: 'Flintstone',
      orders: [1, 2, 3],
      photo: document.querySelector('#fileInput').files
    }, {
      headers: {
        'Content-Type': 'multipart/form-data'
      }
    }
  )
  ~~~

- 将 `JS` 对象转换为 `URL encoded` 格式的请求体进行请求

  ~~~javascript
  const {data} = await axios.post('https://httpbin.org/post', {
      firstName: 'Fred',
      lastName: 'Flintstone',
      orders: [1, 2, 3]
    }, {
      headers: {
        'Content-Type': 'application/x-www-form-urlencoded'
      }
  })
  ~~~

## `API`

### 核心 `API`

- `axios(config)` / `axios(url[, config])`

  - 发起请求，示例

    ~~~javascript
    // 发起一个post请求
    axios({
      method: 'post',
      url: '/user/12345',
      data: {
        firstName: 'Fred',
        lastName: 'Flintstone'
      }
    });
    ~~~

    ~~~javascript
    // 在 node.js 用GET请求获取远程图片
    axios({
      method: 'get',
      url: 'http://bit.ly/2mTM3nY',
      responseType: 'stream'
    })
      .then(function (response) {
        response.data.pipe(fs.createWriteStream('ada_lovelace.jpg'))
      });
    ~~~

  - 发送 `GET` 时，可以使用默认配置，这样只需传入 `url`

    ~~~javascript
    // 发起一个 GET 请求 (默认请求方式)
    axios('/user/12345');
    ~~~

### 请求方式别名

- 为了方便起见，已经为所有支持的请求方法提供了别名。
  - `axios.request(config)`
  - `axios.get(url[, config])`
  - `axios.delete(url[, config])`
  - `axios.head(url[, config])`
  - `axios.options(url[, config])`
  - `axios.post(url[, data[, config]])`
  - `axios.put(url[, data[, config]])`
  - `axios.patch(url[, data[, config]])`
  - `axios.postForm(url[, data[, config]])`
  - `axios.putForm(url[, data[, config]])`
  - `axios.patchForm(url[, data[, config]])`

### `Axios` 实例

- 除了直接获取到的 `axios` 对象本身，还可以自定义配置新建一个 `axios` 实例

- `axios.create([config])`

- 示例

  ~~~javascript
  const instance = axios.create({
    baseURL: 'https://some-domain.com/api/',
    timeout: 1000,
    headers: {'X-Custom-Header': 'foobar'}
  });
  ~~~

- 以下是可用的实例方法。指定的配置将与实例本身的配置合并

  - `instance.request(config)`
  - `instance.get(url[, config])`
  - `instance.delete(url[, config])`
  - `instance.head(url[, config])`
  - `instance.options(url[, config])`
  - `instance.post(url[, data[, config]])`
  - `instance.put(url[, data[, config]])`
  - `instance.patch(url[, data[, config]])`
  - `instance.getUri([config])`

### 请求配置

- 创建请求的配置中，只有 `url` 是必需的

- 如果没有指定 `method`，请求将默认使用 `GET` 方法

- 可用配置项

  ~~~javascript
  {
    // `url` 是用于请求的服务器 URL
    url: '/user',
  
    // `method` 是创建请求时使用的方法
    method: 'get', // 默认值
  
    // `baseURL` 将自动加在 `url` 前面，除非 `url` 是一个绝对 URL。
    // 它可以通过设置一个 `baseURL` 便于为 axios 实例的方法传递相对 URL
    baseURL: 'https://some-domain.com/api/',
  
    // `transformRequest` 允许在向服务器发送前，修改请求数据
    // 它只能用于 'PUT', 'POST' 和 'PATCH' 这几个请求方法
    // 数组中最后一个函数必须返回一个字符串， 一个Buffer实例，ArrayBuffer，FormData，或 Stream
    // 你可以修改请求头。
    transformRequest: [function (data, headers) {
      // 对发送的 data 进行任意转换处理
  
      return data;
    }],
  
    // `transformResponse` 在传递给 then/catch 前，允许修改响应数据
    transformResponse: [function (data) {
      // 对接收的 data 进行任意转换处理
  
      return data;
    }],
  
    // 自定义请求头
    headers: {'X-Requested-With': 'XMLHttpRequest'},
  
    // `params` 是与请求一起发送的 URL 参数
    // 必须是一个简单对象或 URLSearchParams 对象
    params: {
      ID: 12345
    },
  
    // `paramsSerializer`是可选方法，主要用于序列化`params`
    // (e.g. https://www.npmjs.com/package/qs, http://api.jquery.com/jquery.param/)
    paramsSerializer: function (params) {
      return Qs.stringify(params, {arrayFormat: 'brackets'})
    },
  
    // `data` 是作为请求体被发送的数据
    // 仅适用 'PUT', 'POST', 'DELETE 和 'PATCH' 请求方法
    // 在没有设置 `transformRequest` 时，则必须是以下类型之一:
    // - string, plain object, ArrayBuffer, ArrayBufferView, URLSearchParams
    // - 浏览器专属: FormData, File, Blob
    // - Node 专属: Stream, Buffer
    data: {
      firstName: 'Fred'
    },
    
    // 发送请求体数据的可选语法
    // 请求方式 post
    // 只有 value 会被发送，key 则不会
    data: 'Country=Brasil&City=Belo Horizonte',
  
    // `timeout` 指定请求超时的毫秒数。
    // 如果请求时间超过 `timeout` 的值，则请求会被中断
    timeout: 1000, // 默认值是 `0` (永不超时)
  
    // `withCredentials` 表示跨域请求时是否需要使用凭证
    withCredentials: false, // default
  
    // `adapter` 允许自定义处理请求，这使测试更加容易。
    // 返回一个 promise 并提供一个有效的响应 （参见 lib/adapters/README.md）。
    adapter: function (config) {
      /* ... */
    },
  
    // `auth` HTTP Basic Auth
    auth: {
      username: 'janedoe',
      password: 's00pers3cret'
    },
  
    // `responseType` 表示浏览器将要响应的数据类型
    // 选项包括: 'arraybuffer', 'document', 'json', 'text', 'stream'
    // 浏览器专属：'blob'
    responseType: 'json', // 默认值
  
    // `responseEncoding` 表示用于解码响应的编码 (Node.js 专属)
    // 注意：忽略 `responseType` 的值为 'stream'，或者是客户端请求
    // Note: Ignored for `responseType` of 'stream' or client-side requests
    responseEncoding: 'utf8', // 默认值
  
    // `xsrfCookieName` 是 xsrf token 的值，被用作 cookie 的名称
    xsrfCookieName: 'XSRF-TOKEN', // 默认值
  
    // `xsrfHeaderName` 是带有 xsrf token 值的http 请求头名称
    xsrfHeaderName: 'X-XSRF-TOKEN', // 默认值
  
    // `onUploadProgress` 允许为上传处理进度事件
    // 浏览器专属
    onUploadProgress: function (progressEvent) {
      // 处理原生进度事件
    },
  
    // `onDownloadProgress` 允许为下载处理进度事件
    // 浏览器专属
    onDownloadProgress: function (progressEvent) {
      // 处理原生进度事件
    },
  
    // `maxContentLength` 定义了node.js中允许的HTTP响应内容的最大字节数
    maxContentLength: 2000,
  
    // `maxBodyLength`（仅Node）定义允许的http请求内容的最大字节数
    maxBodyLength: 2000,
  
    // `validateStatus` 定义了对于给定的 HTTP状态码是 resolve 还是 reject promise。
    // 如果 `validateStatus` 返回 `true` (或者设置为 `null` 或 `undefined`)，
    // 则promise 将会 resolved，否则是 rejected。
    validateStatus: function (status) {
      return status >= 200 && status < 300; // 默认值
    },
  
    // `maxRedirects` 定义了在node.js中要遵循的最大重定向数。
    // 如果设置为0，则不会进行重定向
    maxRedirects: 5, // 默认值
  
    // `socketPath` 定义了在node.js中使用的UNIX套接字。
    // e.g. '/var/run/docker.sock' 发送请求到 docker 守护进程。
    // 只能指定 `socketPath` 或 `proxy` 。
    // 若都指定，这使用 `socketPath` 。
    socketPath: null, // default
  
    // `httpAgent` and `httpsAgent` define a custom agent to be used when performing http
    // and https requests, respectively, in node.js. This allows options to be added like
    // `keepAlive` that are not enabled by default.
    httpAgent: new http.Agent({ keepAlive: true }),
    httpsAgent: new https.Agent({ keepAlive: true }),
  
    // `proxy` 定义了代理服务器的主机名，端口和协议。
    // 您可以使用常规的`http_proxy` 和 `https_proxy` 环境变量。
    // 使用 `false` 可以禁用代理功能，同时环境变量也会被忽略。
    // `auth`表示应使用HTTP Basic auth连接到代理，并且提供凭据。
    // 这将设置一个 `Proxy-Authorization` 请求头，它会覆盖 `headers` 中已存在的自定义 `Proxy-Authorization` 请求头。
    // 如果代理服务器使用 HTTPS，则必须设置 protocol 为`https`
    proxy: {
      protocol: 'https',
      host: '127.0.0.1',
      port: 9000,
      auth: {
        username: 'mikeymike',
        password: 'rapunz3l'
      }
    },
  
    // see https://axios-http.com/zh/docs/cancellation
    cancelToken: new CancelToken(function (cancel) {
    }),
  
    // `decompress` indicates whether or not the response body should be decompressed 
    // automatically. If set to `true` will also remove the 'content-encoding' header 
    // from the responses objects of all decompressed responses
    // - Node only (XHR cannot turn off decompression)
    decompress: true // 默认值
  
  }
  ~~~


### 响应结构

- 一个请求的响应包含以下信息

  ~~~javascript
  {
    // `data` 由服务器提供的响应
    data: {},
  
    // `status` 来自服务器响应的 HTTP 状态码
    status: 200,
  
    // `statusText` 来自服务器响应的 HTTP 状态信息
    statusText: 'OK',
  
    // `headers` 是服务器响应头
    // 所有的 header 名称都是小写，而且可以使用方括号语法访问
    // 例如: `response.headers['content-type']`
    headers: {},
  
    // `config` 是 `axios` 请求的配置信息
    config: {},
  
    // `request` 是生成此响应的请求
    // 在node.js中它是最后一个ClientRequest实例 (in redirects)，
    // 在浏览器中则是 XMLHttpRequest 实例
    request: {}
  }
  ~~~

### 默认配置

- 全局 `axios` 的默认值配置

  ~~~javascript
  axios.defaults.baseURL = 'https://api.example.com';
  axios.defaults.headers.common['Authorization'] = AUTH_TOKEN;
  axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded';
  ~~~

- 自定义实例的默认值配置

  ~~~javascript
  // 创建实例时配置默认值
  const instance = axios.create({
    baseURL: 'https://api.example.com'
  });
  
  // 创建实例后修改默认值
  instance.defaults.headers.common['Authorization'] = AUTH_TOKEN;
  ~~~

- 配置的优先级

  - 配置将按优先级进行合并

  - 顺序是（优先级后高于前）

    1. 在 `lib/defaults.js` 中找到库默认值
    2. 实例的 `defaults` 属性
    3. 请求的 `config` 参数

  - 示例

    ~~~javascript
    // 使用库提供的默认配置创建实例
    // 此时超时配置的默认值是 `0`
    const instance = axios.create();
    
    // 重写库的超时默认值
    // 现在，所有使用此实例的请求都将等待2.5秒，然后才会超时
    instance.defaults.timeout = 2500;
    
    // 重写此请求的超时时间，因为该请求需要很长时间
    instance.get('/longRequest', {
      timeout: 5000
    });

### 拦截器

- 拦截器可以在请求要被发起前或响应要被被 `then` 或 `catch` 处理前拦截它们

  ~~~javascript
  // 添加请求拦截器
  axios.interceptors.request.use(function (config) {
      // 在发送请求之前做些什么
      return config;
    }, function (error) {
      // 对请求错误做些什么
      // 如果希望请求失败之后还能继续链式调用，应该返回一个被拒绝的 Promise，这样可以在后续的 .catch 中处理
      // 如果返回一个非 Promise 值，那么就会被当作响应数据，即使请求失败了，axios 也会将其视为成功  
      return Promise.reject(error);
    });
  
  // 添加响应拦截器
  axios.interceptors.response.use(function (response) {
      // 2xx 范围内的状态码都会触发该函数。
      // 对响应数据做点什么
      return response;
    }, function (error) {
      // 超出 2xx 范围的状态码都会触发该函数。
      // 对响应错误做点什么
      return Promise.reject(error);
    });
  ~~~

- 如果稍后需要移除拦截器，可以这样

  ~~~javascript
  const myInterceptor = axios.interceptors.request.use(function () {/*...*/});
  axios.interceptors.request.eject(myInterceptor);
  ~~~

- 可以给自定义的 `axios` 实例添加拦截器

  ~~~javascript
  const instance = axios.create();
  instance.interceptors.request.use(function () {/*...*/});
  ~~~

### 错误处理

- 示例

  ~~~javascript
  axios.get('/user/12345')
    .catch(function (error) {
      if (error.response) {
        // 请求成功发出且服务器也响应了状态码，但状态代码超出了 2xx 的范围
        console.log(error.response.data);
        console.log(error.response.status);
        console.log(error.response.headers);
      } else if (error.request) {
        // 请求已经成功发起，但没有收到响应
        // `error.request` 在浏览器中是 XMLHttpRequest 的实例，
        // 而在node.js中是 http.ClientRequest 的实例
        console.log(error.request);
      } else {
        // 发送请求时出了点问题
        console.log('Error', error.message);
      }
      console.log(error.config);
    });
  ~~~

- 使用 `validateStatus` 配置选项，可以自定义什么范围外的 `HTTP code` 会抛出错误

  ~~~javascript
  axios.get('/user/12345', {
    validateStatus: function (status) {
      return status < 500; // 处理状态码小于 500 的情况不会抛出错误
    }
  })
  ~~~

- 使用 `toJSON` 可以获取更多关于HTTP错误的信息。

  ```javascript
  axios.get('/user/12345')
    .catch(function (error) {
      console.log(error.toJSON());
    });
  ```

### 取消请求

#### `AbortController`

- 从 `v0.22.0` 开始，`Axios` 支持以 `Web API` 方式 —— `AbortController` 取消请求

  ~~~javascript
  const controller = new AbortController();
  
  axios.get('/foo/bar', {
     signal: controller.signal
  }).then(function(response) {
     //...
  });
  // 取消请求
  controller.abort()
  ~~~

#### `CancelToken`（弃用）

- `CancelToken` 取消请求的方式从`v0.22.0` 开始不再推荐使用

## 补充

### `AbortController`

- `AbortController` 是一个 `Web` 提供的 `API`，表示一个控制器对象，允许根据需要中止一个或多个 `Web` 请求

- `AbortController` 可以作为构造函数创建一个新的 `AbortController` 实例

  ~~~javascript
  const controller = new AbortController()
  ~~~

- 实例属性与方法

  - `signal`
    - 是一个只读属性
    - 能获取到一个 `AbortSignal` 实例对象，该对象可以根据需要处理 `DOM` 请求通信，既可以建立通信，也可以终止通信
  - `abort()`
    - 调用 `abort` 方法后能中止一个尚未完成的网络请求
    - 这能够中止 `fetch` 请求及任何响应体的消费和流

- 使用示例

  ~~~javascript
  let controller;
  const url = "video.mp4";
  
  const downloadBtn = document.querySelector(".download");
  const abortBtn = document.querySelector(".abort");
  
  downloadBtn.addEventListener("click", fetchVideo);
  
  abortBtn.addEventListener("click", () => {
    if (controller) {
      controller.abort();
      console.log("中止下载");
    }
  });
  
  function fetchVideo() {
    controller = new AbortController();
    const signal = controller.signal;
    fetch(url, { signal })
      .then((response) => {
        console.log("下载完成", response);
      })
      .catch((err) => {
        console.error(`下载错误：${err.message}`);
      });
  }
  // 当 abort() 被调用时，这个 fetch() promise 将 reject 一个名为 AbortError 的 DOMException。
  ~~~

#### `AbortSignal`

- `AbortSignal` 是一个 `Web` 提供的 `API`，表示一个信号对象（`signal object`），允许通过 `AbortController` 对象与 `DOM` 请求进行通信并在需要时将其中止
- 实例属性
  - `signal.aborted`
    - 一个布尔值，表示与之通信的请求是否被终止（`true`）或未终止（`false`）
  - `signal.reason`
    - 一旦信号被终止，提供一个使用 `JS` 值表示的终止原因
  - `signal.onabort`
    - 可将一个回调函数赋值给 `onabort`
    - 当事件关联的请求被终止时（例如调用 `AbortController.abort()`），触发 `AbortSignal` 实例的 `abort` 事件，调用该回调函数
- 实例方法
  - `signal.throwIfAborted()`
    - 继承自 `AbortSignal` 接口的父接口 `EventTarget` 的方法
    - 如果信号已经被终止，则抛出信号终止的 `reason`
- 静态方法
  - `AbortSignal.abort([reason])`
    - 返回一个已经被设置为终止的 `AbortSignal` 实例，该 `AbortSignal` 实例的 `aborted` 属性为 `true`，`reason` 属性是传入的 `reason` 参数的值或默认值
    - 不会触发 `abort` 事件
    - 可传入一个任意的 `JS` 值作为 `reason`，如果不指定，则默认设置为 `AbortError` [`DOMException`](https://developer.mozilla.org/zh-CN/docs/Web/API/DOMException)
  - `AbortSignal.timeout(time)`
    - 返回一个在指定时间后将自动终止的 `AbortSignal` 实例
    - 传入的 `time` 为指定的时间，单位为毫秒

#### `AbortSignal` 与 `AbortController` 的关系

- `AbortSignal` 是一个类，但无法直接使用 `new` 关键字调用进行实例化
- 获取 `AbortSignal` 的方式有
  - 调用 `AbortSignal` 的静态方法 `abort` 和 `timeout`
  - `AbortController` 实例的 `signal` 属性
- `AbortSignal` 实例的 `abort` 事件
  - 如果使用 `AbortSignal.abort()` 返回一个已经被设置为终止的 `AbortSignal` 实例，该实例创建出来时就是终止状态，因此不会触发它的 `abort` 事件
  - 使用 `AbortSignal.timeout()` 创建出来的 `AbortSignal` 实例转变为终止状态时，会发出该 `AbortSignal` 实例的 `abort` 事件
  - 以及 `AbortController` 实例调用 `abort()` 时，会触发它的 `signal` 对象（ `AbortSignal` 实例）的 `abort` 事件