> 本文旨在介绍 `crystal` 配合 `seajs` 开发模块的两种方式，针对 E 表项目 OD 文档开发的同学，或者是需要使用 `seajs` + `crystal` 维护旧项目的同学

[E 表人财 OD 文档项目](http://gitlab.alibaba-inc.com/platform/eproject-2018)模块数据获取有两种方式：

1. 数据从父模块通过 `data-model-path` 属性传递给子模块，子模块通过 `renderModel(model)` 方法获取对应数据
2. 数据完全在子模块内部维护，不需要数据传递

## 方案1 父模块传递数据给子模块

> 这种方式适合一个大模块分为几个小模块，但是所有的数据都是在父模块中从统一接口获取，然后再分发数据给各个子模块的 case

举个栗子：`/src/modules/eproject.evaluation2015/index.handlebars` 是 OD 文档页的唯一主模块，所有核心结构的 `DOM` 都在这个模块的模板文件里渲染，相应的有很多子模块通过 `widget` 的方式进行渲染，如下示例：

```
<div id="J_Advice" 
    class="J_Module" 
    data-widget="~/eproject.evaluation2015.advice/" 
    data-model-path="form/ADVICE" 
    data-profile='{{json profile}}'>
</div>
```

该模块负责渲染发展建议，数据是从父模块 `/src/modules/eproject.evaluation2015/index.js` 的 `AJAX` 请求中异步获取并通过 `data-model-path` 的形式传递给子模块，子模块只需要根据数据进行渲染即可，默认是自动渲染（即绑定 `tpl` 即可）。

## 方案2 子模块单独维护数据

> 这种方式适合新增模块，或者该模块和主模块之间无墙裂耦合关系。比如，2017 年新增的外语能力自评模块 `eproject.evaluation2015.i18n.ability`，数据则是在自身模块的 `fetch` 方法里维护，那么该模块在主模块中通过 `widget` 加载的方式如下示例：

```
<div 
    class="J_PersonalI18nAbility" 
    data-widget="~/eproject.evaluation2015.i18n.ability/"
    data-profile='{{json profile}}'
>
    <div class="kuma-loading-l" style="margin:0 auto;"></div>
</div>
```

区别就在于该模块没有 `data-model-path` 属性，所以所需数据从 `fetch` 方法里异步获取后手动触发渲染。这里手动渲染的区别在于，当 `fetch` 里 `AJAX` 数据准备好了之后，需要调用如下方法进行模块渲染：

```
me.set('model', me.renderModel());
```

这里，`me.renderModel()` 并非必须的，只要是对应数据即可，查看代码会发现外语能力自评模块的 `me.renderModel()` 也是返回了模块所需基本数据。

## 小结

> 这两种方式单独使用都是 OK 的，那么，能否混合使用呢？栗子来一波

假设我们要添加一个测试模块 `eproject.evaluation2015.test`，首先我们在主模板 `/src/modules/eproject.evaluation2015/index.handlebars` 添加如下代码

```
<div 
    class="J_Test" 
    data-widget="~/eproject.evaluation2015.test/"
    data-model-path=""
>
    <div class="kuma-loading-l" style="margin:0 auto;"></div>
</div>
```

紧接着，我们在模块的 `fetch` 函数里去异步拉取数据，数据准备好了之后，假设我们根据数据手动更新了部分 `DOM` 结构，假设该部分 `DOM` 结构叫 `测试列表`。

这时候，就可能会出现一个诡异的问题是：`测试列表` 大多数时候渲染正常，偶尔渲染不出来。

为什么呢？

原因就在于 `data-model-path` 和 `fetch` 方法在执行时序上的冲突。

* 首先，子模块默认通过 `widget` 加载完成之后，会立即执行组件内的 `fetch` 方法去异步加载对应数据，数据获取成功后渲染对应 `DOM` 结构，也就是这里的 `测试列表`
* 接着，父模块的主模板加载完成后，其模块内部的 `fetch` 方法开始执行，这时候我们搜索关键字 `autoRender.bindSubModel` 会发现有两处，分别在两个异步回调里触发执行，这就是问题的根源了。这两个回调里会分别传递对应的 `model` 给子组件，子组件的 `renderModel` 方法会相应执行以重新渲染子组件。
* 那么当父组件的两个异步请求数据回来的更早的时候，子组件虽然被重复渲染了两次，依然可以正确渲染，因为最终的子组件数据拿到的最晚，渲染结果也就是正确的；那么当子组件的请求较早完成的时候，正确数据会被渲染一次，这时候，父组件的异步请求数据回来之后，子组件又会被渲染两次，而且是没有携带子组件里获取的数据来渲染，就会导致渲染异常

（完）

参考文档：

* http://gitlab.alibaba-inc.com/platform/eproject-2018






