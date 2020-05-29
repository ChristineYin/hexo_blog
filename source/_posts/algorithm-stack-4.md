---
title: 数据结构和算法 - 栈
date: 2020-05-29 18:07:12
tags:
  - 数据结构
  - 算法
  - 栈
categories: 
  - [数据结构和算法]
excerpt: 栈是一种操作受限的数据结构，只支持入栈和出栈操作。后进先出是它最大的特点，栈即可以通过数组实现，也可以通过链表。不管基于数组还是链表，入栈、出栈的时间复杂度都为O(1)。
---

## 最小栈

设计一个支持push、pop、top操作，并能在常数时间内检索到最小元素的栈

```bash
var MinStack = function() {
    this.items = []
    this.min = null
};

// 进栈
MinStack.prototype.push = function(x) {
    if(!this.items.length) this.min = x 
    this.min = Math.min(x, this.min)
    this.items.push(x) 
};

// 出栈
MinStack.prototype.pop = function() {
    let num = this.items.pop() 
    this.min = Math.min(...this.items)
    return num
};

// 获取栈顶元素
MinStack.prototype.top = function() {
    if(!this.items.length) return null
    return this.items[this.items.length -1] 
};

// 检索栈中的最小元素
MinStack.prototype.getMin = function() {
    return this.min
};
```
https://leetcode-cn.com/problems/min-stack/

## 有效的括号

```bash
var isValid = function (s) {
  var map = {
    '(': ')',
    '{': '}',
    '[': ']'
  }
  var arr = [];
  for (var i = 0, len = s.length; i < len; i++) {
    if (Object.keys(map).includes(s[i])) {
      arr.push(s[i])
    } else {
      if (map[arr.pop()] !== s[i]) return false
    }
  }
  return arr.length === 0;
}
```
https://leetcode-cn.com/problems/valid-parentheses/


## 用两个栈实现队列
```bash
var CQueue = function () {
  this.inStack = [];
  this.outStack = [];
};

/** 
 * @param {number} value
 * @return {void}
 */
CQueue.prototype.appendTail = function (value) {
  this.inStack.push(value)
};

/**
 * @return {number}
 */
CQueue.prototype.deleteHead = function () {
  if (this.outStack.length) {
    return this.outStack.pop()
  } else {
    while (this.inStack.length) {
      this.outStack.push(this.inStack.pop())
    }
    return this.outStack.pop() || -1;
  }
};
```
https://leetcode-cn.com/problems/yong-liang-ge-zhan-shi-xian-dui-lie-lcof/