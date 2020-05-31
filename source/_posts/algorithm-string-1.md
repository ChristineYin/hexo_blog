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

## 左旋转字符串

字符串的左旋转操作是把字符串前面的若干个字符转移到字符串的尾部。请定义一个函数实现字符串左旋转操作的功能。比如，输入字符串"abcdefg"和数字 2，该函数将返回左旋转两位得到的结果"cdefgab"。

**示例 1：**

```bash
输入: s = "abcdefg", k = 2
输出: "cdefgab"
```

**示例 2：**

```bash
输入: s = "lrloseumgh", k = 6
输出: "umghlrlose"
```

**限制：**

- 1 <= k < s.length <= 10000

**题解：**

```bash
/**
 * @param {string} s
 * @param {number} n
 * @return {string}
 */

var reverseLeftWords = function (s, n) {
  var s1 = s.slice(0, n);
  var s2 = s.slice(n, s.length).concat(s1)
  return s2;
}
```

https://leetcode-cn.com/problems/zuo-xuan-zhuan-zi-fu-chuan-lcof

## 翻转单词顺序

输入一个英文句子，翻转句子中单词的顺序，但单词内字符的顺序不变。为简单起见，标点符号和普通字母一样处理。例如输入字符串"I am a student. "，则输出"student. a am I"。

**示例 1：**

```bash
输入: "the sky is blue"
输出: "blue is sky the"
```

**示例 2：**

```bash
输入: "  hello world!  "
输出: "world! hello"
解释: 输入字符串可以在前面或者后面包含多余的空格，但是反转后的字符不能包括。
```

**示例 3：**

```bash
输入: "a good   example"
输出: "example good a"
解释: 如果两个单词间有多余的空格，将反转后单词间的空格减少到只含一个。
```

**说明：**

- 无空格字符构成一个单词。
- 输入字符串可以在前面或者后面包含多余的空格，但是反转后的字符不能包括。
- 如果两个单词间有多余的空格，将反转后单词间的空格减少到只含一个。

**题解：**

```bash
var reverseWords = function(s) {
  return s.trim().replace(/\s+/g, ' ').split(' ').reverse().join(' ')
};
```

https://leetcode-cn.com/problems/reverse-words-in-a-string/

## 最长公共前缀

编写一个函数来查找字符串数组中的最长公共前缀。
如果不存在公共前缀，返回空字符串 ""。

**示例 1:**

```bash
输入: ["flower","flow","flight"]
输出: "fl"
```

**示例 2:**

```bash
输入: ["dog","racecar","car"]
输出: ""
解释: 输入不存在公共前缀。
```

**说明：**
所有输入只包含小写字母 a-z 。

**题解：**

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

## 回文字符串

给定一个字符串，验证它是否是回文串，只考虑字母和数字字符，可以忽略字母的大小写。
说明：本题中，我们将空字符串定义为有效的回文串。

**示例 1：**

```bash
输入: "A man, a plan, a canal: Panama"
输出: true
```

**示例 2：**

```bash
输入: "race a car"
输出: false
```

**题解：**

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

https://leetcode-cn.com/problems/valid-palindrome/

## 无重复字符的最长子串

给定一个字符串，请你找出其中不含有重复字符的 最长子串 的长度。

**示例 1：**

```bash
输入: "abcabcbb"
输出: 3
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
```

**示例 2：**

```bash
输入: "bbbbb"
输出: 1
解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。
```

**示例 3：**

```bash
输入: "pwwkew"
输出: 3
解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
     请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。
```

**题解：**

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
