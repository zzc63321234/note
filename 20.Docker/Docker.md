`Docker` 是一种开源的容器化技术，它可以将应用程序中的所有文件封装成一个镜像，这样，应用程序可以在任何支持 `Docker` 的环境中允许，且能保证一致的运行效果。

`Docker` 提供了 `Docker Hub` 镜像仓库，可以把本地镜像 `push` 到仓库或者从仓库 `pull` 镜像到本地。

`Docker` 提供了 `Docker Desktop` 桌面应用程序，允许在桌面端方便地使用 `Docker` 和允许本地容器。

## 名词解释

### 宿主机（`Host`）

宿主机是指运行 `Docker` 引擎的物理机或虚拟机，它为 `Docker` 容器提供了运行环境。

### 容器（`Containers`）

`Container` 是 `Image` 运行时的实体。`Container` 可以被创建、启动、停止、删除、暂停等。每个 `Container` 都是相互隔离的。可以把容器看作是一个简易版的 `Linux` 环境和运行在其中的应用程序。

### 镜像（`Images`）

`Image` 是用来构建和运行 `Docker` 容器的模板，提供了容器运行时需要用到的文件和参数配置。

### 数据卷（`Volumes`）

`Volume` 是一种用于持久化存储 `Docker` 容器生成和使用的数据的机制。它们是 `Docker` 管理的特殊目录，位于宿主机上。其主要目的是持久化容器中的数据，以在容器间共享或防止数据丢失，其主要特性有：

- `volumes` 可以在容器间共享和重用。
- 对 `volumes` 的写入操作，不会对镜像有任何影响。
- 
- 容器被删除不会影响到 `volumes`。

## 拉取和运行

例如在 `Docker Desktop` 中运行一个 `nginx`。

### 拉取镜像

在 `Docker Desktop` 界面顶栏中搜索 `nginx`，在搜索结果中找到 `nginx`，点击 `pull`，即可拉取一个 `nginx` 镜像。

对应的命令为：

~~~bash
$ docker pull nginx:latest
~~~

这里的 `latest` 指的是仓库中的标签。

### 创建容器并运行

在 `Docker Desktop` 的 `Images` 标签中可以看到本地已拉取的镜像。点击对应镜像的 `Run` 图标，会弹出一个 `Run a new container` 的表单，表单内容分别是：

- `Container name`：容器名称，如果不指定则会随机指定。
- `Ports`：映射端口，如 `nginx` 服务在容器中的端口是 `80`，将其映射到指定的宿主机端口上。如果指定映射端口为 `0`，则每次启动容器时都会随机指定一个端口。
- `Volumes`：分别指定 `Host path`（数据卷在宿主机上的路径）和 `Container path`（容器中要挂载数据卷的路径）。`Host path` 可以是宿主机上的目录，也可以是一个在 `Docker`  中创建的 `volume` 的名称。
- `Environment variables`：指定环境变量。

点击 `Run` 按钮即提交表单，创建容器并运行。

对应的命令为：

~~~bash
$ docker run --name nginx-test -p 8000:80 -v C:/docker/tmp/nginx:/usr/share/nginx/html -e KEY1=VALUE1 -d --restart always nginx:latest
~~~

含义为：

- `--name`：容器名称。

- `-p`：指定端口，`-p 8000:80` 代表将容器中的 `80` 端口映射到宿主机的 `8000` 端口。

- `-v`：映射挂载，格式是 `HostPath:ContainerPath`。

- `-e`：指定环境变量

- `-d`：意为 `detached`，从容器终端中脱离，即后台运行，不会占用命令行。后台运行时可以使用 `docker ps` 命令查看当前正在运行的容器。

- `--restart`：重启策略。支持的值有：

  - `no`：默认值，不会自动重启。

  - `on-failure[:max-retries]`：

    仅当容器因错误退出（即退出状态非零）时重启容器。

    可以通过 `:max-retries` 来限制重启的次数，比如 `on-failure:3` 表示最多重启三次。

  - `always`：

    容器停止后总是尝试重启。

    这意味着即使容器中的应用程序成功执行完毕且没有遇到任何错误的情况下正常退出，也会被重启。

    如果容器是手动停止的，则只有在 `Docker` 守护进程重启或者容器手动被重新启动时才会重启。

  - `unless-stopped`：

    与 `always` 类似，但如果容器被手动停止，则即使在 `Docker` 守护进程重启后也不会自动重启容器。

该命令会返回一个容器的 `hash` 值，在 `Docker Desktop` 的 `Containers` 列表中也能看到。

实际上上面示例中使用 `-v` 参数挂载的并不是 `volume`，而是挂载点。它们在设计上对应的命令参数分别是 `-v` 和 `--mount`，这两个参数可以通用，但是实际上这是两种不同的行为。

### 停止和运行容器

使用 `docker stop` 命令停止运行中的容器：

~~~bash
$ docker stop nginx-test
~~~

使用 `docker start` 命令启动处于停止状态的容器：

~~~bash
$ docker start nginx-test
~~~

## 查看容器和镜像列表

使用 `docker ps` 命令即为查看 `process status`，默认会返回一个运行中的容器的列表。如果需要查看全部的容器，则使用 `docker ps -a`。

