## new 一个对象的过程
```js
function Person() { 
  this.name = 'holz' 
} 
let p = new Person();
```
1. 创建一个空对象 let obj = {}；
2. 将空对象原型指向构造函数的原型， obj.__proto__ = Person.prototype；
3. 使用空对象作为执行上下文执行构造函数Person.call(obj)，来设置空对象的属性；
4. 返回这个空对象。

### 如果构造函数返回一个对象
new 关键词会直接返回这个对象
```js
function Person(){
   this.name = 'Jack'; 
   return {age: 18}
}
var p = new Person(); 
console.log(p)  // {age: 18}
console.log(p.name) // undefined
console.log(p.age) // 18
```
### 如果返回的是一个函数
new 关键词会返回这个函数
```js
function Person(){
   this.name = 'Jack'; 
   return function() {}
}
var p = new Person();
console.log(p) // f(){}
```
### 如果返回的不是对象
会正常执行new的逻辑，忽略构造函数的返回。
```js
function Person(){
   this.name = 'Jack'; 
   return 'tom';
}
var p = new Person(); 
console.log(p)  // {name: 'Jack'}
console.log(p.name) // Jack
```
也就是说：**new关键词执行之后总是会返回一个对象，要么是实例对象，要么是构造函数return语句返回的对象或函数。**

### 手动实现一个new函数
```js
function _new(ctor, ...args) {
    if (typeof ctor !== 'function') {
        throw 'constructor must be a function'
    }
    // 1. 生产一个空对象
    let obj = new Object();
    // 2. 对象原型指向构造函数原型
    obj.__proto__ = ctor.prototype;
    // 3. 以空对象为执行上下文执行构造函数，来设置属性，并拿到return的值
    const res = ctor.call(obj, ...args);
    const isObject = typeof res === 'object' && typeof res !== null;
    const isFunction = typeof res === 'function';
    return isObject || isFunction ? res : obj;
}
```

## 手写call和apply
```js
Function.prototype.myCall = function(ctx, ...args) {
    let ctx = ctx || window;
    // 以ctx为上下文来执行当前函数
    ctx.fn = this;
    let result = ctx.fn(...args);
    delete ctx.fn;
    return result;
}
```
```js
Function.prototype.myApply = function(ctx, args) {
    let ctx = ctx || window;
    // 以ctx为上下文执行当前函数
    ctx.fn = this;
    let result;
    if (args) {
        result = ctx.fn(...args);
    } else {
        result = ctx.fn()
    }
    delete ctx.fn;
    return result;
}
```
```js
Function.prorotype.myBind = function (ctx, ...args) {
    if (typeof this !== 'function') {
        throw new Error('this must be a function');
    }
    const self = this;
    const fBound = function() {
        // fBound 可能这么使用 new fBound();
        self.apply(this instanceof self ? this : ctx, args.concat(Array.prototype.slice.call(arguments)));
    }
    if (this.prototype) {
        fBound.prototype = Object.create(this.prototype);
    }
    return fBound;
}
```
