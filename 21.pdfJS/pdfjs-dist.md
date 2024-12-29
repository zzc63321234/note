usage example: [pdf.js/examples/node/getinfo.mjs at master · mozilla/pdf.js (github.com)](https://github.com/mozilla/pdf.js/blob/master/examples/node/getinfo.mjs)

## 引入

如何设置 `workerSrc`：

> ~~~typescript
> import * as pdfjsLib from "pdfjs-dist";
> pdfjsLib.GlobalWorkerOptions.workerSrc = "/dependencies/pdf.worker.mjs";
> ~~~
>
> `pdfjsLib.GlobalWorkerOptions.workerSrc` 的值需要是一个指向 `pdf.worker.mjs` （或是 `pdf.worker.js`，`pdf.worker.min.js` 等名称）文件的路径字符串，而不是该文件中的模块。这里将 `pdf.worker.mjs` 作为静态资源放入了 `public` 目录下的 `dependencies` 中，这样在服务器启动后就能通过 `pdfjsLib.GlobalWorkerOptions.workerSrc` 的字符串值作为路径访问到该文件。

如何在 `web worker` 中使用 `pdfjs` 的主体，以 `vite` 为例：

>~~~typescript
>import * as pdfjsLib from "pdfjs-dist";
>import workerSrc from "pdfjs-dist/build/pdf.worker.min.mjs?worker&url";
>
>// 在 web worker 中，pdfjs 通过设置 workerPort 的方式来指定它的 pdf.worker.js
>const worker = new Worker(workerSrc, { type: "module" });
>pdfjsLib.GlobalWorkerOptions.workerPort = worker;
>
>// Worker 环境中不存在 document，需要指定 ownerDocument
>pdfjsLib.getDocument({
>  url: "xxx",
>  ownerDocument: {
>    fonts: self.fonts,
>    createElement: (name: string) => {
>      if (name == "canvas") {
>        return new OffscreenCanvas(0, 0);
>      }
>      return null;
>    },
>  },
>});
>~~~

## `pdfjsLib` 模块中的 `API` 解释

见 [JSDoc: Module: pdfjsLib (mozilla.github.io)](https://mozilla.github.io/pdf.js/api/draft/module-pdfjsLib.html)。

### 渲染流程相关

#### `pdfjsLib.getDocument()`

`pdfjsLib` 的静态方法，用于加载 `PDF`。

接收的参数可以是： `string` | `URL` | `TypedArray` | `ArrayBuffer` | `DocumentInitParameters`。

如果使用 `URL` 作为参数，那么则会调用 `Fetch API` 获取它，需要遵守同源策略。

返回一个 `PDFDocumentLoadingTask`。

#### `PDFDocumentLoadingTask`

用来表示 `PDF` 文档的加载任务的对象。

见 [JSDoc: Class: PDFDocumentLoadingTask (mozilla.github.io)](https://mozilla.github.io/pdf.js/api/draft/module-pdfjsLib-PDFDocumentLoadingTask.html)。

`loadingTask.promise` 中是一个 `promise`，若 `PDF` 文档加载成功，该 `promise` 会将给出一个 `PDFDocumentProxy` 实例。

其它实例属性：

- `docId`：

  `string` 类型，表示该加载任务的唯一标识符。

- `destroyed`：

  `boolean` 类型，表示该加载任务是否已销毁。

- `destroy()`：

  该方法用于销毁加载任务。

  返回一个 `promise`，当销毁完成时会转变为 `fulfilled` 状态。

- `onPassword`：

  设置用于请求密码的回调函数。

  与 `PasswordResponses` 相关。

- `onProgress`：

  设置监视加载进度的回调函数。

  回调接收 `OnProgressParameters` 参数。

#### `PDFDocumentProxy`

这是 `worker` 线程中的 `PDFDocument` 的代理对象。

见 [JSDoc: Class: PDFDocumentProxy (mozilla.github.io)](https://mozilla.github.io/pdf.js/api/draft/module-pdfjsLib-PDFDocumentProxy.html)。

从 `PDFDocumentLoadingTask` 实例的 `promise` 属性中的 `promise` 中可以取得 `PDFDocumentProxy` 实例。

常用实例属性：

- `numPages`：

  `number` 类型，表示当前文档中的总页数。

- `getPage()`

  传入页数（从 `1` 开始）调用，会以 `promise` 形式返回一个对应文档中该页的 `PDFPageProxy`。

- `cleanup()`：

  调用后会清理当前文档在 `worker` 线程上分配的资源。如果文档正在渲染中调用，则渲染会出错。

- `destroy()`：

  销毁当前文档实例并停止对应的 `worker` 线程。

- `getPageIndex()`：

  传入一个 `RefProxy` 调用，会以 `promise` 形式返回该 `refProxy` 的页面索引序号（以 `0` 开始）。

- `getPageLabels()`

  调用后会以 `promise` 的形式返回一个数组（如果文档存在标签，否则返回 `null`），数组中的每一项代表对应该索引的页面的标签字符串。

- `saveDocument()`

  调用后会以 `promise` 的形式返回已被保存的文档的数据的 `Unit8Array`。

#### `PDFPageProxy`

这是 `worker` 线程中的 `PDFPage` 的代理对象。

见 [JSDoc: Class: PDFPageProxy (mozilla.github.io)](https://mozilla.github.io/pdf.js/api/draft/module-pdfjsLib-PDFPageProxy.html)。

使用 `PDFDocumentProxy` 实例的 `getPage()` 方法传入页数调用后返回的 `promise` 中可以取得 `PDFPageProxy`。

常用实例属性：

- `commonObjs`：

  该属性的值是一个 `PDFObjects` 实例。

- `pageNumber`：

  `number` 类型，表示当前页数（从 `1` 开始）。

- `ref`：

  该属性的值是一个 `RefProxy` 实例或 `null`，表示一个指向该页的引用。

- `rotate`：

  `number` 类型，表示该页的顺时针旋转角度的度数。

- `view`

  一个表示该页的可见部分的坐标数组，表示为 `[x1, y1, x2, y2]`。

- `stats`

  如果开启了 `page stats`，该属性的值为表示 `page stat` 的 `StatTimer` 实例，否则为 `null`。

- `cleanup()`：

  该方法用于清理该 `page` 分配的资源。

  如果传入一个 `true`，那么就会重置 `page stats`（如果开启了 `page stats`）。

- `getViewPort()`

  调用后会返回一个 `PageViewPort` 对象，包含 `width` 和 `height` 属性以及一些渲染所需要的转换相关的属性。

  接收 `GetViewportParameters` 对象作为参数。

- `render()`

  调用后会开始一个渲染进程。

  接收 `RenderParameters` 对象作为参数：

  > 其中必须包含的属性有：
  >
  > - `canvasContext`：
  >
  >   一个 `CanvasRenderingContext2D` 实例。
  >
  > - `viewPort`：
  >
  >   一个 `PageViewport` 实例。通过 `PDFPageProxy` 实例的 `getViewPort()` 方法获取。

  返回一个 `RenderTask` 实例。

#### `RenderTask`

用于控制渲染任务的对象。

见 [JSDoc: Class: RenderTask (mozilla.github.io)](https://mozilla.github.io/pdf.js/api/draft/module-pdfjsLib-RenderTask.html)。

常用实例属性：

- `promise`：

  该属性的值为一个 `promise`，当渲染任务完成时会转变为 `fulfilled` 状态。

- `cancel()`：

  调用该方法后会取消渲染任务。渲染任务的 `promise` 会转变为 `rejected` 状态。

### 渲染信息相关

#### `GetViewportParameters`

将该类型的对象传递给 `PDFPageProxy` 实例的 `getViewPort()` 方法，以返回对应的渲染视口对象。

对象中的属性：

| Name       | Type    | Attributes | Description                                                  |
| :--------- | :------ | :--------- | :----------------------------------------------------------- |
| `scale`    | number  |            | The desired scale of the viewport.                           |
| `rotation` | number  | <optional> | The desired rotation, in degrees, of the viewport. If omitted it defaults to the page rotation. |
| `offsetX`  | number  | <optional> | The horizontal, i.e. x-axis, offset. The default value is `0`. |
| `offsetY`  | number  | <optional> | The vertical, i.e. y-axis, offset. The default value is `0`. |
| `dontFlip` | boolean | <optional> | If true, the y-axis will not be flipped. The default value is `false`.（`PDF` 中的 `y` 轴与 `CANVAS` 中的 `y` 轴的方向是相反的，因此提供了该选项。默认会翻转 `y` 轴。） |

## 相关文章

[javascript - PDF.js实现个性化PDF渲染（文本复制） - 个人文章 - SegmentFault 思否](https://segmentfault.com/a/1190000016963084)