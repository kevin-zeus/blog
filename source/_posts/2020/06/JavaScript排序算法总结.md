---
title: JavaScript排序算法总结
date: 2020-06-22 20:22:34
tags:
---


# JavaScript排序算法总结

排序算法应该是我们在实际的开发场景中用的最多的一种算法，常见的排序算法有冒泡排序、简单选择排序、直接插入排序、归并排序、堆排序和快速排序。

## 前言

以下的排序代码中会出现`swap`函数，该函数实现如下：

```js
function swap(array, index1, index2) {
  [array[index1], array[index2]] = [array[index2], array[index1]];
}
```

## 排序算法说明

### 1、定义

对一序列对象根据某个关键字进行排序

### 2、部分概念

- **时间复杂度**：一个算法执行所耗费的时间。
- **空间复杂度**：运行完一个程序所需内存的大小。

### 3、算法总结

|排序算法|平均时间复杂度|最好情况|最坏情况|空间复杂度|
|:--|:--|:--|:--|:--|
|冒泡排序|O(n^2)|O(n)|O(n^2)|O(1)|

### 4、算法分类

- 交换排序：冒泡排序、快速排序；
- 插入排序：直接插入排序、希尔排序；
- 选择排序：简单选择排序、堆排序；

## 冒泡排序

![图片](https://blog-1257256187.cos.ap-chengdu.myqcloud.com/img/640.gif)

冒泡排序是最容易理解的一种排序算法，如上图。

### 简单的冒泡

```js
function bubbleSort(array) {
  let length = array.length;
  for (let i = 0; i < length; i++) {
    for (let j = 0; j < length-1; j++) {
      if (array[j] > array[j+1]) {
        swap(array, j, j+1);
      }
    }
  }
}
```

### 改进后的冒泡排序

上面简单的冒泡排序中，我们会发现当算法执行到外循环第二轮时，最后面的两个值还是进行了一次比较，即使这是不必要的。所以，我们可以稍微改进一下：

```js
function bubbleSort(array) {
  let length = array.length;
  for (let i = 0; i < length; i++) {
    for (let j = 0; j < length-1-i; j++) { // 变动了j的范围
      if (array[j] > array[j+1]) {
        swap(array, j, j+1);
      }
    }
  }
}
```

## 选择排序（简单选择排序）

![简单选择排序](https://blog-1257256187.cos.ap-chengdu.myqcloud.com/img/20200514105627)

从图中可以看出，选择排序首先是找到序列中最小值并将其放置在第一位，接着找到第二小的值放到第二位，以此类推。

```js
function selectionSort(arr) {
  let length = arr.length, indexMin;
  for (let i=0; i<length-1; i++) {
    indexMin = i;
    for (let j=i; j<length; j++) {
      if (arr[indexMin] > array[j]) {
        indexMin = j;
      }
    }
    if (i !== indexMin) {
      swap(arr, i, indexMin);
    }
  }
}
```

## 插入排序（直接插入排序）

![插入排序](https://blog-1257256187.cos.ap-chengdu.myqcloud.com/img/20200514105642)

可以看上面的动图，插入排序就是从第一位开始，拎出来，第一位前面没有位置了，OK放下不管。第二轮，将第二位拎出来，对比第二位与其前一位，如果比前一位小，互换位置，再继续向前比较直到前面没有值。第三轮，以此类推。

```js
function insertionSort(arr) {
  let length = arr.length, j ,temp;
  for (let i=1; i<length; i++) {
    j = i;
    temp = arr[i];
    while (j>0 && arr[j-1] > temp) {
      arr[j] = arr[j-1];
      j--;
    }
    arr[j] = temp;
  }
}
```

## 堆排序

## 归并排序

![归并排序](https://blog-1257256187.cos.ap-chengdu.myqcloud.com/img/20200514105701)

和选择排序一样，归并排序的性能不受输入数据的影响，但表现比选择排序好的多，因为始终都是O(nlogn)的时间复杂度。代价是需要额外的内存空间。

归并排序是建立在归并操作上的一种有效的排序算法。该算法是采用分治法（Divide and Conquer）的一个非常典型的应用。归并排序是一种稳定的排序方法。将已有序的子序列合并，得到完全有序的序列；即先使每个子序列有序，再使子序列段间有序。若将两个有序表合并成一个有序表，称为2-路归并。

算法描述：
- 把长度为n的输入序列分成两个长度为n/2的子序列；
- 对这两个子序列分别采用归并排序；
- 将两个排序好的子序列合并成一个最终的排序序列。

## 快速排序

![快速排序](https://blog-1257256187.cos.ap-chengdu.myqcloud.com/img/20200514105716)

快速排序是最常用的排序算法了，它使用了分治的方法，将原始数组分为较小的数组。具体的算法描述如下：

- 从数列中挑选一个元素，作为“基准”（pivot）；
- 重新排序数列，所有元素比基准值小的摆放在基准左边，所有元素比基准值大的摆在基准右边（相同的数可以任一边）。在这个分区退出之后，该基准就处于数列的中间位置。这部分操作过程称为分区（partition）操作；
- 递归地把小于基准值元素的子数列和大于基准值元素的子数列排序。

```js
function quickSort(array) {
  quick(array, 0, array.length-1);

  function quick(array, left, right) {
    if (left < right) {
      let indexMin = partition(array, left, right);
      quick(array, left, indexMin-1);
      quick(array, indexMin+1, right);
    }
  }

  function partition(array, left, right) {
    let pivot = array[left],
      indexMin = left;
    for (let i = left+1; i<=right; i++) {
      if (array[i] < pivot) {
        indexMin++;
        swap(array, i, indexMin);
      }
    }
    swap(array, left, indexMin);
    return indexMin;
  }
}
```

