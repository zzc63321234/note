`Nest CLI` 是一个命令行界面工具，用于帮助初始化、开发和维护 `Nest` 应用程序。

使用 `npm` 全局安装 `Nest CLI`：

~~~bash
$ npm install -g @nestjs/cli
~~~

安装完成后，可以使用 `nest` 可执行文件直接从命令行调用 `CLi` 命令。

## 命令概述

| 命令       | 别名 | 描述                                                       |
| ---------- | ---- | ---------------------------------------------------------- |
| `new`      | `n`  | 搭建一个新的标准模式应用程序，包含所有需要运行的样板文件。 |
| `generate` | `g`  | 根据原理图生成或修改文件。                                 |
| `build`    |      | 将应用程序或 `workspace` 编译到输出文件夹中。              |
| `start`    |      | 编译并运行应用程序（或 `workspace` 中的默认项目）。        |
| `add`      |      | 导入已打包为 `nest` 的库，运行其安装示意图。               |
| `info`     | `i`  | 显式已安装的 `nest` 包和其它有用的系统信息。               |

可以使用以下结构获取有关单个命令的帮助：

~~~bash
$ nest <command> --help
~~~

## 命令语法

所有的 `nest` 命令都遵循相同的格式：

~~~bash
$ nest <commandOrAlias> <requiredArg> [optionalArg] [options]
~~~

例如：

~~~bash
$ nest new my-test-project --dry-run
~~~

在这里，`new` 是命令，它的别名是 `n`。`my-nest-project` 是必选参数。`--dry-run` 有简写形式 `-d`。以上命令与下面命令等效：

~~~bash
$ nest n my-nest-project -d
~~~

## 创建项目

要在开发模式加创建、构建和运行新的 `Nest` 项目，需要在项目的上级目录运行以下命令：

~~~bash
$ nest new my-nest-project
$ cd my-nest-project
$ npm run start:dev
~~~

## 生成文件

`nest generate` 可以根据原理图生成或修改文件：

~~~bash
$ nest generate <schematic> <name> [options]
$ nest g <schematic> <name> [options]
~~~

可用 `schematics` 如下：

| 名称          | 别名 | 描述                                                         |
| ------------- | ---- | ------------------------------------------------------------ |
| `class`       | `cl` | 生成一个新类。                                               |
| `controller`  | `co` | 生成控制器声明。                                             |
| `service`     | `s`  | 生成服务声明。                                               |
| `module`      | `mo` | 生成模块声明。                                               |
| `filter`      | `f`  | 生成过滤器声明。                                             |
| `guard`       | `gu` | 生成守卫声明。                                               |
| `interceptor` | `in` | 生成一个拦截器声明。                                         |
| `middleware`  | `mi` | 生成中间件声明。                                             |
| `pipe`        | `pi` | 生成管道声明。                                               |
| `decorator`   | `d`  | 生成自定义装饰器。                                           |
| `provider`    | `pr` | 生成提供者声明。                                             |
| `resolver`    | `r`  | 生成解析器声明。                                             |
| `interface`   |      | 生成一个接口。                                               |
| `gateway`     | `ga` | 生成网关声明。                                               |
| `app`         |      | 在 `monorepo` 中生成一个新应用程序(如果它是一个标准结构，则转换为 `monorepo`)。 |
| `library`     |      | 在 `monorepo` 中生成一个新库(如果是标准结构，则转换为 `monorepo` )。 |

可用选项如下：

| 选项                            | 简写 | 描述                                       |
| ------------------------------- | ---- | ------------------------------------------ |
| `--dry-run`                     | `-d` | 报告将要进行的更改，但不真正更改文件系统。 |
| `--flat`                        |      | 不要为元素生成文件夹。                     |
| `--spec`                        |      | 强制spec文件生成（默认）。                 |
| `--no-spec`                     |      | 禁用spec文件生成。                         |
| `--project [project]`           | `-p` | 要在哪个项目中生成内容。                   |
| `--collection [collectionName]` | `-c` | 指定一个第三方的示意图集合在生成文件。     |

## 应用程序运行和构建

`nest start` 用于编译并运行应用程序：

~~~bash
$ nest start [name] [options]
~~~

通常可以使用 `--watch` 参数在监视模式下运行，使得应用程序运行时可以实时重载：

~~~bash
$ nest start --watch
~~~

可以使用 `--dev` 参数在调试模式下运行：

~~~bash
$ nest start --debug
~~~

`nest build` 用于将应用程序或工作区编译到输出文件夹中：

~~~bash
$ nest build [name] [options]
~~~

