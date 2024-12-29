## 路径相关

### 别名配置

`vite.config.ts` 中配置：

~~~typescript
// vite.config.ts
import { defineConfig } from "vite";
import path from 'path';

export default defineConfig({
  resolve: {
    alias: {
      "@": path.join(__dirname, "src"),
    },
  },
});
~~~

在 `tsconfig.json` 中配置使得在编辑器中也生效：

~~~json
{
  "paths": {
    "@/*": ["./src/*"]
  }
}
~~~

### 同步 `tsconfig.json` 配置

通过 `vite-tsconfig-paths` 插件可以使得 `vite` 通过 `tsconfig.json` 中的 `paths` 和 `baseUrl` 选项来配置模块解析路径：

~~~typescript
import { defineConfig } from "vite";
import tsconfigPaths from "vite-tsconfig-paths";

export default defineConfig({
  plugins: [tsconfigPaths()],
});
~~~

## 静态资源处理

### 自动处理

`vite` 会将引入的静态资源会返回解析后的公共路径 `url`：

~~~typescript
import imgUrl from './img.png'
document.getElementById('hero-img').src = imgUrl
~~~

这里的 `imgUrl` 在 `dev` 模式下是 `"/img.png"`，在生产构建后会是类似于 `"/assets/img.2d8efhg.png"`。这样的行为类似于 `Webpack` 中的 `file-loader`。

常见的图像、媒体和字体文件类型被自动检测为静态资源。可以使用 [`assetsInclude` 选项](https://vitejs.cn/vite3-cn/config/shared-options.html#assetsinclude) 扩展内部列表。

### 显式处理

使用 `?url` 后缀可以显式地将未被包含在内部列表或 `assetsInclude` 中的资源解析为 `url`：

~~~typescript
import workletURL from 'extra-scalloped-border/worklet.js?url'
CSS.paintWorklet.addModule(workletURL)
~~~

使用 `?raw` 后缀可以将资源解析为字符串内容：

~~~typescript
import shaderString from './shader.glsl?raw'
~~~

使用 `?inline` 后缀可以将资源强制内联，而不是打包成单独的文件。

使用 `?worker` 或 `?sharedworker` 后缀将资源导入为 `web worker`：

~~~typescript
// 在生产构建中将会分离出 chunk
import Worker from './shader.js?worker'
const worker = new Worker()
~~~

~~~typescript
// sharedworker
import SharedWorker from './shader.js?sharedworker'
const sharedWorker = new SharedWorker()
~~~

~~~typescript
// 内联为 base64 字符串
import InlineWorker from './shader.js?worker&inline'
~~~

~~~typescript
// 以 URL 形式读取，与 ?url 不同的是，?worker&url 读取的文件在构建过程中会被打包为 chunk
import MyWorker from './worker?worker&url'
~~~

### `public` 目录

默认位于 `<root>/public`，可以通过 `publicDir` 选项指定。

`public` 目录下的静态资源会在打包时完整地复制到目标目录的根目录下，且保留原本的文件名。

## 环境变量

### 客户端环境变量

`import.meta.env` 对象是 `vite` 中用于暴露环境变量的一个特殊对象。其默认的内建变量有：

- `import.meta.env.MODE`: {`string`} 应用运行的[模式](https://vitejs.cn/vite3-cn/guide/env-and-mode.html#modes)。
- `import.meta.env.BASE_URL`: {`string`} 部署应用时的基本 URL。他由[`base` 配置项](https://vitejs.cn/vite3-cn/config/shared-options.html#base)决定。
- `import.meta.env.PROD`: {`boolean`} 应用是否运行在生产环境。
- `import.meta.env.DEV`: {`boolean`} 应用是否运行在开发环境 (永远与 `import.meta.env.PROD`相反)。
- `import.meta.env.SSR`: {`boolean`} 应用是否运行在 [server](https://vitejs.cn/vite3-cn/guide/ssr.html#conditional-logic) 上。

`vite` 内置了 `dotev` 以读取 `.env` 环境配置文件。会从环境目录下的以下文件中加载额外的环境变量：

- `.env` 所有情况下都会加载。
- `.env.local` 所有情况下都会加载，但会被 `vite` 默认生成的 `.gitignore` 命中，即会被 `git` 忽略。
- `.env.[mode]` 只在指定模式下加载。在对应模式下优先级高于 `.env`。
- `.env.[mode].local` 只在指定模式下加载，但会被 `git` 忽略。

为了防止意外地将一些环境变量泄漏到客户端，只有以 `VITE_` 为前缀的变量才会暴露给经过 `vite` 处理的代码。例如下面这些环境变量：

```.env
VITE_SOME_KEY=123
DB_PASSWORD=foobar
```

只有 `VITE_SOME_KEY` 会被暴露为 `import.meta.env.VITE_SOME_KEY` 提供给客户端源码，而 `DB_PASSWORD` 则不会。

```typescript
console.log(import.meta.env.VITE_SOME_KEY) // 123
console.log(import.meta.env.DB_PASSWORD) // undefined
```

可以在 `src/vite-env.d.ts` 中定义这些被添加的环境变量的类型：

~~~typescript
/// <reference types="vite/client" />

interface ImportMetaEnv {
  readonly VITE_APP_TITLE: string
  // more env variables...
}

interface ImportMeta {
  readonly env: ImportMetaEnv
}
~~~

