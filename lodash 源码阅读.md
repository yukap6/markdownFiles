# lodash 源码阅读

**add.js**

作用：两数相加。

> 依赖 `./.internal/createMathOperation.js`，在 `createMathOperation.js` 中进行了数据预处理（过滤非法数据，使用了经典的闭包操作）。 `./.internal/baseToString.js` 转换变量为字符串，`./.internal/baseToNumber.js` 转换变量为数字，同样都进行了一些预处理。



**after.js**

作用：`after` 函数执行 `n` 或者大于 `n` 次之后，执行 `func` 函数。

语法；`after(n, func)`。

> 可以用于多个异步操作之后，执行结尾操作（使用闭包来记住变量 `n` 的值）。

**ary.js**

作用：*Creates a function that invokes `func`, with up to `n` arguments, ignoring any additional arguments*.

语法：`ary(func, n)`。

> 关键依赖 `./.internal/createWrap.js` 在项目源码中没找到文件，😢。

**assignWith.js**

作用：This method is like `assign` except that it accepts `customizer` which is invoked to produce the assigned values. If `customizer` returns `undefined`, assignment is handled by the method instead. The `customizer` is invoked with five arguments: (objValue, srcValue, key, object, source).

语法：

> 关键依赖 `./.internal/createAssigner.js` 项目中文件不存在，😭。

**at.js**

语法：`at(object, ...paths)`。

作用：根据指定路径来获取对象在对应路径上的值，并以数组形式返回。

