- 防抖

  - 连续触发相同的事件时，之前的事件回调都会被取消，只有最后一次触发的事件的回调会被执行

  - 实现方式：

    - `lodash`库/`UnderScore`库

      ~~~javascript
      // 引入lodash.js后，'_'会作为lodash提供的对象
      _.debounce(function(){
          console.log('回调被执行')
      }, 500)
      // _.debounce方法执行后会返回一个新的函数，将该函数作为事件的回调对象
      // 绑定该回调的事件被触发后，回调会在500ms后执行，如果在此事件内事件再次被触发，则上一次的回调不会被执行
      
      // vue脚手架中已存在lodash依赖，可直接在组件中引入lodash
      // 完全引入 import _ from 'lodash'
      // 按需引入 import debounce from 'lodash/debounce'
      ~~~

- 节流

  - 规定一个单位时间,在这个单位时间内,只能有一次触发事件的回调函数执行,如果在同一个单位时间内某事件被触发多次,只有一次能生效

  - 实现方式

    - `lodash`库/`UnderScore`库

      ~~~javascript
      _.throttle(function(){
          console.log('回调被执行')
      }, 500)
      ~~~

- 使用场景

  - 防抖：
    - 用户操作很频繁时，只执行一次
    - 简单理解：取消之前的重复操作
  - 节流：
    - 用户操作很频繁时，把频繁操作减少为少量操作，给浏览器更充裕的时间解析代码
    - 简单理解：取消之后的重复操作