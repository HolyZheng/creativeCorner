### js中的八种数据类型
基础类型：undefined、Null、Boolean、String、Number、Symbol、BigInt
引用类型：Object

### Object引用类型
Object引用类型又分为：Array、RegExp、Date、Math、Function

### 内存存储方式
1. 基础类型存储在栈内存中
2. 引用类型存储在堆内存中

## 判断数据类型的方法
### 一、typeof
```js
typeof 1 // 'number'
typeof '1' // 'string'
typeof undefined // 'undefined'
typeof true // 'boolean'
typeof Symbol() // 'symbol'
typeof null // 'object'
typeof [] // 'object'
typeof {} // 'object'
typeof console // 'object'
typeof console.log // 'function'
```
可以用来判断非null的基础类型，但是如果是引用类型的话，就会不准确：
1. typeof null 会返回Object，js的bug。
2. 引用类型除了Function，其余全返回Object。

### 二、instanceof
用来判断当前对象是否是某个构造函数生产的对象。
```js
let Car = function() {}
let benz = new Car()
benz instanceof Car // true
let car = new String('Mercedes Benz')
car instanceof String // true
let str = 'Covid-19'
str instanceof String // false
let num = new Number(1)
num instanceof Number // true
num = 1;
num instanceof Number // false
```
自己实现一个instanceof
```js
function myInstanceof(left, right) {
    // 如果left是基础类型，返回false
    if (typeof left !== 'object' || typeof left === null) return false;
    let proto = Object.getPrototypeOf(left);
    while(true) {
        if (proto === null) return false;
        if (proto === right.prototype) return true;
        proto = Object.getPrototypeOf(propo);
    }
}
```
### 推荐使用 Object.prototype.toString
```js
Object.prototype.toString({})       // "[object Object]"
Object.prototype.toString.call({})  // 同上结果，加上call也ok
Object.prototype.toString.call(1)    // "[object Number]"
Object.prototype.toString.call('1')  // "[object String]"
Object.prototype.toString.call(true)  // "[object Boolean]"
Object.prototype.toString.call(function(){})  // "[object Function]"
Object.prototype.toString.call(null)   //"[object Null]"
Object.prototype.toString.call(undefined) //"[object Undefined]"
Object.prototype.toString.call(/123/g)    //"[object RegExp]"
Object.prototype.toString.call(new Date()) //"[object Date]"
Object.prototype.toString.call([])       //"[object Array]"
Object.prototype.toString.call(document)  //"[object HTMLDocument]"
Object.prototype.toString.call(window)   //"[object Window]"
```
### 推荐一个类型判断的工具函数
```js
function getType(obj){
  // 对于typeof返回结果是object的，再进行如下的判断，正则返回结果
  return Object.prototype.toString.call(obj).replace(/^\[object (\S+)\]$/, '$1');  // 注意正则中间有个空格
}
```