使用 `docker images` 命令可以查看本地的镜像列表。

## 操作容器

`Docker Desktop` 容器面板中的标签可以用来操作容器。

`Exec` 标签对应面板中可以使用当前运行中的容器中的命令行执行命令，对应命令为 `docker exec`：

> 在客户端命令行中执行以下命令：
>
> ~~~bash
> $ docker exec -it 1d0d3e7f65a1cc6ba3eee8fbca0328d8821d40e665f17f309c69025832b3d41d bash
> ~~~
>
> 含义为：
>
> - -`it` 是 `-i` 和 `-t` 两个参数的组合。
>
>   `-i`（-`interactive`）表示以交互模式运行容器，`Docker` 会将宿主机命令行中的输入流（例如键盘输入）连接到运行的容器中。
>
>   `-t`（`-tty`）表示为容器分配一个伪终端（`pseudo-TTY`），使得容器的输出看起来像是在一个终端中运行的。
>
> - 一长串字符是对应的容器的 `hash ID`。
> - `bash` 表示使用容器中的 `bash` 终端，也可以用路径表示为 `/bin/bash`
>
> 如果要退出 `TTY` 返回宿主机终端，可以在 `TTY` 中使用 `exit` 命令，或者使用 `Ctrl+P` 和 `Ctrl+Q` 组合键来从容器中“脱离”（`detach`）。

`Logs` 标签对应面板中可以查看日志，对应 `docker logs` 命令：

~~~bash
$ docker logs 1d0d3e7f65a1cc6ba3eee8fbca0328d8821d40e665f17f309c69025832b3d41d
~~~

`Inspect` 标签对应面板中可以查看容器的详情，对应 `docker inspect` 命令，返回 `JSON` 格式的详情信息：

~~~bash
$ docker inspect 1d0d3e7f65a1cc6ba3eee8fbca0328d8821d40e665f17f309c69025832b3d41d
~~~

## `Dockerfile`

`Dockerfile` 是用来构建 `Docker` 镜像的文本文件，其中包含了一系列的指令和配置，用于定义容器镜像的构建规则和运行环境。

示例：

>~~~dockerfile
>FROM node:latest
>
>WORKDIR /app
>
>COPY . .
>
>RUN npm config set registry https://registry.npmmirror.com/
>
>RUN npm install -g http-server
>
>EXPOSE 8080
>
>CMD ["http-server", "-p", "8080"]
>
>VOLUME /test
>~~~
>
>这些指令的含义如下：
>
>- `From`：基于一个基础镜像进行修改。
>- `WORKDIR`：指定镜像环境中当前的工作目录。
>- `COPY`：将镜像外指定路径的内容赋值到镜像内的指定路径中。
>- `EXPOSE`：声明镜像生成的容器运行时会暴露出的网络端口。
>- `RUN`：构建镜像时执行的命令。
>- `CMD`：镜像生成的容器启动时执行的命令。
>- `VOLUME`：在镜像中创建一个挂载目录。如果使用该指令指定了挂载目录，那么即使在创建容器时没有指定挂载目录，也会在宿主机上创建一个临时目录作为挂载目录，使得保证该目录下的数据移动会被持久化。
>
>将这个文件命名为 `Dockerfile` 保存，并在同级目录下添加一个 `index.html`，在当前目录下运行以下命令以构建 `Docker` 镜像：
>
>~~~bash
>$ docker build -t test:tag .
>~~~
>
>这里的 `test` 是镜像名，`tag` 是镜像的标签名。命令最后的 `.` 表示 `Dockerfile` 所在的上下文目录是当前目录。
>
>另外，也可以指定 `Dockerfile` 也可以使用其它命名，并在 `build` 时通过 `-f` 参数指定：
>
>~~~bash
>$ docker build -t test:tag -f filename .
>~~~
>
>还可以定义 `.dockerignore` 文件声明哪些文件不发送到镜像中：
>
>~~~.dockerugnore
># 这是注释语法
>*.md
>!README.md
>node_modules/
>[a-c].txt
>.git/
>.DS_Store
>.vscode/
>.dockerignore
>.eslintignore
>.eslintrc
>.prettierrc
>.prettierignore
>~~~

### 优化手段

#### 使用 `Alpine`

`Alpine` 是一个面向安全的轻型 `Linux` 发行版。`Docker` 官方推荐使用 `Alpine` 作为基础镜像环境。`Alpine` 裁剪了很多不必要的 `linux` 功能，使得镜像体积大幅减小。在 `docker hub` 中拉取镜像时，也可以选择 `alpine` 版本。

#### 利用分层存储

使用 `Dockerfile` 构建镜像时，会使用分层存储机制，使得每执行 `Dockerfile` 中的一条命令，就会创建一个新的层。每一层都会再上一层的基础上执行对于命令后生成。每一层都是只读的，当容器启动时，`Docker` 会在这些只读层的顶部添加一个可写层，所有的变更都会发生再这个可写层中。

这些层可以被缓存，用于以后构建镜像时被复用：

