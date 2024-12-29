# BootStrap

- BootStrap栅格系统

  - 栅格化是指将整个网页的宽度分成若干等份

  - BootStrap3默认将网页分成12等份

    |          | 超小屏幕 |  小屏幕  | 中等屏幕 |  大屏幕  |
    | :------: | :------: | :------: | :------: | :------: |
    | 响应断点 |  <768px  | >=768px  | >=992px  | >=1200px |
    |   别名   |    xs    |    sm    |    md    |    lg    |
    | 容器宽度 |   100%   |  750px   |  970px   |  1170px  |
    |  类前缀  | col-xs-* | col-sm-* | col-md-* | col-lg-* |
    |   列数   |    12    |    12    |    12    |    12    |
    |  列间隙  |   30px   |   30px   |   30px   |   30px   |

    - 类前缀中的*代表该列所占的栅格份数（共12份）

  - container

    - .container是BootStrap中专门提供的类名，所有应用该类名的盒子，默认已被指定宽度且居中
    - .container-fluid也是BootSrap中专门提供的类名，所有应用该类名的盒子，宽度均为100%
    - 分别使用.row类名和.col类名定义栅格布局的行和列
    - 注意：
      1. container类自带左右padding15px
      2. row类自带左右padding-15px，可用于叠在container内抵消左右padding
  
- 媒体查询

  ~~~css
  /* 超小屏幕（手机，小于 768px） */
  /* 没有任何媒体查询相关的代码，因为这在 Bootstrap 中是默认的（还记得 Bootstrap 是移动设备优先的吗？） */
  
  /* 小屏幕（平板，大于等于 768px） */
  @media (min-width: @screen-sm-min) { ... }
  
  /* 中等屏幕（桌面显示器，大于等于 992px） */
  @media (min-width: @screen-md-min) { ... }
  
  /* 大屏幕（大桌面显示器，大于等于 1200px） */
  @media (min-width: @screen-lg-min) { ... }
  ~~~

  ~~~css
  @media (max-width: @screen-xs-max) { ... }
  @media (min-width: @screen-sm-min) and (max-width: @screen-sm-max) { ... }
  @media (min-width: @screen-md-min) and (max-width: @screen-md-max) { ... }
  @media (min-width: @screen-lg-min) { ... }
  ~~~

  