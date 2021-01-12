## 浅拷贝
如果对象属性是基本的数据类型，复制的就是基本类型的值给新对象；但如果属性是引用数据类型，复制的就是内存中的地址。

### 浅拷贝方式1: object.assign
1. 不会拷贝对象的继承属性
2. 不会拷贝对象的不可枚举属性
3. 可以拷贝Symbol类型的属性

```js
let obj1 = { a:{ b:1 }, sym:Symbol(1)}; 
Object.defineProperty(obj1, 'innumerable' ,{
    value:'不可枚举属性',
    enumerable:false
});
let obj2 = {};
Object.assign(obj2,obj1)

```
### 浅拷贝方式2: 扩展运算符
```js
/* 对象的拷贝 */
let obj = {a:1,b:{c:1}}
let obj2 = {...obj}
obj.b.c = 2
console.log(obj)  //{a:2,b:{c:2}} console.log(obj2); //{a:1,b:{c:2}}
/* 数组的拷贝 */
let arr = [{a:1}, {b:2}];
let newArr = [...arr]; //跟arr.slice()是一样的效果
arr[0].a = 2
newArr[0].a === 2 // true
```

### 浅拷贝方式3: concat
```js
let arr1 = [{a:1}];
let arr2 = [{b:1}];
let arr = arr1.concat(arr2);
arr1[0].a = 2;
arr[0].a === 2 //true
```
### 浅拷贝方式4: slice
```js
let arr = [1, 2, {val: 4}];
let newArr = arr.slice();
newArr[2].val = 1000;
console.log(arr);  //[ 1, 2, { val: 1000 } ]
```

## 深拷贝
深拷贝相对于浅拷贝的不同：对于复杂引用数据类型，其在堆内存中完全开辟了一块内存地址，并将原有的对象完全复制过来存放。
两个对象相互独立、不受影响。

### 深拷贝方式1: JSON.stringfy
但是有一定的限制：
1. 无法拷贝函数、undefined、symbol、
2. Date引用类型会变成字符串
3. 无法拷贝不可枚举的属性
4. RegExp会变成空对象
5. NaN、Infinity及-Infinity会变成null
6. 无法拷贝对象的原型链
7. 无法拷贝对象的循环引用

```js
function Obj() { 
  this.func = function () { alert(1) }; 
  this.obj = {a:1};
  this.arr = [1,2,3];
  this.und = undefined; 
  this.reg = /123/; 
  this.date = new Date(0); 
  this.NaN = NaN;
  this.infinity = Infinity;
  this.sym = Symbol(1);
} 
let obj1 = new Obj();
Object.defineProperty(obj1,'innumerable',{ 
  enumerable:false,
  value:'innumerable'
});
console.log('obj1',obj1);
let str = JSON.stringify(obj1);
let obj2 = JSON.parse(str);
console.log('obj2',obj2);
```

### 深拷贝方法2: 手写（基础版）
核心原理：如果值是引用类型则再次调用该函数
```js
function deepClone(obj) {
    let cloneObj = {};
    for(let key in obj) {
        if (typeof obj[key] === 'object' && obj[key] !== null) {
            cloneObj[key] = deepClone(obj[key])
        } else {
            cloneObj[key] = obj[key]
        }
    }
    return cloneObj;
}
```
缺点：
1. 无法拷贝不可枚举属性和Symbol类型属性
2. 对于Array、Date、RegExp、Error、Function不能正确拷贝
3. 无法正常拷贝循环引用

### 深拷贝改进版
// TODO