# <center> lodash </center>

[TOC]

### 在此之前：

>  两个问题：

```js
// 1.
let a = new Array(5).map(v => {return 5})

// 2.
let b = { c:[1, 2, 3] }

['a', 'b', 'c'].map(v => {
  console.log(v)
})    

console.log(b)        
```



## 起源

​    **lodash** 是一个JavaScript库，它内部封装了诸多对字符串、数组、对象等常见数据类型的处理函数，而不扩展任何内置的对象，其中部分是目前 ECMAScript 尚未制定的规范，但同时被业界所认可的辅助函数。目前每天使用 npm 安装 Lodash 的数量在百万级以上，这在一定程度上证明了其代码的健壮性。

​    它之前是Underscore的一个Fork，随着作者的不断的commits已经成为Underscore的超集，提供更一致的API行为，更多功能（如AMD支持，深层克隆和深度合并），更全面的[文档](http://lodash.com/docs)，更好的整体性能和大型数组/对象迭代的优化，以及更多的自定义构建和模板预编译实用程序的灵活性。

​    现在很多主要的npm包都依赖于lodash，如JavaScript转译器[Babel](https://babeljs.io/)、博客平台[Ghost](https://ghost.org/)等。其中Ghost是从underscore迁移到了lodash。

---

## 安装

在 React + Webpack + Babel(ES6) 的开发环境中，使用 Lodash 需要安装插件 [babel-plugin-lodash](https://www.npmjs.com/package/babel-plugin-lodash) 并更新 Babel 配置文件：

```node
npm install --save lodash
npm install --save-dev babel-plugin-lodash
```

更新 Babel 的配置文件 `.babelrc`:

```json
{
    "presets": [
        "react",
        "es2015",
        "stage-0"
    ],
    "plugins": [
        "lodash"
    ]
}
```

---

## 结构

### 作用域

与其他第三库一样，_ 也通过 **闭包** 来包裹自己的业务逻辑。

\```js

```js
;(function(){
  // .....执行逻辑
}.call(this))
```

\```

> **一般而言，这些库的闭包结构主要有以下目的：**

> - 避免全局污染：所有库的逻辑，库所定义和使用的变量全部被封装到了该函数的作用域中。

> + 隐私保护：但凡在立即执行函数中声明的函数、变量等，除非是自己想暴露，否则不能在外部获得。



### 分号

1. 前面为什么要加一个分号呢 ？ 
   其作用只有一个，就是闭合前面语句。

    **代码风格**：分号、大括号、 return的括号，缩进....

代码风格本身并没有什么优劣，但在编码时候要需要了解一下JavaScript的特性，编码时候不注意可能会产生一些暗坑，比如说：

```js
// 圆括号前面语句未闭合引发的问题
let a = 1
(() => {
    // do somthing
})()                  // 1 is not a function

// 上面这种情况还是好的，至少还能报个错，搞不好某些情况下类似问题代码还能运行，例如：
let b = { c:[1, 2, 3] }

['a', 'b', 'c'].map(v => {
  console.log(v)         // 1,2,3  
})    

console.log(b)                       
```



2. 为什么有的时候“；”可以加有时候不加又会出问题呢？

这不是“；”的锅，而是JavaScript有一个 **ASI** 的机制:    [ASI机制的中文解释](https://cnodejs.org/topic/56dbbd25820d3c9b3d63e369)

其实不一定只有分号才可以避免上面例子中出现的问题，~ ，！，^ 等 也可以实现隔离两段语句的效果

很多语言都是可以不加分号的,比如：Go, Scala, Ruby, Python, Swift, Groovy...

其实真正会导致上下行解析出问题的 token 只有 4 个：圆括号，方括号，算数运算符，正则开头的斜杠（除号）。但很少有在代码中用正则、运算符作为行首的情况，所以总结下来就是一句话：当一行的开头是括号或者方括号的时候加上分号就 OK；



---

## 模块组成

- `Array`，适用于数组类型，比如填充数据、查找元素、数组分片等操作
- `Collection`，适用于数组和对象类型，部分适用于字符串，比如分组、查找、过滤等操作
- `Function`，适用于函数类型，比如节流、延迟、缓存、设置钩子等操作
- `Lang`，普遍适用于各种类型，常用于执行类型判断和类型转换
- `Math`，适用于数值类型，常用于执行数学运算
- `Number`，适用于生成随机数，比较数值与数值区间的关系
- `Object`，适用于对象类型，常用于对象的创建、扩展、类型转换、检索、集合等操作
- `Seq`，常用于创建链式调用，提高执行性能（惰性计算）
- `String`，适用于字符串类型

`lodash/fp` 模块提供了更接近函数式编程的开发方式，其内部的函数经过包装，具有 immutable(固定参数)、auto-curried（柯里化）、iteratee-first、data-last 等特点。Lodash 在 [GitHub Wiki](https://github.com/lodash/lodash/wiki/FP-Guide) 中对 lodash/fp 的特点做了如下概述：

- Fixed Arity，固化参数个数，便于柯里化
- Rearragned Arguments，重新调整参数位置，便于函数之间的聚合
- Capped Iteratee Argument，封装 Iteratee 参数
- New Methods

```js
// The `lodash/map` iteratee receives three arguments:
// (value, index|key, collection)
_.map(['6', '8', '10'], parseInt)
// → [6, NaN, 2]

// The `lodash/fp/map` iteratee is capped at one argument:
// (value)
fp.map(parseInt)(['6', '8', '10'])
// → [6, 8, 10]


// `lodash/padStart` accepts an optional `chars` param.
_.padStart('a', 3, '-')
// → '--a'

// `lodash/fp/padStart` does not.
fp.padStart(3)('a')
// → '  a'
fp.padCharsStart('-')(3)('a')
// → '--a'


// `lodash/filter` is data-first iteratee-last:
// (collection, iteratee)
var compact = _.partial(_.filter, _, Boolean);
compact(['a', null, 'c'])
// → ['a', 'c']

// `lodash/fp/filter` is iteratee-first data-last:
// (iteratee, collection)
var compact = fp.filter(Boolean)
compact(['a', null, 'c'])
// → ['a', 'c']
```

---

## 特色



### 占位符

lodash 几乎所有的方法都支持占位符模式：

```js
var whoLoveWho = function(name, like) {
  return `${name} love ${like}`;
};

// 普通的 _.partial 用法
var commonLove = _.partial(whoLoveWho, 'taibowen');
commonLove('fanbingbing');
// => 'taibowen love fanbingbing'

// 使用了占位符
var _love = _.partial(whoLoveWho, _, 'qiqi');
_.partial('liuyichi');
// => 'liuyichi love qiqi'
```



### 柯里化

```js
var arr = function(a, b, c) {
  return [a, b, c];
};

var curried = _.curry(arr);

curried(1)(2)(3);
// => [1, 2, 3]

curried(1, 2)(3);
// => [1, 2, 3]

curried(1, 2, 3);
// => [1, 2, 3]

// 使用了占位符
curried(1)(_, 3)(2);
// => [1, 2, 3]
```







### null-safe 空处理：

在接收数据时候很容易因为某个数据为空而产生程序崩溃：

1. 对象的空处理

例如： 我们写了一个 custom 组件，其中想要利用父组件的某个数据做一些事情,如果直接在属性链上取值，其中某一个属性没有的话会报错，所以要做一些判断处理：

```JS
// 原生

if(this.props.parent.(data || {}).(data || {}).somePro){
    // do somthing
}

// or

if(this.props.parent && this.props.parent.data && this.props.parent.data.data && this.props.paarent.data.data.somPro){
    // do somthing
};

// lodash 会将没有的属性返回一个 "undefined"
if(_.get(this.props.parent, 'data.data.somPro')){
    // do somthing
};
```

2. 数组的空处理

例如：要迭代一个 new Array() 出来的数组

```js
let arr = new Array(5).map(() => 1)
console.log(arr)   //  [,,,,,] length为5的空数组， chrom为 [undefined x 5]

// 原生的处理：
arr1 = Array.from({length:5}).map(()=> 1)
console.log(arr1)          // [1,1,1,1,1]

// lodash 的处理
_.map(new Array(5),() => 1)
```



### 无需参数校验

js是弱类型语言,除非加入各种类型检测,否则很难确定传入的array是Array，这就是为什么有时会选择用_.each(array)代替[].forEach, 此处包括空(null/undefined);



### 链式编程：

```js
const obj={a: {b: {c: {one: 'blue', two: 'red'}}}}
_.map(_.get(obj, 'a.b.c'), (item, key)=> item) 

// chain 开启显示链模式，用value解除(代码量又缩短了，且代码可读性更高)
_.chain(obj).get('a.b.c').map().value()
```



### API 增强：

1. Array.prototype.filter 与 _.filter([array], [obj])

```js
var users = [
  { 'user': 'barney', 'age': 36, 'active': true },
  { 'user': 'fred',   'age': 40, 'active': false }
];

// ES6 Array.prototype.filter  (第二个参数必须是一个函数)
users.filter((v)=>{return !v.active})

// _.filter
_.filter(users, function(v) { return !v.active; });
// 2参是一个fuc

// The `_.matches` iteratee shorthand.
_.filter(users, { 'age': 36, 'active': true });
// 2参是一个obj
 
// The `_.property` iteratee shorthand.
_.filter(users, 'active');
// 2参是一个string
```

2. fp 所有的迭代方法都不会改变原对象，而是返回一个新的对象



### 更多的 API 

1. 深拷贝：

JavaScript 没有直接提供深拷贝的函数，但我们可以用其他函数来模拟，比如 `JSON.parse(JSON.stringify(objectToClone))`，但这种方法要求对象中的属性值不能是函数。或者  用递归把属性链上的所有属性都复制一遍；

Lodash 中的 `_.cloneDeep` 函数封装了深拷贝的逻辑，用起来更加简洁。

```js
var obj = [{ 'a': 1 }, { 'b': 2 }];

var deep = _.cloneDeep(obj);
console.log(deep[0] === obj[0]);
// => false
```

2. 随机数

```js
// 原生过去随机数函数
function getRandomNumber(min, max){
    return Math.floor(Math.random() * (max - min + 1)) + min;
}
getRandomNumber(15, 20);

// Lodash 随机数
_.random(15, 20, false);
```

3. 筛选属性

我们可以使用数组、字符串以及函数的方式筛选对象的属性，并且最终会**返回一个新的对象**，不会对旧对象产生影响。

```js
var obj = {"name": "alin", "car": "bieke", "age": 17};

// Lodash
var newObj = _.pick(objA, ['car', 'age']);
// {"car": "bieke", "age": 17}
```

## 性能

- **惰性求值  （Lazy Evaluation）**

用 lodash 创造的函数函数是 lazy evaluation 的。简单来说，就是当处理过程复杂的情况下，lazy evaluation 可以让计算性能提高非常多。flow 已经自带做了这种性能优化，而原生的链式调用是不会有这种性能优化的。



## 小结：

[10 个可用 ES6 替代的 Lodash 特性](http://www.zcfy.cc/article/10-lodash-features-you-can-replace-with-es6-467.html)