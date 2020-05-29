---
title: 数据结构和算法 - 字符串
date: 2020-05-29 18:08:18
tags:
  - 数据结构
  - 算法
  - 字符串
categories: 
  - [数据结构和算法]
excerpt: 字符串
---

## 翻转字符串里的单词

```bash
var reverseWords = function(s) {
  return s.trim().replace(/\s+/g, ' ').split(' ').reverse().join(' ')
};
```
https://leetcode-cn.com/problems/reverse-words-in-a-string/


## 最长公共前缀
```bash
var longestCommonPrefix = function (strs) {
  if (strs.length === 0) return '';

  var str1 = strs[0];
  var common = ''
  for (var i = 0, len = str1.length; i < len; i++) {
    for (var j = 1; j < strs.length; j++) {
      if (strs[j][i] !== str1[i]) {
        return common;
      }
    }
    common = common.concat(str1[i])
  }

  return common;
};
```
https://leetcode-cn.com/problems/longest-common-prefix/


## 判断输入是不是回文字符串
```bash
function isPlalindrome(input) {
  if (typeof input !== 'string') return false;
  return input.split('').reverse().join('') === input;
}

function isPlalindrome(input) {
  if (typeof input !== 'string') return false;
  let i = 0, j = input.length - 1;
  while (i < j) {
    if (input[i] !== input[j]) return false;
    i++;
    j--;
  }
  return true;
}
```

## 无重复字符的最长子串
```bash
var lengthOfLongestSubstring = function (s) {
  var max = 0;
  var arr = [];
  var idx = 0;
  for (var i = 0, len = s.length; i < len; i++) {
    idx = arr.indexOf(s[i]);
    if (idx !== -1) {
      arr.splice(0, idx + 1)
    }
    arr.push(s[i])
    max = max > arr.length ? max : arr.length;
  }
  return max;
}
```
https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/







