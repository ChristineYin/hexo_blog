---
title: 数据结构和算法 - 数组
date: 2020-05-29 17:57:09
tags:
  - 数据结构
  - 算法
  - 数组
categories: 
  - [数据结构和算法]
# top_image: https://rmt.dogedoge.com/fetch/fluid/storage/bg/dojm2h.png?w=1920&q=100&fmt=webp
excerpt:  数组是一种线性表数据结构，它用一组连续的内存空间，来存储一组具有相同类型的数据。最大的特点就是支持按下标随机访问，但插入、删除操作也因此变得比较低效，平均情况时间复杂度为O(n)。
---

数组是一种线性表数据结构，它用一组连续的内存空间，来存储一组具有相同类型的数据。最大的特点就是支持按下标随机访问，但插入、删除操作也因此变得比较低效，平均情况时间复杂度为O(n)。
- 线性表就是数据排成像一条线一样的结构，每个线性表上的数据最多只有前和后两个方向。除了数组，链表、队列、栈等也都是线性表结构。与之相对立的非线性表，比如二叉树、堆、图等。之所以叫非线性，是因为在非线性表中，数据之间并不是简单的前后关系。
- 连续的内存空间和相同类型的数据。正因为这两个限制，才可以做到"随机访问"。但如果想在数组中删除和插入一个数据，为了保证连续性，就需要做大量的数据搬移工作。

## 合并有序数组

给你两个有序整数数组 nums1 和 nums2，请你将 nums2 合并到 nums1 中，使 nums1 成为一个有序数组。

```bash
var merge = function (nums1, m, nums2, n) {
  var i = m - 1, j = n - 1, index = m + n - 1;
  while (i >= 0 && j >= 0) {
    nums1[index--] = nums2[j] >= nums1[i] ? nums2[j--] : nums1[i--];
  }
  while (j >= 0) {
    nums1[index--] = nums2[j--]
  }
};

var nums1 = [3, 4, 7, 0, 0, 0];
var nums2 = [2, 5, 6];
merge(nums1, 3, nums2, 3)
```
https://leetcode-cn.com/problems/merge-sorted-array/


## 求目标值

给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。

```bash
var twoSum = function(nums, target) {
  for(var i = 0, len = nums.length; i < len; i++) {
    var idx = nums.indexOf(target - nums[i]);
    if (idx !== i && idx !== -1) {
      return [i, idx]
    }
  }
};
```

## 数组扁平化
```bash
var arr = [ [1, 2, 2], [3, 4, 5, 5], [6, 7, 8, 9, [11, 12, [12, 13, [14] ] ] ], 10]

let flatArr = arr.flat(4)
```

## 数组去重
```bash
let disArr = Array.from(new Set(flatArr))
```

## 数组排序
```bash
let result = disArr.sort(function(a, b) {
    return a-b
})
```

## 数组交集

计算多个数组的交集

```bash
const getIntersection = (...arrs) => {
  return Array.from(new Set(arrs.reduce((total, arr) => {
      return arr.filter(item => total.includes(item));
  })));
}
```

## 最大连续子序列

给定一个整数，找出总和最大的连续数列，并返回总和

```bash
var maxSubArray = function (nums) {
  var max = sum = nums[0];

  for (var i = 1; i < nums.length; i++) {
    if (sum < 0) {
      sum = nums[i]
    } else {
      sum += nums[i]
    }
    max = Math.max(max, sum)
  }
  return max;
}
```

## 缺失的数字

0 ~ n-1缺失的数字

```bash
var missingNumber = function (nums) {
  var low = 0, high = nums.length - 1, mid = 0;
  while (low <= high) {
    mid = Math.floor((low + high) / 2);
    if (nums[mid] === mid) {
      low = mid + 1;
    } else {
      high = mid - 1;
    }
  }
  return low;
}
```
// https://leetcode-cn.com/problems/que-shi-de-shu-zi-lcof/
