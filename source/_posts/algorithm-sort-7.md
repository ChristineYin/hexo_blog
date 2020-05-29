---
title: 数据结构和算法 - 排序
date: 2020-05-29 18:06:04
tags:
  - 数据结构
  - 算法
  - 排序
categories: 
  - [数据结构和算法]
excerpt: 常见排序：冒泡排序、插入排序、选择排序、归并排序、快速排序、计数排序、基数排序、桶排序。
---

## 冒泡排序
```bash
function bubbleSort(arr) {
  var temp, isSorted, len = arr.length;
  for (var i = 0; i < len; i++) {
    isSorted = true;
    for (var j = 0; j < len - i - 1; j++) {
      if (arr[j] > arr[j + 1]) {
        temp = arr[j];
        arr[j] = arr[j + 1];
        arr[j + 1] = temp;
        if (isSorted) isSorted = false;
      }
    }
    if (isSorted) break;
  }
}
// isSorted用来判断本次排序是否有数据交换，没有即可认为已是有序数组
```

## 插入排序
```bash
function insertSort(arr) {
  var insertValue, j;
  for (var i = 1, len = arr.length; i < len; i++) {
    insertValue = arr[i];
    j = i - 1;
    for (; j >= 0 && arr[j] > insertValue; j--) {
      arr[j + 1] = arr[j]
    }
    arr[j + 1] = insertValue;
  }
}
// insertValue记录本轮比较的值，避免两两交换，优化
```

## 选择排序
```bash
function selectSort(arr) {
  var minIndex, temp;
  for (var i = 0, len = arr.length; i < len; i++) {
    minIndex = i;
    for (var j = i + 1; j < len; j++) {
      minIndex = arr[j] < arr[minIndex] ? j : minIndex
    }
    temp = arr[minIndex];
    arr[minIndex] = arr[i];
    arr[i] = temp;
  }
}
```

在以上三种排序中，时间复杂度都为O(n^2)，适合小规模数据排序。冒泡排序和选择排序只能停留在理论层面，学习的目的也只是为了开拓思维，在实际开发中应用并不多，但插入排序还是挺有用的。

归并排序和快速排序一样，都使用的就是分治思想。分治，就是分而治之，将一个大问题分解成小的子问题解决。小的子问题解决了，大问题也就解决了。分治算法一般用递归实现。分治是一种解决问题的处理思想，递归是一种编程技巧。


## 归并排序
```bash
function mergeSort(arr, start, end) {
  if (end > start) {
    var mid = parseInt((start + end) / 2)
    mergeSort(arr, start, mid)
    mergeSort(arr, mid + 1, end)
    merge(arr, start, mid, end)
  }
}
function merge(arr, start, mid, end) {
  var temp = [], i = start; j = mid + 1, index = 0;
  while (i <= mid && j <= end) {
    index = arr[i] < arr[j] ? i++ : j++;
    temp.push(arr[index])
  }
  if (j <= end) {
    temp = temp.concat(arr.slice(j, end + 1))
  }
  if (i <= mid) {
    temp = temp.concat(arr.slice(i, mid + 1))
  }
  arr.splice(start, end - start + 1, ...temp)
}

mergeSort(arr, 0, 7)
```
https://mp.weixin.qq.com/s/885uGVhlffWAxjgIEW-TiA


## 快速排序
```bash

```