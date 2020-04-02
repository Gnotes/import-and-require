# Import-And-Require

基础语法我们就不赘述了哈，大家都知道的是 `require` 是 CommonJS 的规范，而 `import` 是 `ESModule` 的语法规范，那么他们的区别我们就来说一下。

### 执行过程

```
① 解析(resolve): Node 文件路径处理
② 加载(loading): 从对应路径加载文件
③ 包裹(wrapping): 包裹头尾文件字符串(上边的代码)
④ 运算(evaluation): 交给VM引擎运算
⑤ 缓存(caching): 模块缓存已加载的文件
```

**Node** 文件的执行过程:

《深入浅出 nodejs》一书中提到，每个模块文件的 `require`，`exports` 和 `module` 这 3 个变量并 **没有在模块中定义，也并非全局函数/对象** 。而是在编译的时候 **Node** 对 **JS** 文件内容进行了 **头尾的包装**。在头部加了

```js
(function (exports, require, module, **filename, **dirname) {
```

在尾部加了

```js
})
```

这样看起来虽然貌似理解了 require，exports 和 module 的来源

**因此**：在被 VM 执行运算 `Evaluation` (运行时)的时候 Node 才知道`require` 导出的具体是什么值
**然而**：通过 `import` 导入的 `模块`，是词法(Lexical: 简单理解为定义的时候就知道了具体的导出)层上的，在被执行运算 `Evaluation` 之前就已经知道有哪些导出，那也是为什么在导入导出变量中的不匹配时就会报错，而不是代码运行时。

值得注意的是 `import` 关键字 和 ES6 `import()` 函数，**不是同一个东西哈**，`import()` 相当于 `require` 可以在运行时动态引入模块

静态编译

> CommonJS 模块是运行时加载，ES6 模块是编译时输出接口。CommonJS 加载的是一个对象（即 module.exports 属性），该对象只有在脚本运行完才会生成。而 ES6 模块不是对象，它的对外接口只是一种静态定义，在代码静态解析阶段就会生成

动态绑定

> CommonJS 模块输出的是一个值的拷贝，ES6 模块输出的是值的引用。CommonJS 模块输出的是值的拷贝，也就是说，一旦输出一个值，模块内部的变化就影响不到这个值。ES6 模块的运行机制与 CommonJS 不一样。JS 引擎对脚本静态分析的时候，遇到模块加载命令 import，就会生成一个只读引用。等到脚本真正执行时，再根据这个只读引用，到被加载的那个模块里面去取值。换句话说，ES6 的 import 有点像 Unix 系统的“符号连接”，原始值变了，import 加载的值也会跟着变。因此，ES6 模块是动态引用，并且不会缓存值，模块里面的变量绑定其所在的模块

示例:

```js
// index.require.js
var a = 1;
var b = function() {
  a = 2;
};

module.exports = {
  a,
  b,
};
// test.require.js
var m = require("./index.require");

console.log(m.a); // 1
m.b();
console.log(m.a); // 1
```

```js
// index.import.js
export let a = 1;
export const b = function() {
  a = 2;
};

// test.import.js
import { a, b } from "./index.import";

console.log(a); // 1
b();
console.log(a); // 2
```

## 参考

- [深入理解 node 中 require 的原理及执行过程](https://www.jianshu.com/p/609489e8c929)
- [import vs require – ESM & commonJs module differences](http://voidcanvas.com/import-vs-require/)
- [如何理解 es6 中的 import 是静态编译执行的？](https://www.zhihu.com/question/265631914/answer/296374199)
- [读懂 CommonJS 的模块加载](https://www.cnblogs.com/cherryvenus/p/9722304.html)
