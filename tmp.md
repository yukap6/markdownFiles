# JavaScript 正则

## 基础知识

JS 中用于正则操作的方法，共有6个，字符串实例4个，正则实例2个：

[String#search](http://devdocs.io/javascript/global_objects/string/search)
> 参数默认要求是一个正则表达式，如果不是正则表达式，则会强制使用 `new RegExp` 将其转换为正则表达式

[String#replace](http://devdocs.io/javascript/global_objects/string/replace)
> 语法：`str.replace(regexp|substr, newSubstr|function)` 
> 第二个参数可以是函数，其中 function 的参数是：function (match, p1, pn.., offset, wholeString)
> 
> `newSubStr` 里 \$\$ 表示 \$, \$& 表示匹配到的子字符串, \$\` 表示匹配字符串往前的部分, \$' 表示匹配字符串往后的部分，\$n 表示第 n 个子匹配

[String#split](http://devdocs.io/javascript/global_objects/string/split)
> 语法：`str.split([separator[, limit]])`
> 第二个可选参数可以限制最多分割的数目
> 
> 如果分隔符是空字符串 "", 则目标字符串会按照单个字符进行分割
> 
> 如果分隔符未找到或者不提供，则返回包含原始字符串的数组
> 
> 如果目标字符串是空字符串，则split() 返回包含一个空字符串的数组，如果目标字符串和分隔符都是空字符串，则返回一个空数组
>
> 如果分隔符是一个包含子捕获组的正则表达式，则子捕获组会被叠加到目标数组里，如下示例 [并非所有浏览器支持]

```
var myString = 'Hello 1 word. Sentence number 2.';
var splits = myString.split(/(\d)/);

console.log(splits);
// [ "Hello ", "1", " word. Sentence number ", "2", "." ]
```


[String#match](http://devdocs.io/javascript/global_objects/string/match)

> 语法：`str.match(regexp)`
> 同 search 方法，参数默认要求是一个正则表达式，如果不是正则表达式，则会强制使用 `new RegExp` 将其转换为正则表达式
> 
> 如果参数被忽略，则会返回包含一个空字符串元素的数组 `['']`
> 
> 如果正则表达式不包含 `g` 修饰符，则执行结果同 `RegExp.exec()`；如果包含 `g` 修饰符，则返回的数组包含所有匹配的子字符串（而不再是匹配对象），详见练习3
> 

[RegExp#test](http://devdocs.io/javascript/global_objects/regexp/test)

> 语法：`regexObj.test(str)`
> 如果正则表达式有 `g` 修饰符，则 `lastIndex` 属性会随着 `test()` 方法的使用而增加(同 `exec`)，如下

```
var regex = /foo/g;

// regex.lastIndex is at 0
regex.test('foo'); // true

// regex.lastIndex is now at 3
regex.test('foo'); // false
```

[RegExp#exec](http://devdocs.io/javascript/global_objects/regexp/exec)

> 语法：`regexObj.exec(str)`

## 实战训练

**练习1**：var s1 = "get-element-by-id"; 给定这样一个连字符串，写一个function转换为驼峰命名法形式的字符串 getElementById

解法(1)：

```
function toHump(str) {
  var reg = /-([a-z])/g;
  return str.replace(reg, function(match, p1, offset, string){
    return p1.toUpperCase();
  });
}
```

解法(2：)

```
function toHump(str) {
  var reg = /-\w/g;
  return str.replace(reg, function(match){
    return match.slice(1).toUpperCase();
  });
}
```

**练习2**：检测字符串中是否有重复的字母

```
function isContainRepeatLetter(str) {
    return /([a-zA-Z])\1/.test(str);
}
```

**练习3**：获取URL中指定参数的值

解法(1)：不考虑重复参数的问题，只取第一个值，比如 `&name=job&age=21&name=lucy`，只取 `name=job` 中的 `job`

```
function getUrlParamValueByKey(url, key) {
    var reg = new RegExp(`${key}=([^&]*)`, 'i');
    var r = url.match(reg);
    return r && r[1] || '';
}
```

解法(2)：考虑重复参数的问题，取最后一个值为有效值，比如 `&name=job&age=21&name=lucy`，取 `name=lucy` 中的 `lucy`

```
function getUrlParamValueByKey(url, key) {
  var reg = new RegExp(`${key}=([^&]*)`, 'ig');
  var matched = url.match(reg);
  if (matched) {
    return matched[matched.length - 1].replace(`${key}=`, '');
  }
  return '';
}
```

**练习4**：将字符串倒序输出

```
function strReverse(str) {
    return str.split('').reverse().join('');
}
```

**练习5**：提取链接地址

```
var s3 = "IT面试题博客中包含很多  <a href='http://hi.baidu.com/mianshiti/blog/category/微软面试题'> 微软面试题 </a> ";
function getUrlFromString(str) {
  var reg = /href='([^']*)'/g;
  var arr = str.match(reg) || [];
  return str.match(reg).map((item) => {
    return item.replace('href=\'', '').replace(/'$/, '');
  });
}

```

**练习6**：对数字使用逗号','进行千位分割，也就是三位数字用一个逗号','隔开，比如 12345678, 结果为 12,345,678

解法(1)：

```
function numberSeperateWithComma(num) {
  var reg = /\B(?=(\d{3})+(?!\d))/g;
  return  String(num).replace(reg, ',');
}
```

> 解释：正则表达式 `/\B(?=(\d{3})+(?!\d))/g` 分解开来就是
> 首先，将数字进行三位一分组，定位到三位一分组之间的空白位置，则是 `/(\d){3}+/g`
> 其次，进行三位一分组的时候，后边界不能剩余任何数字，则是 `/(\d){3}+(?!\d)/g`
> 最后，三位一分组之间的空白位置不能出现在最前边，避免 `,123,456` 这种情况，则要限制该空位必须以非单词边界开始，也就是加入 `\B`，则是 `/\B(?=(\d{3})+(?!\d))/g`

解法(2)：逆向思维

```
function numberSeperateWithComma(num) {
  var reg = /(\d{3})(?=\d)/g;
  var str = String(num).split('').reverse().join('');
  var tmpResult = str.replace(reg, '$&,');
  return tmpResult.split('').reverse().join('');
}
```

> 解释：将字符串逆向排序之后，从前往后，每隔3个字符插入一个逗号，然后再逆向排序回目标状态（就相当于从后往前每隔三个数字插入一个逗号）
> 首先，将目标数字进行逆向排序
> 其次，将目标数字进行三个一组匹配并插入逗号','分隔符，正则为 `/(\d{3})/g`，替换方式为 `str.replace(reg, '$&,')`，这里的 $& 表示当前匹配子串
> 接着，处理边界，最后插入的逗号不能在单词边界，即必须紧邻任何数字，所以正则调整为 `/(\d{3})(?=\d)/g`
> 最后，将处理好的结果进行逆向排序，产生目标字符串


