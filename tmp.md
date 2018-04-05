# 第1章 基础

### 1. 二分查找

```
function binaryChop(search, targetArray) {
  let low = 0;
  let hig = targetArray.length - 1;
  targetArray.sort((a, b) => a - b);
  while(low <= hig) {
    const mid = Math.floor(low + ((hig - low) / 2));
    if (search < targetArray[mid]) {
      hig = mid - 1;
    } else if (search > targetArray[mid]) {
      low = mid + 1;
    } else {
      return mid;
    }
  }
  return -1;
}
```

实现二分查找算法的时候，有2个点需要注意下

* 求取中间值 `mid` 的时候，必须向下取整，否则可能会出现小数点下表，是不符合预期的
* 数组排序 `sort` 的时候，必须使用参数 `(a, b) => a - b`，因为默认的排序是按照字符串的 Unicode 码点来排序的，那么就会出现数字 `101` 排在 `2` 前面的情况出现。


