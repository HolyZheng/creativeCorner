### 强制类型转换
强制类型转换方式包括 Number()、parseInt()、parseFloat()、toString()、String()、Boolean()。

#### Number()为例：
1. true -> 1；false -> 0
2. null -> 0
3. undefined -> NaN
4. 字符串：转为十进制
```js
Number('1') === 1
Number('0x10') === 16 // 十六进制
Number(-0x10)  === -16
Number('-0x10') === NaN
Number('0b11') === 3  // 二进制
```
#### Boolean()
除了 undefined、 null、 false、 ''、 0（包括 +0，-0）、 NaN 转换出来是 false，其他都是 true。
```js
Boolean(0)          //false
Boolean(null)       //false
Boolean(undefined)  //false
Boolean(NaN)        //false
Boolean(1)          //true
Boolean(13)         //true
Boolean('12')       //true
```

### 隐式转换
凡是通过逻辑运算符 (&&、 ||、 !)、运算符 (+、-、*、/)、关系操作符 (>、 <、 <= 、>=)、相等运算符 (==) 或者 if/while 条件的操作，如果遇到两个数据类型不一样的情况，都会出现隐式类型转换
#### ==
1. null或undefined只在与互相之间比较的时候才为true；null == undefined; undefined == null
2. 其中一个为symbol，返回false
3. string == number，将string转为number再比较
4. boolean == number，将boolean转为number再比较
5. object == string， 调用object的valueOf/toString转换后进行比较

```js
null == undefined       // true  规则2
null == 0               // false 规则2
'' == null              // false 规则2
'' == 0                 // true  规则4 字符串转隐式转换成Number之后再对比
'123' == 123            // true  规则4 字符串转隐式转换成Number之后再对比
0 == false              // true  e规则 布尔型隐式转换成Number之后再对比
1 == true               // true  e规则 布尔型隐式转换成Number之后再对比
var a = {
  value: 0,
  valueOf: function() {
    this.value++;
    return this.value;
  }
};
// 注意这里a又可以等于1、2、3
console.log(a == 1 && a == 2 && a ==3);  //true f规则 Object隐式转换
```
### + 加号的情况
1. 字符串 + undefined/unll/boolean，先调用toString()后，进行字符串拼接
```js
'1' + undefined   // "1undefined" 规则1，undefined转换字符串
'1' + null        // "1null" 规则1，null转换字符串
'1' + true        // "1true" 规则1，true转换字符串
```
2. 数字 + undefined/null/boolean，先转为数字再进行假发运算
```js
1 + undefined     // NaN  规则2，undefined转换数字相加NaN
1 + null          // 1    规则2，null转换为0
1 + true          // 2    规则2，true转换为1，二者相加为2
```
3. 数字 + 字符串，按字符串规则拼接
```js
'1' + 3           // '13' 规则3，字符串拼接
```
4. 纯对象、数组、正则等，则默认调用对象的转换方法会存在优先级

### Object 的转换规则
1. 调用 Symbol.toPrimitive方法（如果有）
2. 调用 valueOf(), 默认返回自身
3. 调用 toString()
4. 最终应转化为基础类型

```js
var obj = {
  value: 1,
  valueOf() {
    return 2;
  },
  toString() {
    return '3'
  },
  [Symbol.toPrimitive]() {
    return 4
  }
}
console.log(obj + 1); // 输出5
// 因为有Symbol.toPrimitive，就优先执行这个；如果Symbol.toPrimitive这段代码删掉，则执行valueOf打印结果为3；如果valueOf也去掉，则调用toString返回'31'(字符串拼接)
// 再看两个特殊的case：
10 + {}
// "10[object Object]"，注意：{}会默认调用valueOf是{}，不是基础类型继续转换，调用toString，返回结果"[object Object]"，于是和10进行'+'运算，按照字符串拼接规则来，参考'+'的规则C
[1,2,undefined,4,5] + 10
// "1,2,,4,510"，注意[1,2,undefined,4,5]会默认先调用valueOf结果还是这个数组，不是基础数据类型继续转换，也还是调用toString，返回"1,2,,4,5"，然后再和10进行运算，还是按照字符串拼接规则，参考'+'的第3条规则
```
