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

## 包含 min 函数的最小栈

设计一个支持 push、pop、top 操作，并能在常数时间内检索到最小元素的栈

定义栈的数据结构，请在该类型中实现一个能够得到栈的最小元素的 min 函数在该栈中，调用 min、push 及 pop 的时间复杂度都是 O(1)。

**示例 1：**

```bash
MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.min();   --> 返回 -3.
minStack.pop();
minStack.top();      --> 返回 0.
minStack.min();   --> 返回 -2.
```

**题解：**

```bash
/**
 * initialize your data structure here.
 */
var MinStack = function() {
    this.items = []
    this.min = null
};

// 进栈
/**
 * @param {number} x
 * @return {void}
 */
MinStack.prototype.push = function(x) {
    if(!this.items.length) this.min = x
    this.min = Math.min(x, this.min)
    this.items.push(x)
};

// 出栈
/**
 * @return {void}
 */
MinStack.prototype.pop = function() {
    let num = this.items.pop()
    this.min = Math.min(...this.items)
    return num
};

// 获取栈顶元素
/**
 * @return {number}
 */
MinStack.prototype.top = function() {
    if(!this.items.length) return null
    return this.items[this.items.length -1]
};

// 检索栈中的最小元素
/**
 * @return {number}
 */
MinStack.prototype.getMin = function() {
    return this.min
};
```

https://leetcode-cn.com/problems/min-stack/

## 滑动窗口的最大值

给定一个数组 nums 和滑动窗口的大小 k，请找出所有滑动窗口里的最大值。

**示例:**

```bash
输入: nums = [1,3,-1,-3,5,3,6,7], 和 k = 3
输出: [3,3,5,5,6,7]
解释:

  滑动窗口的位置                最大值
---------------               -----
[1  3  -1] -3  5  3  6  7       3
 1 [3  -1  -3] 5  3  6  7       3
 1  3 [-1  -3  5] 3  6  7       5
 1  3  -1 [-3  5  3] 6  7       5
 1  3  -1  -3 [5  3  6] 7       6
 1  3  -1  -3  5 [3  6  7]      7
```

**提示：**
你可以假设 k 总是有效的，在输入数组不为空的情况下，1 ≤ k ≤ 输入数组的大小。

**题解：**

```bash
/**
 * @param {number[]} nums
 * @param {number} k
 * @return {number[]}
 */
var maxSlidingWindow = function (nums, k) {
    if (!nums.length) return [];
    var max = [],
        arr = [];
    for (var i = 0, len = nums.length - k + 1; i < len; i++) {
        arr = nums.slice(i, i + k);
        max.push(Math.max(...arr))
    }
    return max;
}
```

https://leetcode-cn.com/problems/hua-dong-chuang-kou-de-zui-da-zhi-lcof

## 队列的最大值

请定义一个队列并实现函数 max_value 得到队列里的最大值，要求函数 max_value、push_back 和 pop_front 的均摊时间复杂度都是 O(1)。

若队列为空，pop_front 和 max_value  需要返回 -1

**示例 1：**

```bash
输入:
["MaxQueue","push_back","push_back","max_value","pop_front","max_value"]
[[],[1],[2],[],[],[]]
输出: [null,null,null,2,1,2]
```

**示例 2：**

```bash
输入:
["MaxQueue","pop_front","max_value"]
[[],[],[]]
输出: [null,-1,-1]
```

**限制：**

```bash
1 <= push_back,pop_front,max_value的总操作数 <= 10000
1 <= value <= 10^5
```

**题解：**

```bash
var MaxQueue = function() {
    this.items = [];
    this.max = -1;
};

/**
 * @return {number}
 */
MaxQueue.prototype.max_value = function() {
    return this.max;
};

/**
 * @param {number} value
 * @return {void}
 */
MaxQueue.prototype.push_back = function(value) {
    if (!this.max) this.max = value;
    this.max = Math.max(this.max, value);
    this.items.push(value);
};

/**
 * @return {number}
 */
MaxQueue.prototype.pop_front = function() {
    if (!this.items.length) return -1;

    var first = this.items.shift();
    this.max = this.items.length === 0 ? -1 : Math.max(...this.items);
    return first;
};

/**
 * Your MaxQueue object will be instantiated and called as such:
 * var obj = new MaxQueue()
 * var param_1 = obj.max_value()
 * obj.push_back(value)
 * var param_3 = obj.pop_front()
 */
```

https://leetcode-cn.com/problems/dui-lie-de-zui-da-zhi-lcof

## 有效的括号

给定一个只包括 '('，')'，'{'，'}'，'['，']'  的字符串，判断字符串是否有效。

有效字符串需满足：

1. 左括号必须用相同类型的右括号闭合。
2. 左括号必须以正确的顺序闭合。

注意空字符串可被认为是有效字符串。

**示例：**

```bash
输入: "()"
输出: true

输入: "()[]{}"
输出: true

输入: "(]"
输出: false

输入: "([)]"
输出: false
```

**题解：**

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

用两个栈实现一个队列。队列的声明如下，请实现它的两个函数 appendTail 和 deleteHead ，分别完成在队列尾部插入整数和在队列头部删除整数的功能。(若队列中没有元素，deleteHead  操作返回 -1 )

**示例 1：**

```bash
输入：
["CQueue","appendTail","deleteHead","deleteHead"]
[[],[3],[],[]]
输出：[null,null,3,-1]
```

**示例 2：**

```bash
输入：
["CQueue","deleteHead","appendTail","appendTail","deleteHead","deleteHead"]
[[],[],[5],[2],[],[]]
输出：[null,-1,null,null,5,2]
```

**提示：**

- 1 <= values <= 10000
- 最多会对 appendTail、deleteHead 进行 10000 次调用

**题解：**

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
