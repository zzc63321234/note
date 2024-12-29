[react-app-rewired 使用 - 简书 (jianshu.com)](https://www.jianshu.com/p/352e33c56d55)

对于 `create-react-app` 创建的项目，如果需要修改 `webpack` 配置，需要执行 `npm run eject` 弹出配置。这个过程是不可逆的。使用 `react-app-rewired` 可以在不弹出配置的情况下修改 `webpack` 配置。

`react-app-rewired@2.x` 版本需要搭配 `customize-cra` 使用。

安装：

~~~bash
$ npm i react-app-rewired customize-cra -D
~~~

在根目录下新建文件 `config-overrides.js` 文件：

~~~javascript
// 这里是使用原生 react-app-rewired 的定义方式
module.exports = function override(config, env) {
  // do stuff with the webpack config...
  return config
}
~~~

修改 `package.json` 文件：

~~~json
{
  // ...
  "scripts": {
    "start": "react-app-rewired start",
    "build": "react-app-rewired build",
    "test": "react-app-rewired test",
    "eject": "react-scripts eject"
  },
  // ...
}
~~~

可以结合 `customize-cra` 快速生成 `override` 函数，例如：

~~~javascript
const { override, addWebpackResolve } = require("customize-cra");
const path = require("path");

module.exports = override(
  addWebpackResolve({
    modules: ["node_modules", path.resolve(__dirname)],
  })
);
~~~