- 当使用 `docker builder` 命令构建镜像时，`Docker` 会缓存每一层已构建的镜像层。
- 下次执行 `docker builder` 命令构建镜像时，对于每一条命令，`Docker` 首先会检查缓存中是否存在一个与之相匹配的层：
  - 如果存在，且指令的上下文（即该指令和它依赖的文件）没有变化，`Docker` 就会复用这个层。
  - 如果 `Dockerfile` 的某一行命令无法匹配上缓存的层，或该命令的上下文发生了变化，那么 `Docker` 将会从这一行命令开始重新构建。

利用好分层存储的特性，可以加快重复构建的过程。例如以下 `Dockerfile`：

> ~~~dockerfile
> FROM node:18-alpine3.14
> 
> WORKDIR /app
> 
> COPY package.json .
> 
> RUN npm config set registry https://registry.npmmirror.com/
> 
> RUN npm install
> 
> copy . .
> 
> RUN npm run build
> 
> EXPOSE 3000
> 
> CMD [ "node", "./dist/main.js" ]
> ~~~
>
> 这种方式构建镜像时，会先将 `package.json` 单独复制进去，安装完依赖之后再执行复制其它文件的命令，而不是将 所有文件都复制进去后再安装依赖。这么做是因为，如果多次构建这个镜像时，当 `package.json` 的内容没有发生变化时，则能匹配上之前缓存的层，那么至少之后的两条安装的指令都会匹配上缓存的层，可以直接复用缓存而不必重新构建。如果将 `package.json` 和其它所有文件一同复制进去，再安装依赖，那么一旦其它任何文件发生变化，就无法匹配上缓存的层，安装依赖的指令就得重新执行。

#### 多阶段构建

在传统的 `Docker` 镜像构建过程中，所有的构建步骤（包括编译、依赖安装等）和最终运行环境包含在同一镜像中，可能导致最终的镜像中包含了一些仅在构建过程中需要的工具和依赖以及一些运行时中不需要的源码。而如果使用多个 `Dockerfile` 来分别生成构建环境和运行时环境则较为复杂。

`Docker v17.05` 开始支持多阶段构建（`multistage builds`）。多阶段构建支持在一个 `Dockerfile` 中定义多个构建阶段：

- 在先前的阶段中生成一个包含所编译工具和依赖的构建环境镜像来构建应用。
- 在最后的阶段中生成一个轻量级的运行时环境镜像，将先前阶段构建好的运行时所需文件复制到这个镜像中。这样最终的镜像就仅包含了运行时所需的内容。

以下是一个使用了多阶段构建的 `Dockerfile` 示例：

> ~~~dockerfile
> FROM node:18-alpine3.14 as build-stage
> 
> WORKDIR /app
> 
> COPY package.json .
> 
> RUN npm config set registry https://registry.npmmirror.com/
> 
> RUN npm install
> 
> COPY . .
> 
> RUN npm run build
> 
> # production stage
> FROM node:18-alpine3.14 as production-stage
> 
> COPY --from=build-stage /app/dist /app
> 
> COPY --from=build-stage /app/package.json /app/package.json
> 
> WORKDIR /app
> 
> RUN npm config set registry https://registry.npmmirror.com/
> 
> RUN npm install --production
> 
> EXPOSE 3000
> 
> CMD ["node", "/app/main.js"]
> ~~~
>
> 在 `FROM` 指令后面可以使用 `as` 关键字来为某一阶段命名。
>
> 在 `COPY` 指令中可以使用 `--from=xxx` 参数将之前阶段中的文件复制到当前阶段中。

## `Docker Compose`

`Docker Compose` 可以实现对 `Docker` 容器集群的快速编排。它允许用户通过一个单独的 `docker-compose.yml` 模板文件来定义一组相关联的应用容器为一个项目。

`Compose` 中有两个重要概念：

- 服务（`service`）：

  一个应用的容器。实际上可以包括若干运行相同镜像的容器实例。

- 项目（`project`）

  由一组关联的应用容器组成的一个完整业务单元，在 `docker-compose.yml` 文件中定义。一个项目可以由多个服务（容器）关联而成。

[Compose 模板文件 · Docker -- 从入门到实践 (docker-practice.github.io)](https://docker-practice.github.io/zh-cn/compose/compose_file.html)

## 容器间通信

`Docker` 通过 `Namespace` 机制实现了容器的隔离，每个容器也有自己的 `Network Namespace`，因此不同容器之间无法直接通过端口访问其它容器的服务。

`Docker` 支持自定义网络，将容器加入同一个自定义网络后，容器之间就可以通过自定义网络通信。

创建自定义网络：

~~~bash
$ docker network create my-net
~~~

连接容器：

~~~bash
$ docker run -it --rm --name busybox1 --network my-net
~~~

~~~bash
$ docker run -it --rm --name busybox2 --network my-net
~~~

这样，`busybox1` 和 `busybox2` 通过自定义网络进行了网络桥接。对于不需要让宿主机访问的容器，可以不进行端口映射。自定义网络支持 `DNS` 解析，容器之间可以通过容器名直接互相访问。

对于 `Docker Compose`，会为项目中的容器自动创建一个自定义网络而对它们进行网络桥接。
