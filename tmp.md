# 第2章 排序

### 选择排序

> 定义：首先，找到数组中最小的那个元素，其次，将它和数组中的第一个元素交还位置（如果第一个元素就是最小元素就和自己交换）。再次，在剩下的元素当中在找到最小元素将它和第二个元素交换位置。如此往复，直到整个数组排序。这种方法叫做选择排序，因为它在不停的选择剩余元素之中的最小者。

```
// 选择排序
function selectSort(arr) {
  for (let i = 0, len = arr.length; i < len; i++) {
    let min = i;
    for (let j = i + 1; j < len; j++) {
      if (Number(arr[j]) - Number(arr[min]) <= 0) {
        min = j;
      }
    }
    const t = arr[i];
    arr[i] = arr[min];
    arr[min] = t;
  }
  return arr;
}
```

这里要注意的是，对于数组元素的大小比较 `if (Number(arr[j]) - Number(arr[min]) <= 0)` 只适合纯数值排序，具体情况应该按照具体情况来给出比较算法

等差数列求和公式：`Sn=n(a1+an)/2`。（[公式推导](https://www.zybang.com/question/cadd20bef8b5105ca12195883b5a1b9d.html)）

### 插入排序

移动端图片定宽定高等分水平空间布局

* 方案1 calc 计算单个元素 margin, 然后 nth-child(4n) 拿掉每行最后一个元素的 margin, 4 就是每行的元素个数
* 方案2 父元素负的 margin, 每个子元素给相同 margin
* 方案3 flex，适合单行



