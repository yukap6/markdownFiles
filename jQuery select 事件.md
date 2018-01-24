## input 元素的 select() 事件触发在不同浏览器下的异同

以下测试都基于如下环境

> Chrome: Version 63.0.3239.84 (Official Build) (64-bit)
> Firefox: 56.0.2 (64-bit)
> Safari: Version 11.0.1 (13604.3.5)
> 操作系统：Mac OS Version 10.13.1 (17B1003)
> 硬件：Mac Air

### 原生 JavaScript 事件注册监听

核心源码如下（引入了 jQuery 方便调试），[demo 地址](https://jsfiddle.net/yukap/LnvfkoxL/2)

```
// html
<input id="input" type="text" value="Hello World" />
<p>请试着选取输入域中的文本，看看会发生什么。</p>
<button id="btn">触发输入域中的 select 事件</button>

// javascript
var inputEle = document.getElementById("input");
var btn = document.getElementById("btn");
btn.addEventListener('click', (e) => {
  inputEle.select();
});
inputEle.addEventListener('select', (e) => {
  $(inputEle).after('text marked! ');
});
```

点击按钮 `button` 之后，在按钮的点击事件中通过 JavaScript 脚本触发 `input` 的 `select` 事件，结果如下

| 浏览器\表现   | 是否选中 `input` 文本 | `select` 监听函数执行次数  | `select` 事件 `eventPhase` (所处阶段) |
| ------------- |:-------------:| :-----: | -----:|
| Chrome      | 是 | 2或1 | 2 |
| Safari      | 是 | 0   | 未知 |
| Firefox     | 是 | 1   | 2 |

各个浏览器的文本都正常选中，其他表现差异如下

* Firefox 最符合预期，文本正常选中，`select` 事件监听函数执行1次。
* Chrome 诡异的地方在于第一次触发按钮点击的时候，`select` 事件监听函数会被执行2次，之后就会恢复正常的1次。
* Safari 文本正常选中，但是并没有触发 `select` 事件监听函数的执行。

假设 HTML 代码不变，JavaScript 代码调整如下，[demo 地址](https://jsfiddle.net/yukap/LnvfkoxL/3/)

```
var inputEle = document.getElementById("input");
inputEle.addEventListener('select', (e) => {
  $(inputEle).after('text marked! ');
});
inputEle.select();
```

监听事件后，直接通过脚本触发 `select` 事件，结果如下

| 浏览器\表现   | 是否选中 `input` 文本 | `select` 监听函数执行次数  | `select` 事件 `eventPhase` (所处阶段) |
| ------------- |:-------------:| :-----: | -----:|
| Chrome      | 是 | 1 | 2 |
| Safari      | 是 | 0 | 未知 |
| Firefox     | 否 | 1 | 2 |

Chrome 文本选中，`select` 事件监听函数执行1次，符合预期；Firefox 文本未选中，`select` 事件监听函数执行1次，不符合预期；Safari 文本选中，`select` 事件监听函数未执行。

继续保持 HTML 代码不变，JavaScript 代码调整如下，[demo 地址](https://jsfiddle.net/yukap/LnvfkoxL/4/)

```
var inputEle = document.getElementById("input");
inputEle.addEventListener('select', (e) => {
  $(inputEle).after('text marked! ');
});
```

通过直接选择 `input` 输入框的文本来触发 `select` 事件，结果如下

| 浏览器\表现   | 是否选中 `input` 文本 | `select` 监听函数执行次数  | `select` 事件 `eventPhase` (所处阶段) |
| ------------- |:-------------:| :-----: | -----:|
| Chrome      | 是 | 1 | 2 |
| Safari      | 是 | n | 2 |
| Firefox     | 是 | 1 | 2 |

Chrome 和 Firefox 表现都符合预期，特殊的是 Safari。Safari下，当按下鼠标左键拖动选择的过程中，`select` 事件监听函数一直被触发执行，直到拖动停止，每有一个字符被选中或取消选中，就会执行一次 `select` 事件监听函数。

以上 3 种 case 的按钮事件注册都是通过 `addEventLitener` 注册添加的，经实际测验，通过 HTML 属性绑定事件和 DOM 2 级 `dom.onclick` 方式注册按钮监听函数，结果一致。

小结：`select` 事件触发时，文本选中在各个浏览器都符合预期，而监听函数的执行则在各个浏览器中表现有差异。

### 使用 jQuery 进行事件注册监听

核心代码如下，[demo 地址](https://jsfiddle.net/yukap/LnvfkoxL/9/)

```
// html
<input id="input" type="text" value="Hello World" />
<p>请试着选取输入域中的文本，看看会发生什么。</p>
<button id="btn">触发输入域中的 select 事件</button>

// javascript
$("#input").select(function(e){
  $("#input").after(" Text marked!");
});
$("#btn").click(function(){
  $("#input").select();
});
```

点击按钮 `button` 之后，结果如下

| 浏览器\表现   | 是否选中 `input` 文本 | `select` 事件监听函数执行次数  | `select` 事件 `eventPhase` (所处阶段) |
| ------------- |:-------------:| :-----: | -----:|
| Chrome      | 是 | 3或2 | 2或`undefined` |
| Safari      | 是 | 1 | `undefined` |
| Firefox     | 是 | 1 | `undefined` |

Chrome 表现最诡异，第一次会触发执行 3 次 `select` 事件监听函数，事件所处状态分别是 2、2、`undefined`，第二次触发执行 2 次 `select` 事件监听函数，事件所处状态分别是 2、`undefined`；Safari 和 Firefox 表现一致，`selecct`事件监听函数执行 1 次，事件所处状态 `undefined`，表现符合预期。这里有个统一的疑问就是事件状态 `event.eventPhase` 都是 `undefined`（需要注意的是，这里的 `e` 是 jQuery 自定义事件类型 `jQuery.Event`）。

HTML 代码保持不变，调整 JavaScript 代码如下

```
$("#input").select(function(e){
  e.preventDefault();
  $("#input").after(e.eventPhase + " Text marked!");
});
$("#btn").click(function(e){
  $("#input").select();
});
```

点击按钮 `button` 之后，结果如下

| 浏览器\表现   | 是否选中 `input` 文本 | `select` 事件监听函数执行次数  | `select` 事件 `eventPhase` (所处阶段) |
| ------------- |:-------------:| :-----: | -----:|
| Chrome      | 否 | 1 | `undefined` |
| Safari      | 否 | 1 | `undefined` |
| Firefox     | 否 | 1 | `undefined` |

各个浏览器表现一致，文本未选中，`select` 事件监听函数执行 1 次，事件所处阶段 `undefined`。

继续 HTML 代码保持不变，调整 JavaScript 代码如下

```
$("#input").select(function(e){
  $("#input").after(e.eventPhase + " Text marked!");
});
```

通过直接选择 `input` 输入框的文本来触发 `select` 事件，结果如下

| 浏览器\表现   | 是否选中 `input` 文本 | `select` 事件监听函数执行次数  | `select` 事件 `eventPhase` (所处阶段) |
| ------------- |:-------------:| :-----: | -----:|
| Chrome      | 是 | 1 | 2 |
| Safari      | 是 | n | 2 |
| Firefox     | 是 | 1 | 2 |

此时各个浏览器表现符合预期，和原生 JavaScript 的执行结果也一致。

小结：无论是原生 JavaScript 还是 jQuery，当通过选择输入框的文本直接触发 `select` 事件的时候，几个浏览器的表现稳定。当通过脚本触发 `select` 事件的时候，表现各有不同。

那么，差异这么多，原因是什么呢？

* jQuery 事件添加都是用的 `addEventListener` 么? （event.js 190）
* jQuery 事件优化了哪些？做了哪些浏览器兼容处理？

## 相关扩展

> 调研跟踪这个主图的时候，涉及到了很多概念和方法，总结如下


### e.target 和 e.currentTarget 的关系

`e.currentTarget` 表示绑定当前事件的 DOM 元素对象；`e.target` 表示触发当前事件的 DOM 元素对象，举个栗子

```
// html
<div id="divContainer" style="padding: 10px;background-color: #efefef;" >
  <button id="btn3">正常 click 事件</button>
</div>

// javascript
document.getElementById("divContainer").addEventListener('click', (e) => {
  console.log(e.currentTarget, e.target);
});
```

当鼠标在 `div[id=divContainer]` 上触发点击事件时，`e.currentTarget` 和 `e.target` 都指向 `div[id=divContainer]`。当鼠标在 `button[id=btn3]` 上触发点击事件时，`e.currentTarget` 指向 `div[id=divContainer]`，`e.target` 指向 `button[id=btn3]`。也就是 `e.currentTarget` 在绑定事件的时候就确定了，是绑定事件的元素对象；`e.target` 则取决于事件的直接触发对象，是变化的。

### console.log 的正确姿势

当 `console.log(obj)` 时，如果 `obj` 是一个对象，则需要特别注意：（以下为 MDN 原文）
> Please be warned that if you log objects in the latest versions of Chrome and Firefox what you get logged on the console is a reference to the object, which is not necessarily the 'value' of the object at the moment in time you call console.log(), but it is the value of the object at the moment you click it open.

翻译成白话文就是
> 如果要打印一个对象，那么控制台的输出是对该对象的引用，而不是该对象在你调用 `console.log` 时的状态。只有当你在控制台点击展开按钮展开该对象的时候，才会去获取该对象的值并展示。举个栗子(在按钮上点击)

```
// html
<div id="divContainer" style="padding: 10px;background-color: #efefef;" >
  <button id="btn3">正常 click 事件</button>
</div>

// javascript
document.getElementById("divContainer").addEventListener('click', (e) => {
  console.log(e.eventPhase, e.currentTarget, e.target);
  console.log(e);
});
```

执行结果如下图：
![](https://private-alipayobjects.alipay.com/alipay-rmsdeploy-image/skylark/png/67baafd1-0618-4f3f-b4d8-ec4f3a5ebe11.png) 

这里会看到第一次打印的 `e.eventPhase` 值为3, 事件处于冒泡阶段, `e.currentTarget` 指向 `div[id=divContainer]`, `e.target` 指向 `button[id=btn3]`，数据正常。有问题的地方在于 `console.log(e)` 的展开结果，`e.currentTarget` 为 `null`, `e.eventPhase` 为0。这就是因为当点击展开按钮展开 `e` 对象的时候，看到的数据是此时 `e` 对象的值，而非代码执行时的 `e` 对象的值，此时已经没有任何处理事件了，所以 `eventPhase` 为0，没有了事件对象，那么和事件对象绑定的 `currentTarget` 也就为 `null` 了。
> 这里要注意的就是，浏览器控制台的输出未展开时，默认是显示部分快照数据，其实是正确的，展开之后会发现数据反而变化了

所以打印一个对象建议使用 `console.log(JSON.parse(JSON.stringify(obj)));` 来显示对象快照，而非 `console.log(obj);`。

存在一个特殊 case，假如在如上示例代码最后添加代码 `console.log(JSON.parse(JSON.stringify(e)));`，看到的结果应该是：

```
{isTrusted: true}
```

这里跟我们预期大不一样，没有 `eventPhase`，没有 `target`，甚至也没有 `currentTarget`，原因就在于下一小节要提到的 `JSON.stringify()` 注意事项。

### JSON.stringify() 需要注意的事项

* 非数组对象的属性不能保证以特定的顺序出现在序列化后的字符串中。
* 布尔值、数字、字符串的包装对象在序列化过程中会自动转换成对应的原始值。
* undefined、任意的函数以及 symbol 值，在序列化过程中会被忽略（出现在非数组对象的属性值中时）或者被转换成 null（出现在数组中时）。
* 所有以 symbol 为属性键的属性都会被完全忽略掉，即便 replacer 参数中强制指定包含了它们。
* 不可枚举的属性会被忽略

> 上文 `console.log(JSON.parse(JSON.stringify(e)))` 之所以展示信息不完整，就是因为大部分数据是不可枚举的，需要特别注意

### event.eventPhase 的取值

* 0 `Event.NONE` 当前无任何事件处于处理中
* 1 `Event.CAPTURING_PHASE` 事件处于捕获阶段
* 2 `Event.AT_TARGET` 事件处于目标阶段
* 3 `Event.BUBBLING_PHASE` 事件处于冒泡阶段

这里除了我们日常认知的捕获、目标及冒泡三个阶段外，多了状态0，表示当前无任何事件处于处理状态。最典型的应用就是 `console.log(e)` 在控制台查看数据的时候，会看到 `eventPhase` 取值0。

（完）

参考链接

* http://www.cnblogs.com/w-wanglei/p/5662067.html
* 


