###### 在组件使用ref定位DOM元素，在mounted钩子中获取refs时，有时会出现ref是undefined的情况

- 原因：

  使用了v-for，且循环条件需要用到后台数据的动态DOM节点，在mounted阶段还未获取到后台数据，此时这些节点还没有被渲染生成

  本质是依赖后台数据的动态DOM节点的生成时间的问题

- 解决方法：
  - 在`updated`钩子中操作，此阶段动态DOM节点已经生成完毕
  
    但如果该组件更新其它数据很频繁，则会很浪费性能
  
  - 使用`watch`监视用来遍历的数据，在`handler`中使用`this.$nextTick()`的回调中可以获取更新后的DOM



###### 使用了swiper库时，轮播图上的按钮点击后可能会被执行多次或失灵

- 可能造成的原因：轮播图容器被多个Swiper指定

  1. 应用中存在多个轮播图，在分别创建Swiper实例时，指定容器采用了类选择器的方式指定，则可能会造成有些轮播图被多个Swiper实例指定，造成重复执行

  2. Swiper实例需要在轮播图的结构完全渲染完毕后创建才能成功指定该轮播图容器

     在不知道轮播图生成完毕是否需要等待后台数据的情况下（比如将轮播图封装成了一个组件时），无法确定创建Swiper实例需要在什么阶段进行

     比较暴力的做法是使用`watch`来监视用来遍历生成轮播图的数据，在`handel`中使用`this.$nextTick()`的回调中创建Swiper实例，并为该watch添加`immediate: true`, 这样做是在任何可能的阶段都创建一次Swiper实例，以保证Swiper实例一定指定到了轮播图容器，但这样也可能造成一个轮播图容器被多个Swiper指定

- 解决方法：

  1. 需要分别创建Swiper实例时，使用ref来指定特定的轮播图容器，使得Swiper实例和轮播图容器一一对应

  2. - 如果使用上述暴力做法

       可先在data中创建一个变量（如：mySwiper）用以存储Swiper实例

       在创建Swiper实例之前，先判断

       如果存在`this.mySwiper.destroy`，则执行这个方法以销毁这个已经存在Swiper实例

       之后再创建新的Swiper实例，赋值给`this.mySwiper`

       这样一个容器就能只保留一个最新创建的Swiper实例

     - 可以在使用封装好的轮播图组件时，在组件外判断这个组件需要在哪个阶段创建Swiper实例，并将这个信息作为props传入轮播图组件中

       在轮播图组件中，将创建Swiper的代码封装成函数放入`methods`

       在可能需要创建Swiper实例的生命周期钩子中结合props和if语句来调用生成Swiper代码的methods

       这样就可以只在需要的时候创建Swiper实例，避免了重复创建



###### 使用computed时用到了data中的对象数据，当data中的对象数据内的属性改变时，变化不会被监测到

- 原因：通过点语法直接给data中的对象的属性赋值，由于对象的引用地址不变，因此不会被vue监测到

- 解决方法：使用this.$set(target,key,val)方法给data中的对象修改属性

  ​         注意key是字符串，直接输入记得加引号







### 警告类

###### 取消vscode中的eslint警告

- 消除以下全部警告

  `*<!--  eslint-disable -->*`

- 消除范围内警告

  `*<!--  eslint-disable -->*`

  `......`

  `*<!--  eslint-enable -->*`



###### 使用v-for时用到的数据为计算属性中的数据时，有时控制台会出现`"RangeError: Invalid array length"`警告

- 原因：

  计算属性在初次被读取（此处即在渲染模板时）就会被执行一次

  如果计算属性中所用到的数据需要通过异步处理获得，在第一次执行计算属性时，可能该数据为undefined

  undedined参与计算后可能使得计算属性的结果为NaN

  NaN作为v-for用到的数据，因此会出现该警告

- 解决方法：为计算属性的结果设置一个默认值



###### 删除代码编辑器中 `vue/no-use-v-if-with-v-for` 警告

~~~vue
<!-- eslint-disable vue/no-use-v-if-with-v-for,vue/no-confusing-v-for-v-if -->
~~~



###### 页面正常显示，却出现`Vue warn]: Error in render: “TypeError: Cannot read properties of undefined (reading 'xxx')`警告

- 原因：

  可能模板中用到了发送请求获得的数据

  数据为获得前，所要读取属性的对象仍是undefined

- 解决方法：根据情况，先给要读取到的对象赋默认值或空值，避免其为undefined

