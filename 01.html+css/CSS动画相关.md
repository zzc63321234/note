# CSS动画相关

## 渐变

- 语法

  background-image: linear-gradient(方向, 起始颜色, 终止颜色)

## 平面转换

- 属性名：transform

### 位移

- 语法

  transform: translate(水平移动的距离, 垂直移动的距离);

- 取值（正负均可）

  - 像素单位数值
  - 百分比（参考物为盒子自身尺寸）

- 注意

  - translate()如果只给出一个值，表示x轴方向移动距离
  - 单独设置某个方向的移动距离：translateX()和translateY()

### 旋转

- 语法

  transform: rotate(角度);

  角度单位为deg

### 转换原点

- 语法

  tranform-origin: 原点水平位置 原本垂直位置;

- 取值

  - 方位名词
  - 像素单位数值
  - 百分比（参考物为盒子自身尺寸）

- 注意

  默认原点是盒子中心点

### transform复合属性（位移+旋转）

- 语法

  transform: translate() rotate();

- 注意

  - 旋转会改变网页元素的坐标轴向
  - 若先写旋转，则后面的位移转换效果则以旋转后的轴向为准
  - CSS属性具有层叠性，当使用transform复合属性时需要将所有效果写在一起，否则未写的效果将会被覆盖为默认值

### 缩放

- 语法

  transform: scale(x轴缩放倍数, y轴缩放倍数);

- 注意

  一般情况下，只为scale设置一个值，表示x轴与y轴等比例缩放

## 空间转换

### 透视效果

- 将需要实现透视效果的元素的父级添加perspective属性

- 属性

  perspective

- 取值

  像素单位数值，一般取800-1200，该值为模拟透视效果中人眼到屏幕的距离

### 立体呈现

- 语法

  transform-style: preserve-3d;

- 添加立体呈现属性值后，该元素可其内部元素的空间转换来呈现立体图形

### 空间位移

- 语法
  - transform: translate3d(<u>x</u>, <u>y</u>, <u>z</u>);
  - transform: translateX();
  - transform: translateY();
  - transform: translateZ();

### 空间旋转

- 语法

  - transform: rotateZ();
  - transform: rotateX();
  - transform: rotateY();
  - transform: rotate3d(<u>x</u>,<u>y</u>,<u>z</u>,角度度数);

- 取值

  ratate3d(<u>x</u>,<u>y</u>,<u>z</u>,角度度数);中，x,y,z的取值为0-1之间的数字，用来控制旋转轴的方向

## 动画

- 动画效果：实现多个状态间的变化过程，动画过程可控（重复播放、最终画面、是否暂停）

- 实现步骤

  - 定义动画

    ~~~css
    // 两个动画帧
    @keyframes 动画名称 {
        from {}
        to {}
    }
    ~~~

    ~~~css
    // 多个动画帧
    @keyframes 动画名称 {
        0% {}
        10% {}
        15% {}
        100% {}
    }
    ~~~

  - 使用动画

    ~~~css
    // 简单写法
    animation: 动画名称 动画时长;
    ~~~

    ~~~css
    // 完整写法
    animation: 动画名称 动画时长 速度曲线 延迟时间 重复次数 动画方向 执行完毕时状态;
    ~~~

    - 动画名称和动画时长必须赋值
    - 取值不分先后顺序
    - 如果有两个时间值，第一个时间表示动画时长，第二个时间表示延迟时间
    - animation相关属性
      - animation-name 动画名称
      - animation-duration 动画时长
      - animation delay 延迟时间
      - animation-fill-mode 动画执行完毕时状态
        - forwards  最后一帧状态
        - backwards  第一帧状态
      - animation-timing-function 速度曲线
        - steps(数字) 逐帧动画
      - animation-iteration-count 重复次数
        - infinite 无限循环
      - animation-direction 动画执行方向
        - alternate 反方向
      - animation-play-state 暂停动画
        - paused 暂停，通常配合`:hover`使用

  - 使用多组动画

    ~~~css
    animation: 动画1, 动画2, 动画N;
    ~~~

    