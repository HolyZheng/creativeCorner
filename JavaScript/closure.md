## 作用域
作用域就是指变量能够被访问到的范围，比如全局作用域、函数作用域、块级作用域。

## 作用域链
在函数调用的时候会给该函数创建一个作用域链，作用域链第一位为当前函数执行环境对应的变量对象，第二位对应该函数的外部函数的执行环境对应的变量对象，直到最后以为对应全局执行环境的变量对象，它的作用在于保证执行环境对有权访问的变量和函数的有序访问。

### 延长作用域链的方法
try catch语句的catch模块，它会在作用域链的顶端临时增加一个变量对象，并在代码执行后移除。

### 变量提升
JavaScript在编译阶段会把变量和函数的声明放入内存中，所有我们可以在代码中函数声明前正常使用函数，在变量声明前使用变量不会报错（值为undefined）。
ps: es6引入的const和let不存在变量提升。

### 闭包
闭包是指有权访问另一个函数作用域中变量的函数，常见的闭包创建方式是在一个函数内部创建另一个函数。

```js
function wrap () {
  var data = 'something'
  return function() {
    console.log(data)
  }
}
var  fun = wrap()
fun() // 'something'
```
闭包产生的原因是内部函数的作用域链中包含了外部函数的作用域。

## 闭包的表现形式
1. 一个函数内部创建另一个函数（如上）
2. 日常开发中各种回调函数
```js
// 定时器
setTimeout(function handler(){
  console.log('1');
}，1000);
// 事件监听
$('#app').click(function(){
  console.log('Event Listener');
});
```
3. 立即执行函数
```js
var a = 2;
(function IIFE(){
  console.log(a);  // 输出2
})();
```

## 经典循环输出问题
```js
for(var i = 1; i <= 5; i ++){
  setTimeout(function() {
    console.log(i)
  }, 0)
}
```
输出了5个6，原因：
1. setTimout的为宏任务，在主线程同步任务执行完后才执行。因此for循环结束后才执行setTimeout的回调
2. var 声明的变量为非块级作用域，所以每次for循环操作的是同一个i变量，循环结束后值变为6
3. setTimout中的回调沿着作用域链找到i并输出，此时拿到的都为6

### 解决方法
1. 利用IIFE
```js
for(var i = 1;i <= 5;i++){
  (function(j){
    setTimeout(function timer(){
      console.log(j)
    }, 0)
  })(i)
}
```
2. es6 中的let
```js
for(let i = 1; i <= 5; i++){
  setTimeout(function() {
    console.log(i);
  },0)
}
```
3. 定时器的第三个参数
```js
for(var i=1;i<=5;i++){
  setTimeout(function(j) {
    console.log(j)
  }, 0, i)
}
```
