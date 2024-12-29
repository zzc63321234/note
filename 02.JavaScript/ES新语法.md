- 复制对象-ES8语法

  ~~~javascript
  const obj1 = {
      a: 1
  }
  const obj2 = {
      b: 2
  }
  const obj3 = {...obj1, ...obj2}
  console.log(obj3) // {a:1, b:2}
  
  const obj4 = {...obj3, a: 3}
  console.log(obj4) // {a:3, b:2}
  ~~~
  
  