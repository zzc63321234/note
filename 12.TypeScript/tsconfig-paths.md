如果使用 `ts-node`、`ts-node-dev` 等运行 `node` 项目时，`tsconfig.json` 中的路径相关配置并不会被这些运行工具识别，需使用 `tsconfig-paths`。

`tsconfig.json` 中负责路径的配置有：

~~~json
{
  "baseUrl": "./",
  "paths": {
    "~/*": ["./src/*"]
  }
}
~~~

使用 `tsconfig-paths` 将 `tsconfig.json` 中的路径相关配置同步给运行工具。

安装：

~~~bash
$ npm i tsconfig-paths -D
~~~

使用：

~~~bash
$ ts-node-dev -r tsconfig-paths/register main.ts ---respawn
~~~

也可以指定 `tsconfig.json` 文件：

~~~bash
$ ts-node --project ./tsconfig.dev.json -r tsconfig-paths/register main.ts
~~~

注意命令中的顺序，要执行的文件必须在参数 -`r tsconfig-paths/register` 之后。