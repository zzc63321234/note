- 保持固定宽高比

  ~~~html
  <div class="outer">
  	<div class="inner"></div>
  </view>
  ~~~

  ~~~css
  .outer{
      position: relative;
  	width: 100%;
  	height: 0;
  	padding-bottom: 100%; 
  }
  
  .inner{
      position: absolute;
  	width: 100%;
  	height: 100%;
  }
  ~~~

  