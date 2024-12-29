## 简介

- `Echarts` 全称 `EnterpriseCharts`，即企业级数据图表，是一个基于 `JavaScript` 的开源可视化图标库，可提供直观、生动、可交互、可高度个性化定制的数据可视化图表
- `ECharts` 底层依赖轻量级的 `ZRender` 图形库
  - `ZRender` 提供 `Canvas`、`SVG`、`VML` 等多种渲染方式
  - `ECharts` 起初只使用 `Canvas` 渲染，直到 `ECharts v4` 版本后支持 `SVG` 渲染
  - 不同渲染方式的区别
    - `Canvas`
      - 适合绘制图形元素数量较多的图表
      - 如热力图、地理坐标系、平行坐标系上的大规模线图等
    - `SVG`
      - 内存占用低、可无损缩放
      - 图形元素数量多时渲染会变慢
    - 如何选择
      - 当需要创建许多 `ECharts` 实例时选择 `SVG`
      - 当图形元素数量很多时选择 `Canvas`



## 初始化

- `echarts.init([dom][, theme][, opts])`
  - 返回一个 `ECharts` 实例（`echartInstance`）
  - 注意单个容器上不可以初始化多个 `Echarts` 实例
  - [Documentation - Apache ECharts](https://echarts.apache.org/zh/api.html#echarts.init)
  - 如果使用默认 `theme`，则传入 `null`
  - `opts` 常用选项
    - `renderer`
      - 渲染模式，支持 `'canvas'`（默认值） 或者 `'svg'`
    - `width`、`height`
      - 显式地指定实例宽高，单位为像素
      - 默认自动读取容器宽高
    - `useCoarsePointer`
      - 是否扩大可点击元素的响应范围
      - 可选值
        - `null`（默认值）
          - 对移动设备开启
        - `true`
          - 总是开启
        - `false`
          - 总是不开启
      - 参见[增加交互响应范围](https://echarts.apache.org/handbook/zh/how-to/interaction/coarse-pointer)
    - `pointerSize`
      - 扩大元素响应范围的像素大小
      - 默认是 `44`

## 容器

- 注意需要确定调用 `echarts.init` 时确保容器已经有宽度和高度了，否则需要在初始化时在 `opts` 参数对象中指定容器大小

  ~~~html
  <div id="main"></div>
  <script type="text/javascript">
    const myChart = echarts.init(document.getElementById('main'), null, {
      width: 600,
      height: 400
    })
  </script>
  ~~~

- 如果图表初始化后，容器大小发生了改变，可以调用 `echartInstance.resize` 方法重置图表大小

  ~~~html
  <style>
    #main,
    html,
    body {
      width: 100%;
    }
    #main {
      height: 400px;
    }
  </style>
  <div id="main"></div>
  <script type="text/javascript">
    const myChart = echarts.init(document.getElementById('main'))
    window.addEventListener('resize', function() {
      myChart.resize()
    })
  </script>
  ~~~

- `echartInstance.resize([opts])`

  - 重新获取图表大小并刷新画布
  - 接收一个可选的 `opts` 配置对象，包含以下属性
    - `width`
      - 可显式指定实例宽度，单位为像素
      - 如果传入值为 `null`/`undefined`/`'auto'`，则表示自动取 `dom`（实例容器）的宽度
    - `height`
      - 可显式指定实例高度，单位为像素
      - 如果传入值为 `null`/`undefined`/`'auto'`，则表示自动取 `dom`（实例容器）的高度
    - `silent`
      - 是否禁止抛出事件
      - 默认为 `false`
    - `animation`
      - `resize` 的时候是否应用过渡动画，包含时长 `duration` 和缓动 `easing` 两个配置，默认 `duration` 为 0，即不应用过渡动画
  - 注意：有时候图表会放在多个标签页里，那些初始隐藏的标签在初始化图表的时候因为获取不到容器的实际高宽，可能会绘制失败，因此在切换到该标签页时需要手动调用 `resize` 方法获取正确的高宽并且刷新画布，或者在 `opts` 中显示指定图表高宽

- 容器销毁时，需要调用 [`echartsInstance.dispose`](https://echarts.apache.org/api.html#echartsInstance.dispose) 销毁实例，避免内存泄漏

## 样式

### 颜色主题（`Theme`）

- 颜色主题是最简单的更改全局样式的方式

- `Echarts 5` 内置的有默认主题和 `'dark'` 主题，其余的可以在 [主题编辑器](https://echarts.apache.org/theme-builder.html) 里访问到，也可以在里面自己编辑主题

- 加载主题编辑器中的主题

  - 如果保存为 `JSON` 格式，则需要自行加载和注册

    ~~~javascript
    // 假设主题名称是 "vintage"
    fetch('theme/vintage.json')
      .then(r => r.json())
      .then(theme => {
        echarts.registerTheme('vintage', theme)
        var chart = echarts.init(dom, 'vintage')
      })
    ~~~

  - 如果保存为 `UMD` 格式的 `JS` 文件，则文件内部已经进行了注册，直接引入使用即可

    ~~~javascript
    // HTML 引入 vintage.js 文件后（假设主题名称是 "vintage"）
    var chart = echarts.init(dom, 'vintage')
    // ...
    ~~~

### 调色盘

- 调色盘，可以在 `option` 中设置。它给定了一组颜色，图形、系列会自动从其中选择颜色。 可以设置全局的调色盘，也可以设置系列自己专属的调色盘

  ~~~javascript
  const myChart = echarts.init(document.getElementById('main'))
  
  const option = {
    // 全局调色盘。
    color: [
      '#c23531',
      '#2f4554',
      '#61a0a8',
      '#d48265',
      '#91c7ae',
      '#749f83',
      '#ca8622',
      '#bda29a',
      '#6e7074',
      '#546570',
      '#c4ccd3'
    ],
  
    series: [
      {
        type: 'bar',
        // 此系列自己的调色盘。
        color: [
          '#dd6b66',
          '#759aa0',
          '#e69d87',
          '#8dc1a9',
          '#ea7e53',
          '#eedd78',
          '#73a373',
          '#73b9bc',
          '#7289ab',
          '#91ca8c',
          '#f49f42'
        ]
        // ...
      },
      {
        type: 'pie',
        // 此系列自己的调色盘。
        color: [
          '#37A2DA',
          '#32C5E9',
          '#67E0E3',
          '#9FE6B8',
          '#FFDB5C',
          '#ff9f7f',
          '#fb7293',
          '#E062AE',
          '#E690D1',
          '#e7bcf3',
          '#9d96f5',
          '#8378EA',
          '#96BFFF'
        ]
        // ...
      }
    ]
  }
  
  myChart.setOption(option)
  ~~~

### 直接设置样式

- [样式 - 概念篇 - Handbook - Apache ECharts](https://echarts.apache.org/handbook/zh/concepts/style#直接的样式设置)

## 组件相关

### 配置 `option` 与组件合并

- `echartsInstance.setOption(option[, opts])`
  - [Documentation - Apache ECharts](https://echarts.apache.org/zh/api.html#echartsInstance.setOption)
  - 设置图表实例的配置项以及数据，万能接口，所有参数和数据的修改都可以通过 `setOption` 完成，`ECharts` 会合并新的参数和数据，然后刷新图表
  - 如果开启动画，`ECharts` 找到两组数据之间的差异然后通过合适的动画去表现数据的变化
  - 接收参数
    - `option`
      - 图表的配置项和数据，具体见[配置项手册](https://echarts.apache.org/zh/option.html)
    - `opts`
      - `notMerge`
        - 是否不跟之前设置的 `option` 进行合并
        - 默认为 `false`，即表示合并
        - 如果为 `true`，表示所有组件都会被删除，然后根据新 `option` 创建所有新组件
      - `replaceMerge`
        - 可以在这里指定一个或多个组件，如：`xAxis`, `series`，这些指定的组件会进行**替换合并**
        - 如果用户想删除部分组件，也可使用 `replaceMerge`
      - `lazyUpdate` 
        - 在设置完 `option` 后是否不立即更新图表
        - 默认为 `false`，即同步立即更新
        - 如果为 `true`，则会在下一个 `animation frame` 中，才更新图表
      - `silent`
        - 阻止调用 `setOption` 时抛出事件
        - 默认为 `false`，即抛出事件
- 组件合并模式
  - 对于一种类型的组件（如 `xAxis`，`series`）
    - 如果设置 `opts.notMerge` 为 `true`，那么旧的组件会完全移除，新的组件会根据 `option` 创建
    - 如果 `opts.notMerge` 为 `false`
      - 如果在 `opts.replaceMerge` 里指定组件类型，这类组件会进行**替换合并**
      - 否则会进行**普通合并**

### `option` 配置项与组件

- `option` 配置项用于指定图表的配置项和数据
- 组件是 `ECharts` 中表现某种功能或视觉表现的模块
  - 例如标题（`title`）、图例（`legend`）、提示框（`tooltip`）、坐标轴（`xAxis`、`yAxis`）、数据序列（`series`） 等都是图表的组件
  - 这些组件通过 `option` 里对应的属性进行配置

#### 大多数组件共有属性

- `id`
  - 组件 `id``
  - ``string` 类型
  - 默认不指定
  - 用于在 `option` 或 `API` 中引用组件
- `show`
  - 控制属性是否显示
- `zlevel`
  - 用于 `Canvas` 分层
  - 不同 `zlevel` 值的图形会放置在不同的 `Canvas` 中
  - `zlevel` 大的 `Canvas` 会放在 `zlevel` 小的 `Canvas` 上面
- `z`
  - 组件中所有图形的 `z` 值
  - `z` 值大的图形会覆盖 `z` 值小的图形
  - `z` 的优先级低于 `zlevel`，且不会创建新的 `Canvas`
- `triggerEvent`
  - 是否触发事件
- `left`、`top`、`right`、`bottom`
  - 组件离容器左侧、上侧、右侧、下侧的距离
  - 可以是 `20` 这样的具体像素值，也可以是 `'20%'` 这样的容器高宽百分比
  - 自动对齐
    - `left` 的值也可以是 `'left'`、`'center'`、`'right'`，组件会根据相应位置自动对齐
    - `top` 的值也可以是 `'top'`、`'middle'`、`'bottom'`，组件会根据相应位置自动对齐
    - 也就是说，可以只拿 `left` 和 `top` 两个属性实现组件的大致位置布局
- `itemGap`
  - 组件之间每项图形的间隔
- `padding`
  - 组件内边距
  - 可以接收数字直接表示内边距
  - 也可以接收一个数组，按顺序分别表示上、右、下、左的内边距
- `silent`
  - 是否不响应组件事件
  - 默认为 `false`

#### 样式相关配置属性

- `itemStyle`
- `emphasis`
- `select`

### 常用组件

#### `title`

- 标题组件，包含主标题和副标题
- `ECharts 3` 以后单个 `echarts` 实例可以存在多个标题组件
- 常用属性
  - `show`
  - `text`、`subtext`
    - 主、副标题文本
    - 支持使用 `\n` 换行
  - `link`、`sublink`
    - 主、副标题文本超链接
  - `target`、`subtarget`
    - 主、副标题超链接的打开方式
    - 可选值
      - `'self'` 当前窗口打开
      - '`blank`' 新窗口打开，默认值
  - `textStyle`、`subtextStyle`
    - 主、副标题文本样式
  - `textAlign`
    - `text` 和 `subtext` 的水平对齐
    - 可选值
      - `'auto'`（默认值）
      - `'left'`
      - `'right'`
      - `'center'`
  - `textVerticalAlign`
    - `text` 和 `subtext` 的垂直对齐
    - 可选值
      - `'auto'` （默认值）
      - `'top'`
      - `'bottom'`
      - `'middle'`
  - `padding`
    - 标题组件内间距
    - 默认为 `5`
  - `tiemGap`
    - 主副标题间距
    - 默认为 `10`

#### `legend`

- 图例组件
- 图里组件展现了不同 `series` 的标记（`symbol`）、颜色和名字
- 可以通过点击图例控制 `series` 的显示与隐藏
- `ECharts 3` 以后单个 `echarts` 实例可以存在多个图例组件

- 常用属性

  - `type`

    - 图例的类型
    - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#legend.type)
    - 可选值
      - `'plain'`（默认值）
        - 普通图例
      - `'scroll'`
        - 可滚动翻页的图例
        - 适用于图例数量较多时

  - `orient`

    - 图例列表的布局方向
    - 可选值
      - `'horizontal'`（默认值）
      - `vertical`

  - `width`、`height`

    - 图例组件的最大宽高
    - 水平布置时受 `width` 限制，垂直布置时受 `height` 限制
    - 默认为 `'auto'`
    - 可以设置为绝对值或相对容器大小的百分比

  - `align`

    - 图例标记和文本对齐方式
    - 可选值
      - `'auto'`（默认值）
        - 根据组件的位置和 `orient` 决定
        - 当组件的 `left` 值为 `'right'`，且 `orient` 为 `'vertical'` 时表现为右对齐，否则表现为左对齐
      - `'left'`
        - 左对齐
        - 图例标记在文本左边
      - `'right'`
        - 右对齐
        - 图例标记在文本右边

  - `padding`

    - 图例组件内边距
    - 默认为 `5`

  - `itemGap`

    - 图例每项之间的间隔

  - `itemWidth`、`itemHeight`

    - 图例标记的宽高

  - `itemStyle`

    - 图例的图形样式
    - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#legend.itemStyle)

  - `lineStyle`

    - 图例中线图形的样式
    - 用于折线图的横线样式
    - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#legend.lineStyle)

  - `textStyle`

    - 图例的公用文本样式
    - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#legend.textStyle)

  - `formatter`

    - 格式化图例文本

    - 支持字符串模板和回调函数两种形式

    - 示例

      ~~~javascript
      // 使用字符串模板，模板变量为图例名称 {name}
      formatter: 'Legend {name}'
      // 使用回调函数
      formatter: function (name) {
          return 'Legend ' + name;
      }
      ~~~

  - `selectedMode`

    - 图例的选择模式，控制是否可以通过点击图例改变系列的显示状态
    - 可选值
      - `true`（默认值）
        - 默认开启，等同于 `'multiple'`
      - `false`
        - 关闭选择模式
      - `'single'`
        - 单选模式
        - 即每次只显示一个图例
      - `'multiple'`
        - 多选模式

  - `inactiveColor`、`inactiveBorderColor`

    - 图例关闭时的颜色、图例关闭时图例标记的描边颜色（活动状态的颜色在 `itemStyle` 中设置）
    - 默认为 `'#ccc'`

  - `selected`

    - 图例选中状态表

    - 示例

      ~~~javascript
      selected: {
          // 选中'系列1'
          '系列1': true,
          // 不选中'系列2'
          '系列2': false
      }
      // 键名为系列名
      ~~~

  - `selector`

    - 图例组件中的选择器按钮

    - 从 `v4.4.0` 开始支持

    - 默认不显示，可手动开启，也可以手动配置每个按钮的标题

    - 例

      ~~~javascript
      selector: [
          {
              // 全选
              type: 'all',
              // 可以是任意你喜欢的标题
              title: '全选'
          },
          {
              // 反选
              type: 'inverse',
              // 可以是任意你喜欢的标题
              title: '反选'
          }
      ]
      
      // 或
      selector: true
      
      // 或
      selector: ['all', 'inverse']
      ~~~

  - `tooltip`

    - 图例的 tooltip 配置，配置项同 [tooltip](https://echarts.apache.org/zh/option.html#tooltip)

    - 默认不显示，可以在 `legend` 文字很多的时候对文字做裁剪并且开启 `tooltip`，例

      ~~~javascript
      legend: {
          formatter: function (name) {
              return echarts.format.truncateText(name, 40, '14px Microsoft Yahei', '…');
          },
          tooltip: {
              show: true
          }
      }
      ~~~

  - `icon`

    - 自定义图例标记的形状

    - `ECharts` 提供的内置标记类型

      - `'circle'`（默认值）
      - `'emptyCircle'`
      - `'rect'`
      - `'roundRect'`
      - `'triangle'`
      - `'diamond'`
      - `pin`
      - `'arrow'`
      - `'none'`
  
    - 可以通过 `'image://url'` 设置为图片，其中 `URL` 为图片的链接，或者 `dataURI`，例如
  
      ~~~javascript
      // 图片链接
      'image://http://example.website/a/b.png'
      
      // dataURI
      'image://data:image/gif;base64,R0lGODlhEAAQAMQAAORHHOVSKudfOulrSOp3WOyDZu6QdvCchPGolfO0o/XBs/fNwfjZ0frl3/zy7////wAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAACH5BAkAABAALAAAAAAQABAAAAVVICSOZGlCQAosJ6mu7fiyZeKqNKToQGDsM8hBADgUXoGAiqhSvp5QAnQKGIgUhwFUYLCVDFCrKUE1lBavAViFIDlTImbKC5Gm2hB0SlBCBMQiB0UjIQA7'
      ~~~
  
    - 可以通过 `'path://'` 将图标设置为任意的矢量路径，例如
  
      ~~~javascript
      'path://M30.9,53.2C16.8,53.2,5.3,41.7,5.3,27.6S16.8,2,30.9,2C45,2,56.4,13.5,56.4,27.6S45,53.2,30.9,53.2z M30.9,3.5C17.6,3.5,6.8,14.4,6.8,27.6c0,13.3,10.8,24.1,24.101,24.1C44.2,51.7,55,40.9,55,27.6C54.9,14.4,44.1,3.5,30.9,3.5z M36.9,35.8c0,0.601-0.4,1-0.9,1h-1.3c-0.5,0-0.9-0.399-0.9-1V19.5c0-0.6,0.4-1,0.9-1H36c0.5,0,0.9,0.4,0.9,1V35.8z M27.8,35.8 c0,0.601-0.4,1-0.9,1h-1.3c-0.5,0-0.9-0.399-0.9-1V19.5c0-0.6,0.4-1,0.9-1H27c0.5,0,0.9,0.4,0.9,1L27.8,35.8L27.8,35.8z'
      ~~~
  
  - `data`
  
    - 图例的数据数组
  
    - 数组项通常为一个字符串，每一项代表一个系列的 `name`（如果是饼图，也可以是饼图单个数据的 `name`）
  
    - 如果 `data` 没有被指定，则会自动读取
  
    - 如果需要单独配置某一项的样式，可以将该项写成配置对象，使用 `name` 属性表示该系列的 `name`，例
  
      ~~~javascript
      data: [{
          name: '系列1',
          // 强制设置图形为圆。
          icon: 'circle',
          // 设置文本为红色
          textStyle: {
              color: 'red'
          }
      }, '系列2', '系列3']

#### `tooltip`

- 提示框组件
- 提示框组件可以设置在多种地方
  - 可以设置在 `option` 的全局
  - 可以设置在坐标系中，即 [grid.tooltip](https://echarts.apache.org/zh/option.html#grid.tooltip)、[polar.tooltip](https://echarts.apache.org/zh/option.html#polar.tooltip)、[single.tooltip](https://echarts.apache.org/zh/option.html#single.tooltip)
  - 可以设置在系列中，即 [series.tooltip](https://echarts.apache.org/zh/option.html#series.tooltip)
  - 可以设置在系列的每个数据项中，即 [series.data.tooltip](https://echarts.apache.org/zh/option.html#series.data.tooltip)
- 常用属性
  - `trigger`
    - 触发类型
    - 可选值
      - `'item'`
        - 数据项图形触发
        - 主要用于散点图、饼图等无类目轴的图表
      - `'axis'`
        - 坐标轴触发（只要处于坐标轴对应的区域都能触发）
        - 主要用于柱状图、折线图等使用类目轴的图表
        - 可以通过 `tooltip.axisPointer.axis` 指定用于触发的坐标轴
      - `'none'`
        - 不触发
  - `renderMode`
    - 浮层的渲染模式
    - 默认为 `'html'`，即使用 `DOM` 节点展示 `tooltip`
    - 还可以设置为 `'richText'`，即使用富文本形式渲染，渲染的结果在图表对应的 `Canvas` 中，这对于一些没有 `DOM` 的环境（如微信小程序）有更好的支持
  - `showContent`
    - 是否显示提示框浮层
    - 默认为 `true`
    - 当只需要 `tooltip` 触发事件时，或当只需要显示 `axisPointer` 而不需要显示内容时，可以配置该项为 `false`
  - `alwaysShowContent`
    - 默认情况下移除可触发提示框区域后一定时间会隐藏，设为 `true` 可保证一直显示提示框内容
  - `triggerOn`
    - `triggerOn`
      - 坐标轴指示器的触发条件
      - 可选值
        - `'mousemove'`
          - 鼠标移动时触发
        - `'click'`
          - 鼠标点击时触发
        - `'mousemove|click'`（默认值）
          - 鼠标移动和鼠标点击时都触发
          - 虽然在直接使用时，这和 `'mousemove'` 看起来没什么差别，但是在鼠标点击时，`'mousemove|click'` 下也可以触发一次对应事件，而 `'mousemove'` 下则不会
        - `'none'`
  - `showDelay`
    - 浮层显示的延迟，单位 `ms`，在 `triggerOn` 为 `'mousemove'` 时有效
    - 默认为 `0`
  - `hideDelay`
    - 浮层隐藏的延迟，单位 `ms`，在 `alwaysShowContent` 为 `true` 时无效
    - 默认为 `100`
  - `confine`
    - 是否将 `tooltip` 限制在图表区域内
    - 默认为 `false`
    - 当图表外层的 `dom` 被设置为 `'overflow: hidden'`，或者移动端窄屏，导致 `tooltip` 超出外界被截断时，此配置比较有用
  - `transitionDuration`
    - 提示框浮层的移动动画过渡时间，单位为 `s`
    - 默认值是 `0.4`
    - 设置为 `0` 时，提示框浮层会紧跟着鼠标或指示器移动
  - `formatter`
    - 提示框浮层内容格式器，支持字符串模板和函数两种形式
    - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#tooltip.formatter)
  - `axisPointer`
    - 坐标轴指示器配置项的快捷方式
    - 坐标轴指示器是指示坐标轴当前刻度的工具
    - 此处是 `tooltip` 中 `axisPointer` 的快捷方式，此外，还有全局 `axisPointer` 组件，并且不同坐标系中的每个轴都有自己的 `axisPointer`
    - 实际上坐标轴指示器的全部功能都可以通过坐标轴自己的 `axisPointer` 配置项（比如 `xAxis.axisPointer`）来实现，但是使用 `tooltip.axisPointer` 在简单场景下会更方便
    - 优先级低于坐标轴本身的 `axisPointer`
    - `tooltip` 中的 `axisPointer` 专有属性
      - `axis`
        - 启用指示器的坐标轴
        - 可选值
          - `'auto'`
          - `'x'`
          - `'y'`
          - `'radius'`
          - `'angle'`

#### `axisPointer`

- 坐标轴指示器组件的全局公用设置
- 如何显示 `axisPointer`
  - 对于坐标轴上的 `axisPointer`，可以设置它的 `show` 属性为 `true`，即可显示该轴的 `axisPointer`
  - `tooltip` 中的开启方式
    - 设置 `tooltip.trigger` 的值为 `'axis'` 或设置 `tooltip.axisPointer.type` 的值为 `'cross'`，此时坐标系会自动选择显示哪个轴的 `axisPointer`
    - 可以使用 `tooltip.axisPointer.axis` 指定要显示 `axisPointer` 的轴
    - 注意坐标轴的 `tooltip.axisPointer` 选项的设置会覆盖此设置
- 常用属性
  - `type`
    - 指示器类型
    - 可选值
      - `'line'`（默认值）
        - 直线指示器
      - `'shadow'`
        - 阴影指示器
      - `'none'`
        - 无指示器
      - `'cross'`
        - 十字准星指示器
        - 实际上这是启用两个正交的轴的直线指示器
  - `snap`
    - 坐标轴指示器是否自动吸附到点上
    - 默认为自动判断
    - 这个功能在数值轴和时间轴上比较有意义，可以自动寻找细小的数值点
  - `label`
    - 坐标轴指示器的文本标签
    - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#axisPointer.label)
    - 属性
      - `show`
        - 是否显示文本标签
        - `type` 为 `'cross'` 时默认显示，否则默认不显示
      - `formatter`
        - 文本标签的格式化器
        - 可以是字符串模板，也可以是函数
        - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#axisPointer.label.formatter)
  - `lineStyle`
    - 当 `type` 属性值为 `'line'` 时有效
    - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#axisPointer.lineStyle)
  - `shadowStyle`
    - 当 `type` 属性值为 `'shadow'` 时有效
    - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#axisPointer.shadowStyle)
- 全局 `axisPointer` 专有属性
  - `triggerOn`
    - 坐标轴指示器的触发条件
    - 可选值
      - `'mousemove'`
        - 鼠标移动时触发
      - `'click'`
        - 鼠标点击时触发
      - `'mousemove|click'`（默认值）
        - 鼠标移动和鼠标点击时都触发
        - 虽然在直接使用时，这和 `'mousemove'` 看起来没什么差别，但是在鼠标点击时，`'mousemove|click'` 下也可以触发一次对应事件，而 `'mousemove'` 下则不会
      - `'none'`
  - `link`
    - 用于使不同轴的 `axisPointer` 进行联动
    - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#axisPointer.link)

#### `toolbox`

- 工具栏组件

- 内置有[导出图片](https://echarts.apache.org/zh/option.html#toolbox.feature.saveAsImage)，[数据视图](https://echarts.apache.org/zh/option.html#toolbox.feature.dataView)，[动态类型切换](https://echarts.apache.org/zh/option.html#toolbox.feature.magicType)，[数据区域缩放](https://echarts.apache.org/zh/option.html#toolbox.feature.dataZoom)，[重置](https://echarts.apache.org/zh/option.html#toolbox.feature.reset)等工具

- 常用属性

  - `orient`

    - 工具栏 `icon` 的布局朝向
    - 可选值
      - `'horizontal'`（默认值）
      - `'vertical'`

  - `itemSize`

    - 工具栏 `icon` 大小
    - 默认为 `15`

  - `itemGap`

    - 工具栏 `icon` 每项之间的间隔
    - 默认为 `8`

  - `showTitle`

    - 是否在鼠标 `hover` 的时候显示每个工具 `icon` 的标题
    - 默认为 `true`

  - `iconStyle`、`emphasis`

    - `icon` 样式设置
    - 由于 `icon` 的文本信息只在 `icon hover` 时候才显示，所以文字相关的配置项在 `emphasis.iconStyle` 下设置
    - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#toolbox.iconStyle)

  - `feature`

    - 各工具配置项

    - 除了各个内置的工具按钮外，还可以自定义工具按钮

    - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#toolbox.feature)

    - 内置工具

      - `saveAsImage`

        - 导出图片

      - `restore`

        - 重置

      - `dataView`

        - 数据视图

      - `dataZoom`

        - 数据区域缩放
        - 目前只支持直角坐标系的缩放

      - `magicType`

        - 图表类型切换

        - 示例

          ~~~javascript
          feature: {
              magicType: {
                  type: ['line', 'bar', 'stack', 'tield']
              }
          }
          ~~~

      - `brush`

        - 选框组件的控制按钮
        - 也可以不在这里指定，而是在 [brush.toolbox](https://echarts.apache.org/zh/option.html#brush.toolbox) 中指定

#### `visualMap`

- 视觉映射组件

- 用于进行视觉编码，也就是将数据映射到视觉元素

- [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#visualMap)

- 视觉元素可以是

  - `symbol`: 图元的图形类别
  - `symbolSize`: 图元的大小
  - `color`: 图元的颜色
  - `colorAlpha`: 图元的颜色的透明度
  - `opacity`: 图元以及其附属物（如文字标签）的透明度
  - `colorLightness`: 颜色的明暗度，参见 [HSL](https://en.wikipedia.org/wiki/HSL_and_HSV)
  - `colorSaturation`: 颜色的饱和度，参见 [HSL](https://en.wikipedia.org/wiki/HSL_and_HSV)
  - `colorHue`: 颜色的色调，参见 [HSL](https://en.wikipedia.org/wiki/HSL_and_HSV)

- `visualMap` 组件可以定义为**分段型**（`visualMapPiecewise`）或**连续型**（`visualMapContinuous`），通过 `type` 属性来指定

- 视觉映射方式的配置

  - `visualMap` 中可以指定数据的哪个**维度**（通过 `visualMap.dimension` 指定）映射到哪些**视觉元素**（通过 `visualMap.inRange` 和 `visualMap.outOfRange`）指定

  - 在 `visualMap` 组件所控制的 `series` 中，如果 `series` 中某个数据项需要避开 `visualMap` 映射，可以这么配置

    ~~~javascript
    series: {
        type: '...',
        data: [
            {name: 'Shanghai', value: 251},
            {name: 'Haikou', value: 21},
            // 设置 `visualMap: false` 则 visualMap 不对此项进行控制，此时系列
            // 可使用自身的视觉参数（color/symbol/ ...控制此项的显示。
            {name: 'Beijing', value: 821, visualMap: false},
            ...
        ]
    }
    ~~~

##### `continuous` 类型与 `piecewise` 类型的区别

- `continuous` 类型的 `visualMap` 映射完后的数据是连续的
  - 只能将连续的数据映射进来
  - 其交互组件是一个连续的滑动条
    - 在滑动条上可以选择一段范围，只有选中范围内的数据对应的图形才会被视觉映射，其它图形会被淡化
    - 在滑动条上选中一个点后，可以将该点一定范围内匹配到的数据高亮（`emphasis`）显示
- `piecewise` 类型的 `visualMap` 映射完后的数据是离散的
  - 可以将离散的数据映射进来，也可以将连续数据分段后，一段一段地映射进来
  - 其交互组件是一组离散的图例图形
    - 每个图例对应一个离散数据或一段连续数据
    - 可以选择不同的单个图例或多个图例进行突出显示

##### 常用属性（通用）

- `show`
  - 是否显示交互组件
  - 如果为 `false`，则不显示交互组件，但是数据映射的功能还在

- `type`
  - 可选值
    - `'continuous'` 连续型
    - `'piecewise'` 分段型
- `precision`
  - 数据展示的小数精度
  - 除了 `piecewise` 类型下使用了**连续型数据平均分段**模式时精度会根据数据自动适应，其余情况下的精度默认为 `0`，即没有小数
- `dimension`
  - 指定使用数据的哪个维度映射到视觉元素上
  - 默认取最后一个维度
- `seriesIndex`
  - 指定取哪个系列的数据
  - 默认取所有系列
- `hoverLink`
  - 鼠标悬浮到交互组件上时，对应的图形是否高亮显示
  - 在 `continuous` 类型下，当鼠标悬浮到图表图形中时，交互组件的响应位置也会提示对应的位置和数值
  - 默认开启
- `formatter`
  - 标签文本的格式化工具
  - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#visualMap-continuous.formatter)

##### 交互组件样式属性（通用）

- `orient`
  - 如何放置 `visualMap` 交互组件，水平（`'horizontal'`）或者竖直（`'vertical'`）
  - 默认值为 `'vertical'`
- `textStyle`
  - 标签文本样式配置
  - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#visualMap-continuous.textStyle)
- `text`
  - 图例列表两端的文本组成的数组
  - 常用的如 `['High', 'Low']`
  - 可以被 `inverse` 反转两端
- `inverse`
  - 是否反转 `visualMap` 组件
  - 值为 `fale` 时，数据大小的位置规则与直角坐标系相同，即
    - 当 [visualMap.orient](https://echarts.apache.org/zh/option.html#visualMap.orient) 为 `'vertical'` 时，数据上大下小
    - 当 [visualMap.orient](https://echarts.apache.org/zh/option.html#visualMap.orient) 为 `'horizontal'` 时，数据右大左小
  - 值为 `true` 时则相反
- `padding`、`backgroundColor`、`borderColor`、`borderWidth`

##### `continuous` 下的属性

- `min`、`max`
  - 指定视觉映射允许的数据最小值和最大值，必须由用户指定

- `range`

  - 指定手柄对应数值的位置的范围，用数组表示

  - `range` 应在 `min` `max` 范围内

  - 默认值为 `[visualMap.min, visualMap.max]`

  - 例

    ~~~javascript
    chart.setOption({
        visualMap: {
            min: 0,
            max: 100,
            // 两个手柄对应的数值是 4 和 15
            range: [4, 15],
            ...
        }
    })
    ~~~

  - 当使用 `setOption` 改变 `min`、`max` 时，`range` 的自适应

    - 如果 `range` 没有指定，则改变 `min`、`max` 后，`range` 重新取改变后的默认值

    - 如果 `range` 指定了具体值，例

      ~~~javascript
      chart.setOption({visualMap: {min: 10, max: 300, range: [20, 80]}}); // 设置了 range
      
      chart.setOption({visualMap: {min: 0, max: 400}}); // 再次使用 setOption 改变 min、max。
      // 此时 range 不会改变而仍维持本来的数值，仍为 [20, 80]。
      
      chart.setOption({visualMap: {range: null}}); // 再把 range 设为 null。
      // 则 range 恢复为 [min, max]，即 [0, 400]，同时也恢复了自动随 min max 而改变的能力。
      ~~~

- `calculable`

  - 是否显示拖拽用的手柄（手柄用以调整选中范围）

- `realtime`

  - 拖拽时是否实时更新

###### 交互组件样式属性

- `itemWidth`、`itemHeight`
  - 滑动条的宽高
- `align`
  - 交互组件中手柄和标签文本摆放位置
  - 可选值
    - `'auto'` 自动决定。
    - `'left'` 手柄和label在右，`orient` 为 `horizontal` 时有效
    - `'right'` 手柄和label在左，`orient` 为 `horizontal` 时有效
    - `'top'` 手柄和label在下，`orient` 为 `vertical` 时有效
    - `'bottom'` 手柄和label在上，`orient` 为 `vertical` 时有效
- `textGap`
  - 两端文字与滑动条之间的距离，单位为 `px`
  - 默认为 `10`
- `handleIcon`
  - 两端手柄的形状、指示器的形状
  - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#visualMap-continuous.handleIcon)
  - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#visualMap-continuous.indicatorIcon)
- `handleSize`、`indicatorSize`
  - 手柄的大小、指示器大小
  - 可以是相对于组件尺寸的百分比大小
- `handleStyle`、`indicatorStyle`
  - 手柄样式配置、指示器样式配置
  - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#visualMap-continuous.handleSize)

##### `piecewise` 下的属性

- `min`、`max`
  - 指定视觉映射允许的数据最小值和最大值
  - 在**连续型数据自定义分段模式**，即设置了 `pieces` 属性时，不需要指定 `min`、`max`
  - 在**连续型数据平均分段模式**，即设置了 `splitNumber` 属性时，需指定 `min`、`max`，如果不指定，则默认为 `[0, 200]`（注意并不是默认为 `series.data` 的 `dataMin` 和 `dataMax`）
- `minOpen`、`maxOpen`
  - 使用了 `splitNumber` 时会有效
  - 当值为 `true` 时，分段会多出 `<min` 和 `>max` 的部分，这里的 `min` 和 `max` 指代它们的值

- `splitNumber`

  - 对于连续型数据，自动平均切分成几段
  - 默认为 `5` 段，连续的数据范围需要 `max` 和 `min` 来指定
  - 如果设置了 [visualMap-piecewise.pieces](https://echarts.apache.org/zh/option.html#visualMap-piecewise.pieces) 或者 [visualMap-piecewise.categories](https://echarts.apache.org/zh/option.html#visualMap-piecewise.categories)，则 `splitNumber` 无效

- `pieces`

  - 对于连续型数据，自定义切分后的每一段的范围，也可以设定每一段的 `label` 标签文本和每一段的自定义样式，例

    ~~~javascript
    pieces: [
        {min: 1500}, // 不指定 max，表示 max 为无限大（Infinity）。
        {min: 900, max: 1500},
        {min: 310, max: 1000},
        {min: 200, max: 300},
        {min: 10, max: 200, label: '10 到 200（自定义label）'},
        {value: 123, label: '123（自定义特殊颜色）', color: 'grey'}, // 表示 value 等于 123 的情况。
        {max: 5}     // 不指定 min，表示 min 为无限大（-Infinity）。
    ]
    ~~~

  - 或者，更精确得，可以使用 `lt`（小于，`less than`），`gt`（大于，`greater than`），`lte`（小于等于 `less than or equals`），`gte`（大于等于，`greater than or equals`）来表达边界

    ~~~javascript
    pieces: [
        {gt: 1500},            // (1500, Infinity]
        {gt: 900, lte: 1500},  // (900, 1500]
        {gt: 310, lte: 1000},  // (310, 1000]
        {gt: 200, lte: 300},   // (200, 300]
        {gt: 10, lte: 200, label: '10 到 200（自定义label）'},       // (10, 200]
        {value: 123, label: '123（自定义特殊颜色）', color: 'grey'},  // [123, 123]
        {lt: 5}                 // (-Infinity, 5)
    ]
    ~~~

- `categories`

  - 用于表示离散型数据（或可以称为类别型数据、枚举型数据）的全集

  - 用一个数组表示，如

    ~~~javascript
    categories: ['严重污染', '重度污染', '中度污染', '轻度污染', '良', '优'],
    ~~~

- `selectedMode`

  - 选择图表图形的模式
  - 可选值
    - `'multiple'`（多选）
    - `'single'`（单选）
    - `true`（单选）从 `v5.3.3` 版本支持
    - `false`（禁止选择）从 `v5.3.3` 版本支持

###### 交互组件样式属性

- `itemWidth`、`itemHeight`
  - 图例图形的宽高

- `align`
  - 图例和标签文本的摆放关系
  - 可选值为
    - `'auto'`
    - `left`
    - `'right'`
- `textGap`
  - 图例与标签文本的间距，单位为 `px`
  - 默认为 `10`
- `showLabel`
  - 是否显示每项文本的标签
  - 默认情况为，当设置了 `text` 时，`label` 默认不显示（为了兼容 `ECharts 2`），否则默认显示
- `itemGap`
  - 两个图例之间的间隔距离，单位为 `px`
  - 默认为 `10`
- `itemSymbol`
  - 图例图形
  - 默认为 `roundRect`

##### 视觉元素

###### 视觉元素映射属性

- `inRange`

  - 定义在选中范围中的视觉元素

  - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#visualMap-piecewise.inRange)

  - `inRange` 能定义目标系列的视觉形式，也定义了 `visualMap` 本身的视觉样式

    - 举例来说，如果 `visualMap` 控制的是散点图，那么 `inRange` 同时定义了散点图的颜色、尺寸等，也定义了 `visualMap` 本身的颜色、尺寸等

  - 定义方式示例

    - 同时定义目标系列和 `visualMap` 本身的视觉样式

      ~~~javascript
      visualMap: [
          {
              ...,
              inRange: {
                  color: ['#121122', 'rgba(3,4,5,0.4)', 'red'],
                  symbolSize: [30, 100]
              }
          }
      ]
      ~~~

    - 分别定义目标系列和 `visualMap` 本身的视觉样式

      ~~~javascript
      visualMap: [
          {
              ...,
              // 表示 目标系列 的视觉样式。
              target: {
                  inRange: {
                      color: ['#121122', 'rgba(3,4,5,0.4)', 'red'],
                      symbolSize: [60, 200]
                  }
              },
              // 表示 visualMap 本身的视觉样式。
              controller: {
                  inRange: {
                      symbolSize: [30, 100]
                  }
              }
          }
      ]
      
      // 也可以这样定义
      visualMap: [
          {
              ...,
              // 表示 目标系列 的视觉样式 和 visualMap-continuous 共有的视觉样式。
              inRange: {
                  color: ['#121122', 'rgba(3,4,5,0.4)', 'red'],
                  symbolSize: [60, 200]
              },
              // 表示 visualMap-continuous 本身的视觉样式，会覆盖共有的视觉样式。比如，symbolSize 覆盖成为 [30, 100]，而 color 不变。
              controller: {
                  inRange: {
                      symbolSize: [30, 100]
                  }
              }
          }
      ]
      ~~~

  - 如果没有指定 `inRange`，则会默认生效一个 `color` 视觉元素映射，如果不想要，可以设置 `inRange: {color: null}` 来去除

- `outOfRange`

  - 定义在交互组件选中范围外的视觉元素
  - 配置方式同 `inRange`

- 关于视觉通道

  - `inRange` 中，可以有任意几个的**视觉通道**定义，如 `color`、`symbolSize` 等，这些视觉通道会被同时采用
  - 一般来说，使用透明色时，建议使用**透明度**（`opacity`），而**非颜色透明度**（`colorAlpha`），前者能同时控制图元中附属物（如 `label`）的透明度，而后者只能控制图元本身颜色的透明度
  - 视觉映射的方式有两种：线性映射、查表映射

###### 线性映射

- 表示 `data` 中的每个值（`dataValue`）都会经过线性映射计算，从 `[visualMap.min, visualMap.max]` 映射到设定的 `[visual value 1, visual value 2]` 区间中的某一个视觉的值（下称 `visual value`，即视觉通道的值）
- 举例
  - 设置了 `[visualMap.min, visualMap.max]` 为 `[0, 100]`，想要将 `series.data: [50, 10, 100]` 映射到范围为 `[0.4, 1]` 的 `opacity` 上，从而达到使用透明度表达数值大小的目的
    - 那么 `visualMap` 组件会对 `series.data` 中的每一个 `dataValue` 做线性映射计算，得到一个 `opacityValue`
    - 最终得到的 `opacityValues` 为 `[0.7, 0.96, 0.4]`
  - 另外，`[visual value 1, visual value 2]` 区间不一定是一个正增长区间，也可以是负增长区间，使得数据值越大，视觉元素的值越小
    - 例如上面例子中的 `opacity` 映射范围为 `[1, 0.4]`
    - 那么 `visualMap.min: 0` 会映射到 `opacity: 1`，`visualMap.max: 100` 会映射到 `opacity: 0.4`
    - 那么 `series.data: [50, 10, 100]` 映射到 `opacityValues` 就是 `[0.7, 0.44, 1]`
- 以下情况时，会设定为线性映射
  - 当 `visualMap` 类型为 `continuous` 时
  - 当 `visualMap` 类型为 `piecewise` 且未设置 `visualMap.categories` 时
- 视觉通道的值（`visual value`）
  - 视觉通道的值一般都以数组形式表示，例如：`color: ['#333', '#777']`
  - 视觉通道的值的映射方式
    - 对于 `symbolSize`、`opacity`、`colorAlpha`、`colorLightness`、`colorSaturation`、`colorHue` 视觉元素，它们的值直接和数值对应映射
    - 对于 `color` 视觉元素，它的数组表示比如 `['#333', '#78ab23', 'blue']`，意思就是以这三个点作为基准，形成一种『渐变』的色带，数据映射到这个色带上
      - 也就是说，与 `visualMap.min` 相等的值会映射到 `'#333'`，与 `visualMap.max` 相等的值会映射到 `'blue'`
      - 对于 `visualMap.min` 和 `visualMap.max` 中间的其他点，以所给定的 `'#333'`, `'#78ab23'`, `'blue'` 这三个颜色作为基准点进行分段线性插值，得到映射结果
    - 对于 `symbol` 视觉元素，它的数组表示比如 `['circle', 'rect', 'diamond']`
      - 与 `visualMap.min` 相等的值会映射到 `'circle'`，与 `visualMap.max` 相等的值会映射到 `'diamond'`
      - 对于 中间的其他点，会根据他们和 `visualMap.min` 和 `visualMap.max` 的数值距离，映射到 `'circle'`, `'rect'`, `'diamond'` 中某个值上
  - 视觉通道的值的取值范围
    - `opacity`、`colorAlpha`、`colorLightness`、`colorSaturation`、`symbolSize`
      - 取值范围是 `[0, 1]`
    - `colorHue`
      - 取值范围是 `[0, 360]`
    - `color`
      - `rba`、`rgba`、十六进制格式都可
    - `symbol`
      - 同各组件中的 `symbol`

###### 查表映射

- 表示 `series.data` 中的所有值（`dataValue`）都是可枚举的，会根据给定的映射表得到映射结果

- 举例

  - 设置了 `visualMap.categories` 为 `['Demon Hunter', 'Blademaster', 'Death Knight', 'Warden', 'Paladin']`，有 `series.data: ['Demon Hunter', 'Death Knight', 'Warden', 'Paladin']` 想要映射
    - 那么可以定立查表映射规则 `color: {'Warden': 'red', 'Demon Hunter': 'black'}`
    - 于是 `visualMap` 组件会按照表来将 `dataValue` 映射到 `color`

- 当 `visualMap` 类型为 `piecewise` 且设置了 `visualMap.categories` 时，会进行查表映射

- 视觉通道的值一般使用对象或数组表示，例如

  ~~~javascript
  visualMap: {
      type: 'piecewise',
      // categories 定义了 visualMap-piecewise 组件显示出来的项。
      categories: [
          'Demon Hunter', 'Blademaster', 'Death Knight', 'Warden', 'Paladin'
      ],
      inRange: {
          // visual value 可以配成 Object：
          color: {
              'Warden': 'red',
              'Demon Hunter': 'black',
              '': 'green' // 空字串，表示除了'Warden'、'Demon Hunter'外，都对应到 'green'。
          }
          // visual value 也可以只配一个单值，表示所有都映射到一个值，如：
          color: 'green',
          // visual value 也可以配成数组，这个数组须和 categories 数组等长，
          // 每个数组项和 categories 数组项一一对应：
          color: ['red', 'black', 'green', 'yellow', 'white']
      }
  }
  ~~~

###### 修改视觉编码

- 如果在图表被渲染后（即已经使用 `setOption` 设置了初始 `option` 之后），想修改 visualMap 的各种 `视觉编码`，按照惯例，再次使用 `setOption` 即可，例如

  ~~~javascript
  chart.setOption({
      visualMap: {
          inRange: {color: ['red', 'blue']}
      }
  });
  ~~~

- 需要注意的是

  - `visualMap` 中的这几个属性，`inRange`, `outOfRange`, `target`, `controller`，在 `setOption` 时不支持 `merge`，否则会带来过于复杂的 `merge` 逻辑，因此一旦修改了以上几个属性中的一项，其它项也会被清空，而非保留当前状态

  - 也不推荐使用`getOption -> 修改option -> setOption` 的方式，如

    ~~~javascript
    // 不推荐这样做（尽管也能达到和上面的例子相同的结果）：
    var option = chart.getOption(); // 获取所有option。
    option.visualMap.inRange.color = ['red', 'blue']; // 改动color（我想要改变 color）。
    
    // 如下两处也要进行同步改动，否则可能达不到期望效果。
    option.visualMap.target.inRange.color = ['red', 'blue'];
    option.visualMap.controller.inRange.color = ['red', 'blue'];
    
    chart.setOption(option); // option设置回 visualMap
    ~~~

###### 视觉元素映射属性之间的关系

- `inRange` 和 `outRange` 属性分别用来配置选中范围内的和选中范围外的视觉元素映射

- `inRange` 和 `outRange` 中分别有 `target` 和 `controller` 属性来配置图表图形的视觉元素和交互组件的控制元素

  - 其中 `target` 下的属性可以直接设置到 `inRange` 和 `outRange` 中

- `conntroller` 属性也可以直接定义在 `visualMap` 下

  - 其中分别可以定义 `inRange` 和 `outRange` 属性来配置交互组件的视觉元素

    

### 样式相关组件

#### `color`

- 调色盘组件

- 值为一个颜色列表

- 如果系列没有设置颜色，则会依次循环从该列表中取颜色作为系列颜色

- 默认为

  ~~~javascript
  ['#5470c6', '#91cc75', '#fac858', '#ee6666', '#73c0de', '#3ba272', '#fc8452', '#9a60b4', '#ea7ccc']
  ~~~

- 支持使用 `rgb`、`rgba`、十六进制表示的颜色

- 渐变色或纹理填充示例

  ~~~javascript
  // 线性渐变，前四个参数分别是 x0, y0, x2, y2, 范围从 0 - 1，相当于在图形包围盒中的百分比，如果 globalCoord 为 `true`，则该四个值是绝对的像素位置
  {
    type: 'linear',
    x: 0,
    y: 0,
    x2: 0,
    y2: 1,
    colorStops: [{
        offset: 0, color: 'red' // 0% 处的颜色
    }, {
        offset: 1, color: 'blue' // 100% 处的颜色
    }],
    global: false // 缺省为 false
  }
  // 径向渐变，前三个参数分别是圆心 x, y 和半径，取值同线性渐变
  {
    type: 'radial',
    x: 0.5,
    y: 0.5,
    r: 0.5,
    colorStops: [{
        offset: 0, color: 'red' // 0% 处的颜色
    }, {
        offset: 1, color: 'blue' // 100% 处的颜色
    }],
    global: false // 缺省为 false
  }
  // 纹理填充
  {
    image: imageDom, // 支持为 HTMLImageElement, HTMLCanvasElement，不支持路径字符串
    repeat: 'repeat' // 是否平铺，可以是 'repeat-x', 'repeat-y', 'no-repeat'
  }
  ~~~

- [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#color)

#### `backgroundColor`

- 背景色
- 默认为无背景，即 `'transparent'` 透明色
- 支持使用 `rgb`、`rgba`、十六进制表示的颜色
- 也支持渐变色和纹理填充

### 坐标系相关

#### 坐标轴组件通用属性

##### 通用属性

- `type`
  - 坐标轴类型
  - 可选值
    - `'value'`
      - 数值轴
      - 适用于连续数据
    - `'category'`
      - 类目轴
      - 适用于离散的类目数据
      - 为该类型时类目数据可自动从 `series.data` 或 `dataset.source` 中取值，或者可通过坐标轴的 `data` 属性设置类目数据
    - `'time'`
      - 时间轴
      - 适用于连续的时序数据
      - 与数据轴相比，时间轴带有时间的格式化，在刻度计算上也有所不同，例如会根据跨度范围来决定使用月、星期、日还是小时范围的刻度
    - `'log'`
      - 对数轴
      - 适用于对数数据
      - 对数轴上相邻两个相等间距所代表的范围的比值是常数
      - 例如在一个 `10` 为底的对数轴上，数值 `1` 到 `10` 的距离和数值 `10` 到 `100` 的距离是相等的
- `logBase`
  - 对数轴的底数，只在对数轴中生效
- `name`
  - 坐标轴名称
- `nameLocation`
  - 坐标轴名称显示位置
  - 可选值
    - `'start'`
    - `'middle'` 等同于 `'center'`
    - `'end'` 
- `nameTextStyle`
  - 坐标轴名称的文字样式
  - 属性
    - `color`
      - 坐标轴名称的颜色
      - 默认取 `axisLine.lineStyle.color`
    - `fontStyle`
      - 可选值
        - `'normal'`（默认值）
        - `'italic'`
        - `'oblique'`
    - `fontWeight`
      - 可选值
        - `'normal'`（默认值）
        - `'bold'`
        - `'bolder'`
        - `'lighter'`
        - `100`、`200`、`300` ...
    - `fontFamily`
    - `fontSize`
      - 默认为 `12`
  - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#xAxis.nameTextStyle)

- `min`、`max`

  - 坐标轴刻度的最小值、最大值
  - 如果是类目轴中，则代表类目的序数，可以设置为负数
  - 也可以设置成一个函数
    - 该函数接收一个 `value` 对象，包含 `min`、`max` 属性，分别表示数据的最小值和最大值
    - 该函数的返回值代表 `min`、`max` 的取值
    - 如果返回 `null` 或 `undefined` 则代表自动计算取值（从 `v4.8.0` 开始支持）

- `axisLine`、`axisTick`、`axiaLabel`

  - 坐标轴线、坐标轴刻度线、坐标轴标签（刻度值）相关

- `splitLine`、`splitArea`

  - 坐标轴刻度在坐标系中的分隔线、分隔区域

- `bondaryGap`

  - 坐标轴两边的留白策略

  - 类目轴和非类目轴的设置和表现不一样

  - 类目轴中 `bondaryGap` 的取值为布尔值

    - 默认为 `true`，此时刻度是作为分割线，标签和数据点会在两个**刻度**（`axisTick`）之间的**带**（`band`）中间
    - 如果设为 `false`，则标签和数据点中心会与刻度对齐

  - 非类目轴中，`bondaryGap` 是一个有两个项的数组

    - 数组项分别表示坐标轴两边在数据最小值和最大值之外的延伸范围

    - 可以直接设置数值或相对百分比

    - 优先级低于坐标轴的 `min` 和 `max` 属性

    - 例

      ~~~javascript
      // 表示坐标轴在数据最小值和最大值之外延伸 20%
      boundaryGap: ['20%', '20%']
      ~~~

- `inverse`

  - 是否反转坐标轴
  - 默认为 `false`

##### 动画相关

- `animation`

  - 是否开启动画
  - 默认为 `true`

- `animationThreshold`

  - 开启动画的阈值
  - 接收一个数字，当单个系列显示的图形数量大于这个阈值时会关闭动画

- `animationDuration`、`animationDelay`

  - 初始动画的时长、延迟

  - 可以接收数字，单位为 `ms`

  - 默认值分别为 `1000 ` 和 `0`

  - 也可以接收一个回调函数，接收一个 `idx` 参数表示数据序号，可以通过每个数据返回不同的时长实现更戏剧的初始动画效果，例

    ~~~javascript
    {
        animationDuration: function (idx) {
            // 越往后的数据时长越大
            return idx * 100;
        },
        animationDelay: function (idx) {
            // 越往后的数据时长越大
            return idx * 100;
        }
    }
    ~~~

- `animationEasing`

  - 初始动画的缓动效果
  - 接收内置的缓动效果字符串
  - 默认为 `'cubicOut'`
  - 可以参考 [缓动示例](https://echarts.apache.org/examples/zh/editor.html?c=line-easing)

- `animationDurationUpdate`、`animationDelayUpdate`

  - 数据更新动画的时长、延迟
  - 默认值分别为 `300` 和 `0`，单位为 `ms`
  - 支持数值和回调函数

- `animationEasingUpdate` 

  - 数据更新动画的缓动效果
  - 默认为 `'cubicInOut'`

##### 不同轴类型的专有属性

- `logBase`
  - 对数轴的底数，只在对数轴中有效
- `data`
  - 类目数据，只在类目轴中有效
  - 设置了轴类型为类目轴，但没有设置该属性，则该属性的内容会自动从 `series.data` 或 `dataset.source` 中获取
  - 接收一个数组，数组中的每一项代表一个类目对象，包含以下属性
    - `value`
      - 类目名称
    - `textStyle`
      - 设置文字样式
      - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#xAxis.data.textStyle)
- `scale`
  - 开启坐标轴缩放，只在数值轴中有效
  - 默认为 `false`
  - 如果为 `true`，则如果该轴数值范围中不包括 `0` 值，且距离 `0` 值相对较远，则会尝试开启缩放，将最小和最大的数据值作为坐标轴的轴标区间，这意味着坐标轴可能会不包含 `0` 刻度
- `splitNumber`
  - 坐标轴的分割段数
  - 在类目轴中无效
  - 需要注意的是这个分割段数只是个预估值，最后实际显示的段数会在这个基础上根据分割后坐标轴刻度显示的易读程度作调整
  - 默认为 `5`
- `minInterval`
  - 坐标轴的最小间隔大小
  - 只在数值轴或时间轴中有效
  - 例如可以设置为 `1` 以保证坐标轴分割刻度显示成整数
- `maxInterval`
  - 坐标轴的最大间隔大小
  - 只在数值轴或时间轴中有效
  - 例如在时间轴中可以设置为 `3600 * 24 * 1000` 以保证坐标轴分割刻度最大为一天
- `interval`
  - 强制设置坐标轴分割间隔
  - 无法在类目轴中使用，在时间轴中需要传时间戳，在对数轴中需要传指数值

#### 直角坐标系相关组件

##### `grid`

- 直角坐标系内绘图网格组件
- 一般情况下单个 `grid` 内最多可以放置上下两个 `X` 轴，左右两个 `Y` 轴
- 可以在 `grid` 上绘制折线图、柱状图、散点图（气泡图）、饼图等
- 在 `ECharts 2` 中单个 `echarts` 实例中最多只能存在一个 `grid` 组件，`ECharts 3` 以后可以存在任意个 `grid` 组件，使用数组表示多个 `grid`
  - 如果一个图表中存在多个 `grid`，则 `xAxis` 和 `yAxis` 使用 `gridIndex` 属性指定它们所在的 `grid` 的索引

- 常用属性
  - `left`、`top`、`right`、`bottom`
    - `grid` 组件离容器左侧、上侧、右侧、下侧的距离
    - 可以是 `20` 这样的具体像素值，也可以是 `'20%'` 这样的容器高宽百分比
    - 默认值分别为
      - `left: '10%'`
      - `top: 60` 
        - 这个值是根据图例 `legend` 处理结果后决定的
      - `right: '10%'`
      - `bottom: 30`
        - 这个值是根据 `X` 轴处理结果后决定的
  - `width`、`height`
    - 组件的宽高
    - 默认值为 `'auto'`，即自适应
    - 可以是 `20` 这样的具体像素值，也可以是 `'20%'` 这样的容器高宽百分比
  - `borderColor`
    - 网格范围的外边框颜色
    - 默认为 `'#ccc'`
  - `borderWidth`
    - 网格的外边框线宽
    - 默认为 `1`
  - `backgroundColor`
    - 网格的背景色
    - 默认为 `'transparent'`
  - `containLabel`
    - `grid` 区域是否包含坐标系的刻度标签
    - 值为 `false` 时（默认值）
      - `grid.left` `grid.right` `grid.top` `grid.bottom` `grid.width` `grid.height` 决定的是由坐标轴形成的矩形的尺寸和位置
      - 这比较适用于多个 `grid` 进行对齐的场景，因为往往多个 `grid` 对齐的时候，是依据坐标轴来对齐的
    - 值为 `true` 时
      - `grid.left` `grid.right` `grid.top` `grid.bottom` `grid.width` `grid.height` 决定的是包括了坐标轴标签在内的所有内容所形成的矩形的位置
      - 这常用于『防止标签溢出』的场景，标签溢出指的是，标签长度动态变化时，可能会溢出容器或者覆盖其他组件

##### `xAxis`、`yAxis`

- 直角坐标系中的 `X` 轴、`Y` 轴
- 一般情况下单个 `grid` 内最多可以放置上下两个 `X` 轴，左右两个 `Y` 轴，如果要放置更多，需要通过配置其 `offset` 属性防止同个位置多个轴重叠
- `xAxis` 默认是类目轴，`yAxix` 默认是数值轴

- 专有属性
  - `gridIndex`
    - 所在的 `grid` 的索引，默认为 `0`，即使用第一个 `grid`

#### 极坐标系相关组件

##### `polar`

- 极坐标系组件
- 可用于散点图和折线图
- 每个极坐标系拥有一个径向轴和角度轴
  - 如果一个图表中存在多个 `polar`，则 `radiusAxis` 和 `angleAxis` 使用 `polarIndex` 属性指定它们所在的 `polar` 的索引
- 常用属性
  - `center`
    - 极坐标系中心坐标
    - 接收一个数组，数组第一项是横坐标，第二项是纵坐标
    - 默认值 `['50%', '50%']`
    - 支持使用绝对像素值和百分比
  - `radius`
    - 极坐标系半径
    - 取值类型
      - `number`
        - 直接指定外半径的值
      - `string`
        - 百分比表示
        - 例如 `'20%'` 表示外半径为可视区尺寸（容器高宽中较小的一项）的 `20%` 长度
      - 数组
        - 数组第一项是内半径，第二项是外半径

##### `radiusAxis`

- 极坐标系的径向轴

##### `angleAxis`

- 极坐标系的角度轴
- 常用属性
  - `startAngle`
    - 起始刻度的角度
    - 正数代表逆时针反向
    - 单位为度，默认为 `90` 度，即圆心的正上方。`0` 度为圆心的正右方
  - `clockwise`
    - 刻度增长是否按顺时针
    - 默认为 `true`

#### 雷达坐标系相关组件

##### `radar`

- 雷达坐标系组件

- 只适用于雷达图

- 在 `ECharts 2` 中，`polar` 组件用于绘制雷达图，从 `ECharts 3` 开始 `polar` 被重构为标准的极坐标组件，为避免混淆，雷达图使用 `radar` 作为其坐标系

- 雷达坐标系的轴

  - 与极坐标系不同，它不使用某个坐标轴组件作为轴
  - `radar` 坐标系使用 `indicator` 指示器作为轴，每个轴都是一个单独的维度
  - 在 `radar` 组件的 `indicator` 属性中定义指示器坐标轴，通过 [nameGap](https://echarts.apache.org/zh/option.html#radar.nameGap)、[axisLine](https://echarts.apache.org/zh/option.html#radar.axisLine)、[axisTick](https://echarts.apache.org/zh/option.html#radar.axisTick)、[axisLabel](https://echarts.apache.org/zh/option.html#radar.axisLabel)、[splitLine](https://echarts.apache.org/zh/option.html#radar.splitLine)、 [splitArea](https://echarts.apache.org/zh/option.html#radar.splitArea) 几个配置项配置指示器坐标轴线的样式

- 常用属性

  - `center`

    - 同 `polar.center`

  - `radius`

    - 同 `polar.radius`

  - `startAngle`

    - 同 `angleAxis.startAngle`

  - `clockwise`

    - 同 `angleAxis.clockwise`

  - `axisName`

    - 雷达图中指示器名称的配置对象

    - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#radar.axisName)

    - 常用属性

      - `show`

      - `formatter`

        - 指示器名称显示的格式器，支持字符串和回调函数，例

          ~~~javascript
          // 使用字符串模板，模板变量为指示器名称 {value}
          formatter: '【{value}】'
          // 使用回调函数，第一个参数是指示器名称，第二个参数是指示器配置项
          formatter: function (value, indicator) {
              return '【' + value + '】';
          }
          ~~~

  - `shape`

    - 雷达绘制类型
    - 可选值
      - `'polygon'`（默认值）
      - `'circle'`

  - `indicator`

    - 雷达图指示器，用来指定雷达图中的多个变量（维度），例

      ~~~javascript
      indicator: [
         { name: '销售（sales）', max: 6500},
         { name: '管理（Administration）', max: 16000, color: 'red'}, // 标签设置为红色
         { name: '信息技术（Information Techology）', max: 30000},
         { name: '客服（Customer Support）', max: 38000},
         { name: '研发（Development）', max: 52000},
         { name: '市场（Marketing）', max: 25000}
      ]
      ~~~

    - 是一个数组，每个数组项都是一个指示器

    - 常用属性

      - `name`
        - 该项指示器名称
      - `max`
        - 该项指示器最大值
        - 可选，建议设置
      - `min`
        - 该项指示器最小值
        - 默认为 `0`
      - `color`
        - 该项指示器标签文本的颜色

#### 地理坐标系相关组件

##### `geo`

- 地理坐标系组件

- 用于地图的绘制，支持在地理坐标系上绘制散点图、线集

- 地图注册

  - 使用 [GeoJSON](http://geojson.org/) 或 `SVG` 格式的数据作为地图轮廓
  - 使用 `echarts.registerMap(mapName[, opt])` 注册地图数据
    - 接收参数
      - `mapName`
        - 地图名称
      - `opt` 可选配置选项，属性有
        - `geoJSON` / `geoJson`
          - `GeoJSON` 格式的数据
          - 可以是 `JSON` 字符串，也可以是解析得到的对象
        - `svg`
          - 从 `v5.1.0` 开始支持 `SVG` 格式的数据
          - 可以是 `SVG` 字符串，也可以是解析得到的 `SVG DOM` 对象
        - `specialAreas`
          - 将地图中的部分区域缩放到合适位置
          - 只在 `GeoJSON` 中生效，`SVG` 中不生效

- 常用属性

  - `map`

    - 使用注册过的地图名称

  - `roam`

    - 是否开启鼠标缩放和平移漫游
    - 默认为 `false`
    - 布尔值代表都开启或者都不开启
    - 可以指定开启其中一项
      - `'scale'` 代表只开启缩放
      - `'move'` 代表只开启平移

  - `projection`

    - 自定义地图投影
    - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#geo.projection)

  - `center`

    - 当前视角的中心点

    - 默认使用原始坐标（表示经纬度）

    - 如果设置了 `projection` 则使用投影后的坐标表示

    - 示例

      ~~~javascript
      center: [115.97, 29.71]
      ~~~

      ~~~javascript
      projection: {
          projection: (pt) => project(pt)
      },
      center: project([115.97, 29.71])
      ~~~

  - `aspectScale`

    - 用于设置地图的长宽比
    - 如果设置了 `projection` 则无效
    - 默认为 `0.75`
    - 最终的 `aspect` 的计算方式是：`geoBoundingRect.width / geoBoundingRect.height * aspectScale`

  - `boundingCoords`

    - 二维数组

    - 定义定位的左上角以及右下角分别所对应的经纬度

    - 例

      ~~~javascript
      // 设置为一张完整经纬度的世界地图
      map: 'world',
      left: 0, top: 0, right: 0, bottom: 0,
      boundingCoords: [
          // 定位左上角经纬度
          [-180, 90],
          // 定位右下角经纬度
          [180, -90]
      ]
      ~~~

  - `zoom`

    - 当前视角的缩放比例
    - 默认为 `1`

  - `scaleLimit`

    - 一个配置对象，用于配置滚轮缩放的极限控制
    - 通过 `scaleLimit.min` 和 `scaleLimit.max` 限制最小和最大的缩放值

  - `nameMap`

    - 自定义地区的名称映射

    - 将原始数据集中的地名映射成自定义名称

    - 例

      ~~~javascript
      nameMap: {
          'China': '中国',
          'United States': '美国'
      }
      ~~~

  - `nameProperty`

    - 针对 `GeoJSON` 的自定义属性名称
    - 默认是 `'name'`，即选择 `GeoJSON` 数据点中的 `name` 字段作为它的名字

  - `regions`

    - 在地图中对特定的区域配置样式

    - 例

      ~~~javascript
      regions: [{
          name: '广东',
          itemStyle: {
              areaColor: 'red',
              color: 'red'
          }
      }]
      ~~~

    - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#geo.regions)

  - `itemStyle`

    - 地图区域的多边形图形样式
    - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#geo.itemStyle)

  - `layoutCenter`、`layoutSize`

    - 提供除了 `left/right/top/bottom/width/height` 属性之外的布局手段

    - 设置这两个值后，上述属性会无效

    - 在使用 `left/right/top/bottom/width/height` 的时候，可能很难在保持地图高宽比的情况下把地图放在某个盒形区域的正中间，并且保证不超出盒形的范围

    - 此时可以通过 `layoutCenter` 属性定义地图中心在屏幕中的位置，`layoutSize` 定义地图的大小

    - 例

      ~~~javascript
      layoutCenter: ['30%', '30%'],
      // 如果宽高比大于 1 则宽度为 100，如果小于 1 则高度为 100，保证了不超过 100x100 的区域，也可以设置为相对于屏幕宽高中较小值的百分比
      layoutSize: 100
      ~~~

      

### `series` 组件

- `series` 组件用于配置图表的类型和图形信息数据
- 配置项是一个数组，每个数组项都是一个系列对象

#### 基本属性

- `name`

  - 系列名称

- `type`

  - 指定系列图表的类型
  - 常见的有
    - `line` 折线图
    - `bar` 柱状图
    - `pie` 饼图
    - `scatter` 散点图
    - `map` 地图
    - `radar` 雷达图

- `legendHoverLink`

  - 是否启用图例 `hover` 时联动高亮
  - 默认为 `true`

- `markPoint`、`markLine`

  - 图表标注
  - 用于标记特定的点、线
  - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#series-line.markPoint)
  - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#series-line.markLine.data)
  - 常用属性

    - `symbol`

      - 标记的图形
  - `label`
    - 标注的文本
  - `data`
    
    - 要标注的数据数组
      - 每个数组项是一个对象
    
        - 有下面几种方式指定标注的位置。
    
          1. 通过 [x](https://echarts.apache.org/zh/option.html#series-line.markPoint.data.x), [y](https://echarts.apache.org/zh/option.html#series-line.markPoint.data.y) 属性指定相对容器的屏幕坐标，单位像素，支持百分比
      2. 用 [coord](https://echarts.apache.org/zh/option.html#series-line.markPoint.data.coord) 属性指定数据在相应坐标系上的坐标位置，单个维度支持设置 `'min'`, `'max'`, `'average'`
          3. 直接用 [type](https://echarts.apache.org/zh/option.html#series-line.markPoint.data.type) 属性标注系列中的最大值，最小值。这时候可以使用 [valueIndex](https://echarts.apache.org/zh/option.html#series-line.markPoint.data.valueIndex) 或者 [valueDim](https://echarts.apache.org/zh/option.html#series-line.markPoint.data.valueDim) 指定是在哪个维度上的最大值、最小值、平均值
      4. 对于 `markLine`
             - 如果是笛卡尔坐标系的话，也可以通过只指定 `xAxis` 或者 `yAxis` 来实现 X 轴或者 Y 轴为某值的标线，见示例 [scatter-weight](https://echarts.apache.org/examples/zh/editor.html?c=scatter-weight)
    - 当多个属性同时存在时，优先级按上述的顺序

- `clip`

  - 是否裁剪超出坐标系部分的图形
  - 从 `v4.4.0` 开始支持

- `label`

  - 图形上的文本标签

  - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#series-line.label)

  - 接收一个配置对象，常用属性有

    - `show`

    - `position`

      - 可以使用 `'top'`、`'left'`、`'right'`、`'bottom'`、`'inside'` 等

      - 也可以用一个数组表示相对的百分比或者绝对像素值表示标签相对于图形包围盒左上角的位置，例

        ~~~javascript
        // 绝对的像素值
        position: [10, 10],
        // 相对的百分比
        position: ['50%', '50%']
        ~~~

    - `distance`

      - 距离图形元素的距离
      - 当 `position` 为字符描述值（如 `'top'`、`'insideRight'`）时候有效

    - `formatter`

      - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#series-line.label.formatter)

- `labelLine`

  - 标签的视觉引导线配置
  - 从 `v5.0.0` 开始支持
  - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#series-line.labelLine)

- `cursor`

  - 鼠标悬停在图形元素上时的样式
  - 可选值
    - `pointer`（默认值）
    - `move`

- `select`、`selectedMode`

  - `select` 配置选中时的图形样式和标签样式
  - `selectedMode` 配置能否被选中
    - 默认关闭，支持布尔值和字符串，字符串取值可选`'single'`，`'multiple'`，`'series'` 分别表示单选，多选以及选择整个系列
  - 从 `v5.0.0` 开始支持
  - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#series-line.select)

- `emphasis`

  - 图形的高亮状态
  - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#series-line.emphasis)

- `stack`

  - 数据堆叠

  - 接收一个字符串

  - 同个类目轴上的 `series` 如果配置了相同的 `stack` 值就可以堆叠放置

  - 只支持数值轴和对数轴，不支持类目轴和时间轴

  - 相关补充：切换堆叠模式与平铺模式

    - 通过在 `toolbox.freature.magicType.type` 数组中添加 `'stack'` 项使用堆叠类型，添加 `'tiled'` 项使用平铺模式

      ~~~javascript
      toolbox: {
        feature: {
          magicType: { type: ['stack', 'tiled'] },
        }
      }
      ~~~

    - 注意，实际上该模式切换是修改了所有 `series` 的 `stack` 属性

      - 当切换为 `tiled` 模式时，移除所有 `series` 的 `stack` 属性
      - 当切换为 `stack` 模式时，将所有 `series` 的 `stack` 属性设置为相同的值

- `stackStrategy`

  - 数据堆叠策略，前提是 `stack` 属性已被设置
  - 可选值
    - `'samesign'`（默认值）
      - 只要堆叠的值与当前累积的堆叠值具有相同的正负符号时才堆叠
    - `'all'`
      - 堆叠所有的值
    - `'positive'`
      - 只堆叠正值
    - `negative`
      - 只堆叠负值

#### `data` 属性

- 系列中数值内容数组，数组项通常为具体的数据项

- 如果系列没有指定 `data`，并且 `option` 有 `dataset`，那么默认使用第一个 `dataset`

- 可以使用 `series.datasetIndex` 指定其它的 `dataset`

- 通常数据使用一个二维数组表示，每一列被称为一个**维度**

  ~~~javascript
  series: [{
      data: [
          // 维度X   维度Y   其它维度 ...
          [  3.4,    4.5,   15,   43],
          [  4.2,    2.3,   20,   91],
          [  10.8,   9.5,   30,   18],
          [  7.2,    8.8,   18,   57]
      ]
  }]
  ~~~

  - 在直角坐标系中，『维度 `X`』 和『维度 `Y`』会默认对应于 `xAxis` 和 `yAxis`
  - 在极坐标系中，『维度 `X`』 和『维度 `Y`』会默认对应于 `radiusAxis` 和 `angleAxis`
  - 其它维度是可选的，可以在别处使用，例如
    - 在 [visualMap](https://echarts.apache.org/zh/option.html#visualMap) 中可以将一个或多个维度映射到颜色，大小等多个图形属性上
    - 在 [series.symbolSize](https://echarts.apache.org/zh/option.html#series.symbolSize) 中可以使用回调函数，基于某个维度得到 `symbolSize` 值
    - 使用 [tooltip.formatter](https://echarts.apache.org/zh/option.html#tooltip.formatter) 或 [series.label.formatter](https://echarts.apache.org/zh/option.html#series.label.formatter) 可以把其他维度的值展示出来

- 当主轴为类目轴时，数据可以简化用一个一维数组表示，例如

  ~~~javascript
  xAxis: {
      data: ['a', 'b', 'm', 'n']
  },
  series: [{
      // 与 xAxis.data 一一对应。
      data: [23,  44,  55,  19]
      // 它其实是下面这种形式的简化：
      // data: [[0, 23], [1, 44], [2, 55], [3, 19]]
  }]
  ~~~

- `data` 中的值与轴类型的关系

  - 当某维度对应于**数值轴**或**对数轴**时

    - 其值可以为 `number` 类型
    - 也可以兼容 `string` 形式的数字

  - 当某维度对应于**类目轴**时

    - 其值需为类目的序号（从 `0` 开始）或类目的字符串值

    - 例

      ~~~javascript
      xAxis: {
          type: 'category',
          data: ['星期一', '星期二', '星期三', '星期四']
      },
      yAxis: {
          type: 'category',
          data: ['a', 'b', 'm', 'n', 'p', 'q']
      },
      series: [{
          data: [
              // xAxis    yAxis
              [  0,        0,    2  ], // 意思是此点位于 xAxis: '星期一', yAxis: 'a'。
              [  '星期四',  2,    1  ], // 意思是此点位于 xAxis: '星期四', yAxis: 'm'。
              [  2,       'p',   2  ], // 意思是此点位于 xAxis: '星期三', yAxis: 'p'。
              [  3,        3,    5  ]
          ]
      }]
      ~~~

  - 当某维度对应于**时间轴**时

    - 其值可以是时间戳，如 `1484141700832`，表示 `UTC` 时间
    - 也可以是字符串形式的时间描述
      - `ISO 8601` 的子集，只包含这些形式（这几种格式，除非指明时区，否则均表示本地时间，与 `moment`一致）：
        - 部分年月日时间: `'2012-03'`, `'2012-03-01'`, `'2012-03-01 05'`, `'2012-03-01 05:06'`.
        - 使用 `'T'` 或空格分割: `'2012-03-01T12:22:33.123'`, `'2012-03-01 12:22:33.123'`.
        - 时区设定: `'2012-03-01T12:22:33Z'`, `'2012-03-01T12:22:33+8000'`, `'2012-03-01T12:22:33-05:00'`.
      - 其他的时间字符串，包括（均表示本地时间）: `'2012'`, `'2012-3-1'`, `'2012/3/1'`, `'2012/03/01'`, `'2009/6/12 2:00'`, `'2009/6/12 2:05:08'`, `'2009/6/12 2:05:08.123'`
    - 或者用户自行初始化的 `Date` 实例：
      - 注意，用户自行初始化 `Date` 实例的时候，[浏览器的行为有差异，不同字符串的表示也不同](https://dygraphs.com/date-formats.html)。
      - 例如：在 `chrome` 中，`new Date('2012-01-01')` 表示 `UTC` 时间的 `2012` 年 `1` 月 `1` 日，而 `new Date('2012-1-1')` 和 `new Date('2012/01/01')` 表示本地时间的 `2012` 年 `1` 月 `1` 日。在 `safari` 中，不支持 `new Date('2012-1-1')` 这种表示方法。
      - 所以，使用 `new Date(dataString)` 时，可使用第三方库解析（如 [moment](https://momentjs.com/)），或者使用 `echarts.time.parse`，或者参见 [这里](https://dygraphs.com/date-formats.html)。

- 当需要对数据进行个性化定义时

  - 可使用对象表示数据项

  - 其中的 `value` 属性表示具体的数据，如

    ~~~javascript
    [
        12,
        34,
        {
            value : 56,
            // 自定义标签样式，仅对该数据项有效
            label: {},
            // 自定义特殊 itemStyle，仅对该数据项有效
            itemStyle:{}
        },
        10
    ]
    // 或
    [
        [12, 33],
        [34, 313],
        {
            value: [56, 44],
            label: {},
            itemStyle:{}
        },
        [10, 33]
    ]
    ~~~

  - 如果数据项有中与 `series` 中有相同的配置项，则数据项中的优先级更高
  - 数据项常用属性
    - `name`
      - 数据项的名称
    - `value`
      - 数据项的值
    - `itemStyle`
      - 用于定义数据项个性化样式的对象
    - `label`
      - 数据项的标签样式
    - `emphasis`
      - 强调样式
      - 用于当鼠标经过时，数据项的显示效果
    - `tooltip`
      - 单个数据的 `tooltip` 设定

#### `data` 属性映射

- `dimensions`

  - 使用 `dimensions` 定义 `series.data` 或者 `dataset.source` 的每个维度的信息

  - 例

    ~~~javascript
    var option1 = {
      dataset: {
        dimensions: [
          { name: 'score' },
          // 可以简写为 string ，表示 dimension name 。
          'amount',
          // 可以在 type 中指定维度类型。
          { name: 'product', type: 'ordinal' }
        ],
        source: [
          //...
        ]
      }
      // ...
    }
    
    var option2 = {
      dataset: {
        source: [
          // ...
        ]
      },
      series: {
        type: 'line',
        // series.dimensions 会更优先于 dataset.dimension 采纳。
        dimensions: [
          null, // 可以设置为 null 表示不想设置维度名
          'amount',
          { name: 'product', type: 'ordinal' }
        ]
      }
      // ...
    }
    ~~~

  - `dimensions.type` 的取值

    - `'number'`: 默认，表示普通数据。
    - `'ordinal'`: 对于类目、文本这些 `string` 类型的数据，如果需要能在数轴上使用，须是 `'ordinal'` 类型。`ECharts` 默认会试图自动判断这个类型。但是自动判断也可能不准确，所以使用者也可以手动强制指定。
    - `'time'`: 表示时间数据。设置成 `'time'` 则能支持自动解析数据成时间戳（`timestamp`），比如该维度的数据是 '2017-05-10'，会自动被解析。如果这个维度被用在时间数轴（[axis.type](https://echarts.apache.org/option.html#xAxis.type) 为 `'time'`）上，那么会被自动设置为 `'time'` 类型。时间类型的支持参见 [data](https://echarts.apache.org/option.html#series.data)。
    - `'float'`: 如果设置成 `'float'`，在存储时候会使用 `TypedArray`，对性能优化有好处。
    - `'int'`: 如果设置成 `'int'`，在存储时候会使用 `TypedArray`，对性能优化有好处。

  - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#series-line.dimensions)

- `encode`

  - 可以定义 `data` 中的哪个维度被编码成什么，比如

    ~~~javascript
    option = {
        dataset: {
            source: [
                // 每一列称为一个『维度』。
                // 这里分别是维度 0、1、2、3、4。
                [12, 44, 55, 66, 2],
                [23, 6, 16, 23, 1],
                ...
            ]
        },
        series: {
            type: 'xxx',
            encode: {
                x: [3, 1, 5],      // 表示维度 3、1、5 映射到 x 轴。
                y: 2,              // 表示维度 2 映射到 y 轴。
                tooltip: [3, 2, 4] // 表示维度 3、2、4 会在 tooltip 中显示。
            }
        }
    }
    ~~~

  - `encode` 支持的属性

    ~~~javascript
    // 在任何坐标系和系列中，都支持：
    encode: {
      // 使用 “名为 product 的维度” 和 “名为 score 的维度” 的值在 tooltip 中显示
      tooltip: ['product', 'score']
      // 使用 “维度 1” 和 “维度 3” 的维度名连起来作为系列名。（有时候名字比较长，这可以避免在 series.name 重复输入这些名字）
      seriesName: [1, 3],
      // 表示使用 “维度2” 中的值作为 id。这在使用 setOption 动态更新数据时有用处，可以使新老数据用 id 对应起来，从而能够产生合适的数据更新动画。
      itemId: 2,
      // 指定数据项的名称使用 “维度3” 在饼图等图表中有用，可以使这个名字显示在图例（legend）中。
      itemName: 3
    }
    
    // 直角坐标系（grid/cartesian）特有的属性：
    encode: {
      // 把 “维度1”、“维度5”、“名为 score 的维度” 映射到 X 轴：
      x: [1, 5, 'score'],
      // 把“维度0”映射到 Y 轴。
      y: 0
    }
    
    // 单轴（singleAxis）特有的属性：
    encode: {
      single: 3
    }
    
    // 极坐标系（polar）特有的属性：
    encode: {
      radius: 3,
      angle: 2
    }
    
    // 地理坐标系（geo）特有的属性：
    encode: {
      lng: 3,
      lat: 2
    }
    
    // 对于一些没有坐标系的图表，例如饼图、漏斗图等，可以是：
    encode: {
      value: 3
    }
    ~~~

  - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#series-line.encode)

- `seriesLayoutBy`

  - 当使用 `dataset` 时，`seriesLayoutBy` 指定了 `dataset` 中用行还是列对应到系列上，也就是说，系列排布到 `dataset` 的行还是列上
  - 可选值
    - `'column'`（默认值）
      - `dataset` 的列对应于系列，即 `dataset` 中的每一列是一个维度（`dimension`）
      - `dataset` 的行对应也序列，即 `dataset` 中的每一行是一个维度（`dimension`）

- `datasetIndex`

  - 如果 `series.data` 没有指定，并且 `dataset` 存在，那么就会使用 `dataset`
  - `datasetIndex` 用于指定本系列使用哪个 `dataset`

#### 性能优化相关属性

- `sampling`
  - 数据量远大于像素点时候的降采样策略
  - 适用于 `line` 和 `bar`
  - 开启后可以有效的优化图表的绘制效率，默认关闭，也就是全部绘制不过滤数据点
  - 可选值
    - `'lttb'` 采用 Largest-Triangle-Three-Bucket 算法，可以最大程度保证采样后线条的趋势，形状和极值
    - `'average'` 取过滤点的平均值
    - `'max'` 取过滤点的最大值
    - `'min'` 取过滤点的最小值
    - `'sum'` 取过滤点的和
- `large`
  - 是否开启大数据量优化
  - 在数据图形特别多且出现卡顿时可以开启
  - 开启后无法自定义设置单个数据项的样式
- `largeThreshold`
  - 开启绘制优化的阈值
  - 默认为 `400`
- `progressive`
  - 渐进式渲染时每一帧绘制图形数量
  - 设为 `0` 时不启用渐进式渲染
  - 支持每个系列单独配置
  - 渐进式渲染（`progressive rendering`）
    - 渲染时将创建好的图形分到数帧中渲染
    - 每一帧渲染只渲染指定数量的图形
- `progressiveThreshold`
  - 启动渐进式渲染的图形数量阈值
  - 在单个系列的图形数量超过该阈值时启用渐进式渲染
- `progressiveChunkMode`
  - 渐进式渲染分片的方式
  - 可选值
    - `'sequential'`
      - 按数据的顺序分片
      - 缺点是渲染过程不自然
    - `'mod'`（默认值）
      - 取模分片
      - 每个片段中的点会遍布于整个数据，从而能够视觉上均匀地渲染

#### 样式属性

- `colorBy`
  - 从 `option.color` 中的取色策略
  - 从 `v5.2.0` 开始支持
  - 可选值为
    - `'seriers'`（默认值）
      - 按系列分配调色盘中的颜色
      - 即不同系列依次使用调色盘中的不同颜色，同一系列中的所有数据使用相同的颜色
    - `'data'`
      - 按数据项分配调色盘中的颜色
      - 即系列中的每个数据项依次使用调色盘中的不同颜色
- `itemStyle`、`lineStyle`、`areaStyle`
  - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#series-line.itemStyle)

#### 坐标系选择属性

- `coordinateSystem`
  - 使用的坐标系
  - 可选值
    - `'cartesian2d'`（默认）
      - 使用二维直角坐标系（也称笛卡尔坐标系）
      - 通过 `xAxisIndex`、`yAxisIndex` 属性指定相应的直角坐标轴组件
    - `'polar'`
      - 使用极坐标系
      - 通过 `polarIndex` 指定相应的极坐标组件
  - 当使用直角坐标系时可以不指定而使用默认值，当使用极坐标系时需要指定为 `'polar'`
- `xAxisIndex`、`yAxisIndex`

  - 当存在多个 `x` 轴、`y` 轴时，使用这些属性指定要应用的坐标轴序号
- `polarIndex`

  - 当单个图表示例存在多个极坐标系时，使用该属性指定要应用的极坐标系序号
- `radarIndex`、`geoIndex`、`calendarIndex` 等

#### 折线图（`line` 类型）

- 类型介绍

  - 折线图/面积图，是用折线将各个数据点的 `symbol` 连接起来的图表，用于展现数据的变化趋势

  - 可用于直角坐标系和极坐标系上

  - **Tip**

    - 设置 [areaStyle](https://echarts.apache.org/zh/option.html#series-line.areaStyle) 后可以绘制面积图。

    * 配合分段型 [visualMap](https://echarts.apache.org/zh/option.html#visualMap-piecewise) 组件可以将折线图/面积图通过不同颜色分区间

- `showSymbol`
  - 是否一直显示折线拐点处的标记
  - 默认为 `true`
  - 如果设置为 `false`，则只会在 `tooltip hover` 时显示
- `showAllSymbol`
  - 只在主轴为类目轴时有效
  - 可选值
    - `'auto'`（默认值）
      - 如果有足够空间则显示标志图形，否则随主轴标签间隔隐藏
    - `true`
      - 显示所有图形
    - `false`
      - 随主轴标签间隔隐藏
  - 实测下来 `false` 的效果和 `'auto'` 相同，都是当主轴上的文本因为空间不足而隐藏时，标记也会隐藏
- `symbol`
  - 折线拐点处标记的图形
  - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#series-line.symbol)
- `symbolSize`
  - 标记的大小
  - 默认为 `4`
- `symbolOffset`
  - 标记相对于原本位置的偏移
  - 一个数组，默认是 `[0, 0]`，数值可以是绝对像素值，也可以是相对百分比

- `connectNulls`
  - 是否连接空数据
  - 当 `series.data` 中出现空数据时（表示为 `'-'` 或 `null` 或 `undefined` 或 `NaN`）
    - 如果为 `true`，则连线不会中断，只会忽略空数据
    - 如果为 `false`，则空数据两侧的连线会被断开
- `step`
  - 阶梯线图
  - 如果设置成 `true` 则显示成阶梯线图（默认为 `'start'` 模式）
  - 也可以设置成 `'start'`、`'middle'`、`'end'`
- `smooth`
  - 是否平滑曲线显示
  - 如果是布尔值，则表示是否开启平滑处理，默认开启
  - 如果是范围为 `0` 到 `1` 的数字类型，则表示平滑程度，越小表示越接近折线，默认为 `0.5`
- `smoothMonotone`
  - 折线在平滑显示后是否在一个维度上保持单调性
  - 可选值有 `'x'`、`'y'`、`'none'`
  - 单调性
    - 比如在 `x` 轴上维持单调性，意味着在一个区间内，当 `x` 值递增时，`y` 的值也跟着一直递增或一直递减，而不会出现波动
    - 数据点之间的单调性
      - 两个数据点之间使用直线连接时，两个数据点之间是保持单调性的，此时两个数据点之间的最高点和最低点只能是在数据点处出现
      - 当使用曲线平滑显示后，为了平滑过渡，可能会在两个数据点之间出现最高点或最低点，此时两个数据点之间没有保持单调性

#### 柱状图（`bar` 类型）

- 类型介绍
  - 柱状图/条状图是一种通过柱形的高度（横向情况下是宽度）来表现数据大小的一种常用图表类型

- `roundCap`
  - 是否在环形驻条两侧使用圆弧效果
  - 仅对极坐标系柱状图有效
  - 从 `v4.5.0` 开始支持
- `realtimeSort`
  - 是否开启动态排序，用来实现动态排序图效果
  - [动态排序柱状图](https://echarts.apache.org/handbook/zh/how-to/chart-types/bar/bar-race/)
- `showBackground`
  - 是否显示柱条的背景色
  - 通过 `backgroundStyle` 属性配置背景样式
  - 从 `v4.7.0` 开始支持
- `backgroundStyle`
  - 每一个柱条的背景样式，需要将 `showBackground ` 设为 `true` 时生效
  - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#series-bar.showBackground)
- `barWidth`
  - 柱条宽度
  - 默认为自适应
  - 可以设置为绝对值或百分比，百分比基于自动计算出的每一类目的宽度
  - 在同一坐标系上，该属性会被多个 `'bar'` 系列共享，该属性应设置于坐标系中最后一个 `'bar'` 系列上才会生效
- `barMaxWidth`、`barMinWidth`
  - 柱条的最大宽度
  - `barMinWidth` 在直角坐标系中的默认值为 `1`，否则默认值为 `null`
  - 优先级高于 `barWidth`
  - 设置方式同 `barWidth`

- `barMinHeight`
  - 柱条最小高度
  - 单位为像素
  - 可用于防止某数据项的值过小而影响交互
- `barMinAngle`
  - 柱条最小角度，仅对极坐标系有效
  - 可用于防止某数据项的值过小而影响交互
- `barGap`
  - 不同的系列的柱条间距
  - 使用百分比表示，如 `'30%'`，表示柱子宽度的 `30%`，可以为负数
  - 在同一坐标系上，该属性会被多个 `'bar'` 系列共享，该属性应设置于坐标系中最后一个 `'bar'` 系列上才会生效
- `barCategoryGap`
  - 同一系列的柱间距离
  - 默认为类目间距的 `20%`，可以用百分比表示，也可以设置固定像素大小
  - 在同一坐标系上，该属性会被多个 `'bar'` 系列共享，该属性应设置于坐标系中最后一个 `'bar'` 系列上才会生效

#### 饼图（`pie` 类型）

- 类型介绍
  - 饼图主要用于表现不同类目的数据在总和中的占比，每个弧度表示数据数量的比例
  - 对于一个图表中有多个饼图的场景，可以使用 [left](https://echarts.apache.org/zh/option.html#series-pie.left)、[right](https://echarts.apache.org/zh/option.html#series-pie.right)、[top](https://echarts.apache.org/zh/option.html#series-pie.top)、[bottom](https://echarts.apache.org/zh/option.html#series-pie.bottom)、[width](https://echarts.apache.org/zh/option.html#series-pie.width)、[height](https://echarts.apache.org/zh/option.html#series-pie.height) 配置每个饼图系列的位置和视口大小。[radius](https://echarts.apache.org/zh/option.html#series-pie.radius)、[label.edgeDistance](https://echarts.apache.org/zh/option.html#series-pie.label.edgeDistance) 等支持百分比的配置项，是相对于该配置项决定的矩形的大小而言的
  - **Tip**
    - 饼图更适合表现数据相对于总数的百分比等关系。如果只是表示不同类目数据间的大小，建议使用 [柱状图](https://echarts.apache.org/zh/bar)，人们对于微小的弧度差别相比于微小的长度差别更不敏感，或者也可以通过配置 [roseType](https://echarts.apache.org/zh/option.html#series-pie.roseType) 显示成南丁格尔图，通过半径大小区分数据的大小
    - 使用 `label` 配置项定义标签文字的排布
      - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#series-pie.label)

- `selectedOffset`
  - 选中山区的偏移举例
  - 接收数字，默认为 `10`
- `clockwise`
  - 饼图的山区是否按顺时针排布
  - 默认为 `true`

- `startAngle`
  - 起始角度
  - 默认为 `90`，即圆心上方
  - `0` 代表圆心正右方
- `minAngle`
  - 最小的扇区角度
  - 用于防止某个值过小导致扇区太小影响交互
- `minShowLabelAngle`
  - 最小的显示文本标签的扇区角度
  - 小于这个角度的扇区不会显示文本标签
- `roseType`
  - 是否展示成南丁格尔图
  - 可以使用布尔值，也可以使用字符串指定模式
  - 通过半径区分数据大小，可选择两种模式
    - `'radius'`（为 `true` 时默认）
      - 扇区圆心角展现数据的百分比，半径展示数据的大小
    - `area`
      - 所有扇区圆心角相同，仅通过半径展现数据大小
- `avoidLabelOverlap`
  - 是否启用防止标签重叠策略
  - 默认开启，在标签拥挤重叠的情况下会挪动各个标签的位置，防止标签间的重叠
- `stillShowZeroSum`
  - 是否在数据和为 `0`（一般情况下为所有数据都为 `0`）时仍显示扇区
  - 默认为 `true`
- `showEmptyCircle`
  - 是否在无数据时显示一个占位圆
  - 默认为 `true`
  - 从 `v5.2.0` 开始支持
- `percentaPrecision`
  - 饼图百分比数值的精度
  - 默认为 `2`，即保留小数点后两位
- `left`、`top`、`right`、`bottom`
  - 饼图距离容器的边距
- `width`、`height`
  - 饼图的宽高
- `center`、`radius`
  - 饼图的中心坐标、半径
  - 同 `poloar.center`、`polar.radius`

#### 散点图（`scatter` 类型）

- 类型介绍
  - 直角坐标系上的散点图/气泡图可以用来展现数据的 `x`，`y` 之间的关系，如果数据项有多个维度，其它维度的值可以通过不同大小的 [symbol](https://echarts.apache.org/zh/option.html#series-scatter.symbol) 展现成气泡图，也可以用颜色来表现，这些可以配合 [visualMap](https://echarts.apache.org/zh/option.html#visualMap) 组件完成
  - 可以应用在直角坐标系，极坐标系，地理坐标系上
- `symbol` 相关属性 
  - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#series-scatter.symbol)

#### 涟漪特效散点图（`effectScatter` 类型）

- 类型介绍
  - 带有涟漪特效动画的散点图/气泡图
  - 利用动画特效可以将某些想要突出的数据进行视觉突出
- `showEffectOn`
  - 配置何时显示特效
  - 可选值
    - `'render'`（默认值）
      - 绘制完成后显示特效
    - `'emphasis'`
      - `hover` 时显示特效
- `rippleEffect`
  - 涟漪特效相关配置
  - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#series-effectScatter.rippleEffect)
- `coordinateSystem`
  - 该系列使用的坐标系
  - 可选值
    - `'cartesian2d'`
    - `'polar'`
    - `'geo'`
    - `'calendar'`
- `symbol`
  - 标记图形
  - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#series-effectScatter.symbol)
- `label`
  - 文本标签
  - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#series-effectScatter.label)

#### 地图（`map` 类型）

- 类型介绍

  - 地图主要用于地理区域数据的可视化，配合 `visualMap` 组件用于展示不同区域的数据
  - 多个地图类型相同的系列会在同一个地图上显示，这时候使用第一个系列的配置项作为地图绘制的配置
  - 默认情况下，`map` 类型的 `series` 会自己生成内部专用的 `geo` 地理坐标系组件
    - `geo` 地理坐标系组件用于地图的绘制
    - `map` 系列主要用于地理区域数据的可视化，配合 `data` 使用

- 有很多属性与 `geo` 组件的相同

- `data` 属性

  - 地图系列中的数据内容数组

  - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#series-map.data)

  - 数组项可以为单个数值，如

    ~~~javascript
    [12, 34, 56, 10, 23]
    ~~~

  - 如果需要在数据中加入其它维度给 `visualMap` 组件用来映射到颜色等其它图形，每个数据项也可以是数组，如

    ~~~javascript
    [[12, 14], [34, 50], [56, 30], [10, 15], [23, 10]]
    ~~~

    - 每项数组中的第二个值将指定给 `visualMap` 组件

  - 如果需要对个别内容进行个性化定义

    ~~~javascript
    [{
        name: '数据1',
        value: 10
    }, {
        // 数据项名称
        name: '数据2',
        value : 56,
        //自定义特殊 tooltip，仅对该数据项有效
        tooltip:{},
        //自定义特殊itemStyle，仅对该item有效
        itemStyle:{}
    }]
    ~~~

### `dataset` 组件

- `ECharts 4` 开始支持数据集（`dataset`）组件用于单独的数据集声明，从而数据可以单独管理，被多个组件复用，并可以自由指定数据到视觉的映射

- 教程 [数据集 - 概念篇 - Handbook - Apache ECharts](https://echarts.apache.org/handbook/zh/concepts/dataset/)

- 并非所有图表都支持 `dataset`

- 常用属性

  - `source`

    - 原始数据
    - 一般来说原始数据表达的是二维表
      - 可以使用二维数组，也可以使用对象键值对表示
      - 二维数组中，第一行（如果系列中指定了 `series.seriesLayoutBy` 为 `'row'` 则是第一列）可以给出维度名，在可以在 `dataset.dimensions` 中列出维度名，在二维数组中只表示数据

  - `dimensions`

    - 定义 `series.data` 或 `dataset.source` 的每个维度信息
    - `dimensions` 是一个数组，每一项可以是
      - 字符串形式的维度名
      - 配置对象，属性有
        - `name`
          - 维度名
        - `type`
          - 维度中数据的类型
          - 可选值
            - `'number'`
              - 默认，表示普通数据
            - `'ordinal'`
              - 对于类目、文本这些 `string` 类型的数据，如果需要能在轴上使用，须是 `'ordinal'` 类型
              - `ECharts` 默认会自动判断这个类型，如果判断失败可以手动指定
            - `'float'`
              - 即 `Float64Array` 类型
              - 相比于 `'number'` 性能更优
            - `'int'`
              - 即 `Int32Array` 类型
              - 相比于 `'number'` 性能更优
            - `'time'`
              - 表示时间类型
              - 设置成 `'time'` 能支持自动解析数据成时间戳，
        - `displayName`
          - 一般用于 `tooltip` 中的维度名展示
          - 如果没有指定，则默认使用 `name` 来展示

  - `sourceHeader`

    - 第一行/第一列是否是维度名信息
    - 可选值
      - `'auto'/null/undefined`（默认）
        - 自动探测
      - `true`
      - `false`
      - `number`
        - 指定数据行/列的开始索引
        - 比如取值为 `2` 时，表示前两行/列是维度信息，从第三行/列开始是数据信息

  - `transform` 相关

    - [Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#dataset.transform)

    - 基本使用

      - 三个饼图分别显示 `2011`、`2012`、`2013` 年的数据

        ~~~javascript
        var option = {
          dataset: [
            {
              // 这个 dataset 的 index 是 `0`。
              source: [
                ['Product', 'Sales', 'Price', 'Year'],
                ['Cake', 123, 32, 2011],
                ['Cereal', 231, 14, 2011],
                ['Tofu', 235, 5, 2011],
                ['Dumpling', 341, 25, 2011],
                ['Biscuit', 122, 29, 2011],
                ['Cake', 143, 30, 2012],
                ['Cereal', 201, 19, 2012],
                ['Tofu', 255, 7, 2012],
                ['Dumpling', 241, 27, 2012],
                ['Biscuit', 102, 34, 2012],
                ['Cake', 153, 28, 2013],
                ['Cereal', 181, 21, 2013],
                ['Tofu', 395, 4, 2013],
                ['Dumpling', 281, 31, 2013],
                ['Biscuit', 92, 39, 2013],
                ['Cake', 223, 29, 2014],
                ['Cereal', 211, 17, 2014],
                ['Tofu', 345, 3, 2014],
                ['Dumpling', 211, 35, 2014],
                ['Biscuit', 72, 24, 2014]
              ]
              // id: 'a'
            },
            {
              // 这个 dataset 的 index 是 `1`。
              // 这个 `transform` 配置，表示，此 dataset 的数据，来自于此 transform 的结果。
              transform: {
                type: 'filter',
                config: { dimension: 'Year', value: 2011 }
              }
              // 我们还可以设置这些可选的属性： `fromDatasetIndex` 或 `fromDatasetId`。
              // 这些属性，指定了，transform 的输入，来自于哪个 dataset。例如，
              // `fromDatasetIndex: 0` 表示输入来自于 index 为 `0` 的 dataset 。又例如，
              // `fromDatasetId: 'a'` 表示输入来自于 `id: 'a'` 的 dataset。
              // 当这些属性都不指定时，默认认为，输入来自于 index 为 `0` 的 dataset 。
            },
            {
              // 这个 dataset 的 index 是 `2`。
              // 同样，这里因为 `fromDatasetIndex` 和 `fromDatasetId` 都没有被指定，
              // 那么输入默认来自于 index 为 `0` 的 dataset 。
              transform: {
                // 这个类型为 "filter" 的 transform 能够遍历并筛选出满足条件的数据项。
                type: 'filter',
                // 每个 transform 如果需要有配置参数的话，都须配置在 `config` 里。
                // 在这个 "filter" transform 中，`config` 用于指定筛选条件。
                // 下面这个筛选条件是：选出维度（ dimension ）'Year' 中值为 2012 的所有
                // 数据项。
                config: { dimension: 'Year', value: 2012 }
              }
            },
            {
              // 这个 dataset 的 index 是 `3`。
              transform: {
                type: 'filter',
                config: { dimension: 'Year', value: 2013 }
              }
            }
          ],
          series: [
            {
              type: 'pie',
              radius: 50,
              center: ['25%', '50%'],
              // 这个饼图系列，引用了 index 为 `1` 的 dataset 。也就是，引用了上述
              // 2011 年那个 "filter" transform 的结果。
              datasetIndex: 1
            },
            {
              type: 'pie',
              radius: 50,
              center: ['50%', '50%'],
              datasetIndex: 2
            },
            {
              type: 'pie',
              radius: 50,
              center: ['75%', '50%'],
              datasetIndex: 3
            }
          ]
        };
        ~~~

## 常用 `API`

### 全局 `echarts` 对象

- `echarts.init(dom[, theme][, opts])`
  - 返回一个 `ECharts` 实例（`echartInstance`）
  - 注意单个容器上不可以初始化多个 `Echarts` 实例
  - [Documentation - Apache ECharts](https://echarts.apache.org/zh/api.html#echarts.init)
  - 如果使用默认 `theme`，则传入 `null`
  - `opts` 常用选项
    - `renderer`
      - 渲染模式，支持 `'canvas'`（默认值） 或者 `'svg'`
    - `width`、`height`
      - 显式地指定实例宽高，单位为像素
      - 默认自动读取容器宽高
    - `useCoarsePointer`
      - 是否扩大可点击元素的响应范围
      - 可选值
        - `null`（默认值）
          - 对移动设备开启
        - `true`
          - 总是开启
        - `false`
          - 总是不开启
      - 参见[增加交互响应范围](https://echarts.apache.org/handbook/zh/how-to/interaction/coarse-pointer)
    - `pointerSize`
      - 扩大元素响应范围的像素大小
      - 默认是 `44`

- `echarts.registerMap(mapName[, opt])`
  - 注册可用的地图，只在 [geo](https://echarts.apache.org/zh/option.html#geo) 组件或者 [map](https://echarts.apache.org/zh/option.html#series-map) 图表类型中使用
  - 接收参数
    - `mapName`
      - 地图名称
    - `opt` 可选配置选项，属性有
      - `geoJSON` / `geoJson`
        - `GeoJSON` 格式的数据
        - 可以是 `JSON` 字符串，也可以是解析得到的对象
      - `svg`
        - 从 `v5.1.0` 开始支持 `SVG` 格式的数据
        - 可以是 `SVG` 字符串，也可以是解析得到的 `SVG DOM` 对象
      - `specialAreas`
        - 将地图中的部分区域缩放到合适位置
        - 只在 `GeoJSON` 中生效，`SVG` 中不生效

- `echarts.getMap(string)`

  - 获取已注册的地图（不支持 `SVG`）

  - 返回的对象类型如下

    ~~~javascript
    {
        // 地图的 geoJSON 数据
        geoJSON: Object,
        // 地图的特殊区域，见 registerMap
        specialAreas: Object
    }
    ~~~

### `echartsInstance` 实例对象

- `echartsInstance.setOption(option[, opts])`
  - [Documentation - Apache ECharts](https://echarts.apache.org/zh/api.html#echartsInstance.setOption)
  - 设置图表实例的配置项以及数据，万能接口，所有参数和数据的修改都可以通过 `setOption` 完成，`ECharts` 会合并新的参数和数据，然后刷新图表
  - 如果开启动画，`ECharts` 找到两组数据之间的差异然后通过合适的动画去表现数据的变化
  - 接收参数
    - `option`
      - 图表的配置项和数据，具体见[配置项手册](https://echarts.apache.org/zh/option.html)
    - `opts`
      - `notMerge`
        - 是否不跟之前设置的 `option` 进行合并
        - 默认为 `false`，即表示合并
        - 如果为 `true`，表示所有组件都会被删除，然后根据新 `option` 创建所有新组件
      - `replaceMerge`
        - 可以在这里指定一个或多个组件，如：`xAxis`, `series`，这些指定的组件会进行**替换合并**
        - 如果用户想删除部分组件，也可使用 `replaceMerge`
      - `lazyUpdate` 
        - 在设置完 `option` 后是否不立即更新图表
        - 默认为 `false`，即同步立即更新
        - 如果为 `true`，则会在下一个 `animation frame` 中，才更新图表
      - `silent`
        - 阻止调用 `setOption` 时抛出事件
        - 默认为 `false`，即抛出事件
  - 组件合并模式
    - 对于一种类型的组件（如 `xAxis`，`series`）
      - 如果设置 `opts.notMerge` 为 `true`，那么旧的组件会完全移除，新的组件会根据 `option` 创建
      - 如果 `opts.notMerge` 为 `false`
        - 如果在 `opts.replaceMerge` 里指定组件类型，这类组件会进行**替换合并**
        - 否则会进行**普通合并**
  - [Documentation - Apache ECharts](https://echarts.apache.org/zh/api.html#echartsInstance.setOption)
- `echartsInstance.getWidth()`、`echartsInstance.getHeight()`、`echartsInstance.getDom()`
- `echartsInstance.getOption()`
  - 获取当前实例中维护的 `option` 对象
    - 返回的 `option` 对象中包含了用户多次 `setOption` 合并得到的配置项和数据，也记录了用户交互的状态，如图例开关，数据区域缩放选择的范围等
    - 从这份 `option` 可以恢复或者得到一个新的一模一样的实例
  - 返回的 `option` 对象每个组件的属性值都统一是一个数组，不管 `setOption` 船舰来时时单个组件对象还是多个组件对象
  - 不推荐将获取到的 `option` 对象修改后传入 `setOption` 合并
    - 因为 `getOption` 获取的是已经合并过默认值了的，有些属性的默认值可能是由其它配置项推断出来的，如果直接设置它们可能会导致原本的推断失效
- `echartInstance.resize([opts])`
  - 重新获取图表大小并刷新画布
  - 接收一个可选的 `opts` 配置对象，包含以下属性
    - `width`
      - 可显式指定实例宽度，单位为像素
      - 如果传入值为 `null`/`undefined`/`'auto'`，则表示自动取 `dom`（实例容器）的宽度
    - `height`
      - 可显式指定实例高度，单位为像素
      - 如果传入值为 `null`/`undefined`/`'auto'`，则表示自动取 `dom`（实例容器）的高度
    - `silent`
      - 是否禁止抛出事件
      - 默认为 `false`
    - `animation`
      - `resize` 的时候是否应用过渡动画，包含时长 `duration` 和缓动 `easing` 两个配置，默认 `duration` 为 0，即不应用过渡动画
- `echartInstance.clear()`
  - 移除当前实例中的所有组件和图表
- `echartInstance.dispose()`
  - 销毁当前实例
- `echartInstance.isDispose()`
  - 当前实例是否已经释放，返回布尔值

#### 事件相关

##### `API`

- `echartsInstance.dispatchAction(payload)`

  - 触发图表行为

  - 示例

    ~~~javascript
    myChart.dispatchAction({
        type: 'dataZoom',
        start: 20,
        end: 30
    });
    // 可以通过 batch 参数批量分发多个 action
    myChart.dispatchAction({
        type: 'dataZoom',
        batch: [{
            // 第一个 dataZoom 组件
            start: 20,
            end: 30
        }, {
            // 第二个 dataZoom 组件
            dataZoomIndex: 1,
            start: 10,
            end: 20
        }]
    })

- `echartsInstance.on(eventName[, query], handler[, context])`

  - 绑定事件处理函数

  - `ECharts` 中的事件由两种

    - 一种是鼠标事件，在鼠标点击某个图形时触发
    - 一种是调用 `dispatchAction` 后触发的事件，每个 `action` 都有对应的事件

  - 接收参数

    - `eventName`

      - 事件名称
      - 全小写，例如 `'click'`、`'mousemove'`、`'legendselected'`

    - `query`

      - 可选的过滤条件，能够只在指定的组件或元素上进行响应

      - 可以为 `string` 或 `Object`

        - 如果为 `string` 则表示组件类型，格式可以是 `'mainType'` 或 `'mainType.subType'`，例

          ~~~javascript
          chart.on('click', 'series', function () {...});
          chart.on('click', 'series.line', function () {...});
          chart.on('click', 'xAxis.category', function () {...});
          ~~~

        - 如果为 `Object`

          - 可以包含以下的一个或多个属性，每个属性都是可选的

            ~~~javascript
            {
                <mainType>Index: number // 组件 index
                <mainType>Name: string // 组件 name
                <mainType>Id: string // 组件 id
                dataIndex: number // 数据项 index
                name: string // 数据项 name
                dataType: string // 数据项 type，如关系图中的 'node', 'edge'
                element: string // 自定义系列中的 el 的 name
            }
            ~~~

          - 例

            ~~~javascript
              chart.setOption({
                  // ...
                  series: [{
                      name: 'uuu'
                      // ...
                  }]
              });
            
              chart.on('mouseover', {seriesName: 'uuu'}, function () {
                  // series name 为 'uuu' 的系列中的图形元素被 'mouseover' 时，此方法被回调。
              });
            ~~~

            ~~~javascript
              chart.setOption({
                  // ...
                  series: [{
                      // ...
                  }, {
                      // ...
                      data: [
                          {name: 'xx', value: 121},
                          {name: 'yy', value: 33}
                      ]
                  }]
              });
            
              chart.on('mouseover', {seriesIndex: 1, name: 'xx'}, function () {
                  // series index 1 的系列中的 name 为 'xx' 的元素被 'mouseover' 时，此方法被回调。
              });
            ~~~

            ~~~javascript
              chart.setOption({
                  // ...
                  series: [{
                      type: 'graph',
                      nodes: [{name: 'a', value: 10}, {name: 'b', value: 20}],
                      edges: [{source: 0, target: 1}]
                  }]
              });
            
              chart.on('click', {dataType: 'node'}, function () {
                  // 关系图的节点被点击时此方法被回调。
              });
              chart.on('click', {dataType: 'edge'}, function () {
                  // 关系图的边被点击时此方法被回调。
              });
            ~~~

            ~~~javascript
              chart.setOption({
                  // ...
                  series: {
                      // ...
                      type: 'custom',
                      renderItem: function (params, api) {
                          return {
                              type: 'group',
                              children: [{
                                  type: 'circle',
                                  name: 'my_el',
                                  // ...
                              }, {
                                  // ...
                              }]
                          }
                      },
                      data: [[12, 33]]
                  }
              })
            
              chart.on('mouseup', {element: 'my_el'}, function () {
                  // name 为 'my_el' 的元素被 'mouseup' 时，此方法被回调。
              });
            ~~~

    - `handler`

      - 事件处理函数

    - `context`

      - 事件处理函数的上下文，即 `this` 指向

- `echartsInstance.off(eventName[, handler])`

  - 解绑事件函数
  - 接收参数
    - `eventName`
      - 事件名称
    - `handler`
      - 要解绑的事件处理函数

##### `action` 与 `events`

- [Documentation - Apache ECharts](https://echarts.apache.org/zh/api.html#action)
- [Documentation - Apache ECharts](https://echarts.apache.org/zh/api.html#events)

