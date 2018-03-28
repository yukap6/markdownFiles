# lodash 源码阅读

**add.js**

作用：两数相加。

> 依赖 `./.internal/createMathOperation.js`，在 `createMathOperation.js` 中进行了数据预处理（过滤非法数据，使用了经典的闭包操作）。 `./.internal/baseToString.js` 转换变量为字符串，`./.internal/baseToNumber.js` 转换变量为数字，同样都进行了一些预处理。



**after.js**

语法；`after(n, func)`。

作用：`after` 函数执行 `n` 或者大于 `n` 次之后，执行 `func` 函数。

> 可以用于多个异步操作之后，执行结尾操作（使用闭包来记住变量 `n` 的值）。

***ary.js***

语法：`ary(func, n)`。

作用：*Creates a function that invokes `func`, with up to `n` arguments, ignoring any additional arguments*.

> 关键依赖 `./.internal/createWrap.js` 在项目源码中没找到文件，😢。

***assignWith.js***

作用：This method is like `assign` except that it accepts `customizer` which is invoked to produce the assigned values. If `customizer` returns `undefined`, assignment is handled by the method instead. The `customizer` is invoked with five arguments: (objValue, srcValue, key, object, source).

语法：

> 关键依赖 `./.internal/createAssigner.js` 项目中文件不存在，😭。

**at.js**

语法：`at(object, ...paths)`。

作用：根据指定路径来获取对象在对应路径上的值，并以数组形式返回。

```
// 依赖树
at.js
    ./.internal/baseAt.js
        ../get.js
            ./.internal/baseGet.js
                ./castPath.js // 将路径处理为路径数组
                    ./isKey.js // 判断是否普通对象 key
                        ../isSymbol.js // 判断是否 Symbol 对象
                            ./.internal/getTag.js // 判断对象类型
                                ./baseGetTag.js // 判断对象类型的基本方法
                    ./stringToPath.js // 路径解析核心方法，用到了缓存，正则
                        ./memoizeCapped.js // 缓存
                            ../memoize.js
                                ./.internal/MapCache.js
                                    ./Hash.js
                                    ./ListCache.js
                                        ./assocIndexOf.js
                                            ../eq.js
                ./toKey.js
                    ../isSymbol.js
```

其中关键实现是在第4层 `./.internal/baseGet.js`，原理就是将要获取数据的 `path` 分解为数组，然后循环去取每一层的数据，直到取出最终数据位置，核心代码如下

```
function baseGet(object, path) {
  path = castPath(path, object)

  let index = 0
  const length = path.length

  while (object != null && index < length) {
    object = object[toKey(path[index++])]
  }
  return (index && index == length) ? object : undefined
}
```

> 众所周知，js 中获取结构比较深的对象数据的时候，是需要提前判断父结构是否存在的，如果存在才能继续取下一层数据，否则会报错，而 `at` 方法则帮助我们自动解决了这一问题

关于 `lodash` 缓存：`lodash` 提供了 `hash.js` 和 `listCache.js` 两种方法来实现缓存。其中 `hash` 存储键值对只支持普通字符串（或者 Symbol 对象）为键的 case；而 `listCache` 则模拟了 `Map` 数据结构，支持用对象作为键（实质是采用数组来存储，第一位存储键，第二位存储值）。

`eq.js` 判断两个变量是否相等，源码如下（这里主要是为了兼容 `NaN !== NaN` 的 case）

```
function eq(value, other) {
  return value === other || (value !== value && other !== other)
}
```

**attempt.js**

语法：`attempt(func, ...args)`

作用：尝试执行函数 `func`，如果报错，则抛出错误，否则正常返回函数执行结果。常见 case 就是省去了我们执行某个函数时候需要的 `try...catch...` 代码。

**before.js**

语法：`before(n, func)`。

作用：`func` 函数可以执行 `n - 1` 次，执行到第 `n` 次之后，则会抛出异常。

***bindKey.js***

> 依赖不完整

**camelCase.js**

语法：`camelCase(string)`。

作用：将字符串 `string` 转换为驼峰格式。

