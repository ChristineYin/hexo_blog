---
title: 数据结构和算法 - 查找
date: 2020-05-29 18:01:25
tags:
  - 数据结构
  - 算法
  - 查找
categories: 
  - [数据结构和算法]
excerpt: 查找也是比较常见的算法
---

## 查找第一个等于给定值
```bash
function biaryFindFirst(sortedArr, target) {
  if (sortedArr.length === 0) return -1;
  var low = 0;
  var high = sortedArr.length - 1;
  var mid = 0;
  while (low <= high) {
    mid = Math.floor((low + high) / 2);
    if (arr[mid] === target) {
      if (mid === 0 || arr[mid - 1] < target) return mid;
      high = mid - 1;
    } else if (arr[mid] > target) {
      high = mid - 1;
    } else {
      low = mid + 1;
    }
  }
  return -1;
}
```

## 查找最后一个等于给定值
```bash
function biaryFindLast(sortedArr, target) {
  if (sortedArr.length === 0) return -1;
  var low = 0;
  var high = sortedArr.length - 1;
  var mid = 0;
  while (low <= high) {
    mid = Math.floor((low + high) / 2);
    if (arr[mid] === target) {
      if (mid === sortedArr.length - 1 || arr[mid + 1] > target) return mid;
      low = mid + 1;
    } else if (arr[mid] > target) {
      high = mid - 1;
    } else {
      low = mid + 1;
    }
  }
  return -1;
}
```

## 查找第一个大于等于给定值的元素
```bash
function biaryFindFirstBig(sortedArr, target) {
  if (sortedArr.length === 0) return -1;
  var low = 0;
  var high = sortedArr.length - 1;
  var mid = 0;
  while (low <= high) {
    mid = Math.floor((low + higt) / 2)
    if (arr[mid] >= target) {
      if (mid === 0 || arr[mid - 1] < target) return mid;
      high = mid - 1;
    } else if (arr[mid] < target) {
      low = mid + 1;
    }
  }
  return -1;
}
```

## 查找最后一个小于等于给定值的元素
```bash
function biaryFindLastSmall(sortedArr, target) {
  if (sortedArr.length === 0) return -1;
  var low = 0;
  var high = sortedArr.length - 1;
  var mid = 0;
  while (low <= high) {
    mid = Math.floor((low + higt) / 2)
    if (sortedArr[mid] > target) {
      high = mid - 1
    } else {
      if (mid === sortedArr.length - 1 || sortedArr[mid + 1] >= target) return mid
      low = mid + 1
    }
  }
  return -1;
}
```