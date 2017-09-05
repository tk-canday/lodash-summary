# <center> lodash </center>

[TOC]

## 起源

数据处理模式

​    它已经成为Underscore的超集，提供更一致的API行为，更多功能（如AMD支持，深层克隆和深度合并），更全面的[文档](http://lodash.com/docs)和单元测试（在Node，Ringo，Rhino，Narwhal，PhantomJS中运行的测试和浏览器），更好的整体性能和大型数组/对象迭代的优化，以及更多的自定义构建和模板预编译实用程序的灵活性。

​    现在很多主要的npm包都依赖于lodash，如JavaScript转译器[Babel](https://babeljs.io/)、博客平台[Ghost](https://ghost.org/)等。其中Ghost是从Underscore迁移到了lodash。

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
})(this)                  // 1 is not a function

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

`lodash/fp` 模块提供了更接近函数式编程的开发方式，其内部的函数经过包装，具有 immutable、auto-curried、iteratee-first、data-last 等特点。Lodash 在 [GitHub Wiki](https://github.com/lodash/lodash/wiki/FP-Guide) 中对 lodash/fp 的特点做了如下概述：

- Fixed Arity，固化参数个数，便于柯里化
- Rearragned Arguments，重新调整参数位置，便于函数之间的聚合
- Capped Iteratee Argument，封装 Iteratee 参数
- New Methods

```js
// The `lodash/map` iteratee receives three arguments:
// (value, index|key, collection)
_.map(['6', '8', '10'], parseInt);
// → [6, NaN, 2]

// The `lodash/fp/map` iteratee is capped at one argument:
// (value)
fp.map(parseInt)(['6', '8', '10']);
// → [6, 8, 10]


// `lodash/padStart` accepts an optional `chars` param.
_.padStart('a', 3, '-')
// → '--a'

// `lodash/fp/padStart` does not.
fp.padStart(3)('a');
// → '  a'
fp.padCharsStart('-')(3)('a');
// → '--a'


// `lodash/filter` is data-first iteratee-last:
// (collection, iteratee)
var compact = _.partial(_.filter, _, Boolean);
compact(['a', null, 'c']);
// → ['a', 'c']

// `lodash/fp/filter` is iteratee-first data-last:
// (iteratee, collection)
var compact = fp.filter(Boolean);
compact(['a', null, 'c']);
// → ['a', 'c']
```

---

## 特色

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

// lodash

if(_.get(this.props.parent, 'data.data.somPro')){
    // do somthing
};
```

2. 数组的空处理

例如：要迭代一个 new Array() 出来的数组

```js
let arr = new Array(5).map(() => 1);
console.log(arr);    //  [,,,,,] length为5的空数组， chrom为 [undefined x 5]

// 原生的处理：
arr1 = Array.from({length:5}).map(()=> 1)
console.log(arr1);          // [1,1,1,1,1]

// lodash 的处理
_.map(new Array(5),() => 1)
```



### API 增强：

1. Array.prototype.filter 与 _.filter([array], [obj]);
2. 所有的迭代方法都不会改变原对象，而是返回一个新的对象


## 性能

- **惰性求值  （Lazy Evaluation）**