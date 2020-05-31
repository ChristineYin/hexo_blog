---
title: 数据结构和算法 - 链表
date: 2020-05-29 18:03:00
tags:
  - 数据结构
  - 算法
  - 链表
categories:
  - [数据结构和算法]
excerpt: 链表不需要一块连续的内存空间，它通过"指针"将一组零散的内存块串联起来使用，其中，我们把内存块称为链表的"结点"。为了将所有结点串起来，每个链表的结点除了存储数据之外，还需要记录链上的下一个结点的地址，我们把这个记录下一个结点地址的指针叫作后继指针next。我们习惯性的将第一个结点叫作头结点，把最后一个结点叫做尾节点。尾结点指向一个空地址NULL，表示这是链表上的最后一个结点。常见的链表结构分别是单链表、双向链表、循环链表。
---

链表不需要一块连续的内存空间，它通过"指针"将一组零散的内存块串联起来使用，其中，我们把内存块称为链表的"结点"。为了将所有结点串起来，每个链表的结点除了存储数据之外，还需要记录链上的下一个结点的地址，我们把这个记录下一个结点地址的指针叫作后继指针 next。我们习惯性的将第一个结点叫作头结点，把最后一个结点叫做尾节点。尾结点指向一个空地址 NULL，表示这是链表上的最后一个结点。常见的链表结构分别是单链表、双向链表、循环链表。

- 循环链表是一种特殊的单链表，与单链表唯一的区别就是尾结点指针是指向链表的头结点。
- 双向链表它支持两个方向，每个结点不止有一个后继指针 next 指向后面的节点，还有一个前驱指针 prev 指向前面的结点。

## 合并两个有序链表

将两个升序链表合并为一个新的升序链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。

**示例：**

```bash
输入：1->2->4, 1->3->4
输出：1->1->2->3->4->4
```

**题解：**

```bash
/**
 * Definition for singly-linked list.
 * function ListNode(val) {
 *     this.val = val;
 *     this.next = null;
 * }
 */
/**
 * @param {ListNode} l1
 * @param {ListNode} l2
 * @return {ListNode}
 *
var mergeTwoLists = function (l1, l2) {
  if (l1 === null) {
    return l2
  }
  if (l2 === null) {
    return l1
  }
  if (l1.val <= l2.val) {
    l1.next = mergeTwoLists(l1.next, l2)
    return l1
  } else {
    l2.next = mergeTwoLists(l2.next, l1)
    return l2
  }
};
```

https://leetcode-cn.com/problems/merge-two-sorted-lists/

## 判断一个单链表是否有环

给定一个链表，判断链表中是否有环。

为了表示给定链表中的环，我们使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 pos 是 -1，则在该链表中没有环。

**题解：**

```bash
/**
 * Definition for singly-linked list.
 * function ListNode(val) {
 *     this.val = val;
 *     this.next = null;
 * }
 */

/**
 * @param {ListNode} head
 * @return {boolean}
 */
var hasCycle = function (head) {
  while (head) {
    if (head.flag) return true;
    head.flag = true;
    head = head.next;
  }
  return false;
}
```

https://leetcode-cn.com/problems/linked-list-cycle/

## 链表翻转

反转一个单链表。

**示例：**

```bash
输入: 1->2->3->4->5->NULL
输出: 5->4->3->2->1->NULL
```

**题解：**

```bash
/**
 * Definition for singly-linked list.
 * function ListNode(val) {
 *     this.val = val;
 *     this.next = null;
 * }
 */
/**
 * @param {ListNode} head
 * @return {ListNode}
 */
function ListNode(val) {
  this.val = val;
  this.next = null;
}
var reverseList = function (head) {
  if (!head || !head.next) return head;
  var prev = null;
  var curr = head;
  var next;
  while (curr) {
    next = curr.next;
    curr.next = prev;
    prev = curr;
    curr = next;
  }
  return prev
}
```

https://leetcode-cn.com/problems/reverse-linked-list/

## 求链表的中间结点

给定一个带有头结点 head 的非空单链表，返回链表的中间结点。
如果有两个中间结点，则返回第二个中间结点。

**示例 1：**

```bash
输入：[1,2,3,4,5]
输出：此列表中的结点 3 (序列化形式：[3,4,5])
返回的结点值为 3 。 (测评系统对该结点序列化表述是 [3,4,5])。
注意，我们返回了一个 ListNode 类型的对象 ans，这样：
ans.val = 3, ans.next.val = 4, ans.next.next.val = 5, 以及 ans.next.next.next = NULL.
```

**示例 2：**

```bash
输入：[1,2,3,4,5,6]
输出：此列表中的结点 4 (序列化形式：[4,5,6])
由于该列表有两个中间结点，值分别为 3 和 4，我们返回第二个结点。
```

**题解：**

```bash
/**
 * Definition for singly-linked list.
 * function ListNode(val) {
 *     this.val = val;
 *     this.next = null;
 * }
 */
/**
 * @param {ListNode} head
 * @return {ListNode}
 */

var middleNode = function (head) {
  var fast = head, slow = head;
  while (fast && fast.next) {
    fast = fast.next.next;
    slow = slow.next;
  }
  return slow;
}
```

https://leetcode-cn.com/problems/middle-of-the-linked-list/

## 删除链表的倒数第 N 个结点

给定一个链表，删除链表的倒数第 n 个节点，并且返回链表的头结点。

**示例：**

```bash
给定一个链表: 1->2->3->4->5, 和 n = 2.
当删除了倒数第二个节点后，链表变为 1->2->3->5.
```

**题解：**

```bash
/**
 * Definition for singly-linked list.
 * function ListNode(val) {
 *     this.val = val;
 *     this.next = null;
 * }
 */
/**
 * @param {ListNode} head
 * @param {number} n
 * @return {ListNode}
 */

var removeNthFromEnd = function (head, n) {
  var fast = head, slow = head;
  while (n--) {
    fast = fast.next;
  }
  if (!fast) return head.next;

  fast = fast.next;
  while (fast) {
    fast = fast.next;
    slow = slow.next
  }
  slow.next = slow.next.next;
  return head;
}
```

https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list

## 相交链表

```bash

```

https://leetcode-cn.com/problems/intersection-of-two-linked-lists/

## 链表倒数第 k 个节点

输入一个链表，输出该链表中倒数第 k 个节点。为了符合大多数人的习惯，本题从 1 开始计数，即链表的尾节点是倒数第 1 个节点。例如，一个链表有 6 个节点，从头节点开始，它们的值依次是 1、2、3、4、5、6。这个链表的倒数第 3 个节点是值为 4 的节点。

**示例：**

```bash
给定一个链表: 1->2->3->4->5, 和 k = 2.
返回链表 4->5.
```

```bash
/**
 * Definition for singly-linked list.
 * function ListNode(val) {
 *     this.val = val;
 *     this.next = null;
 * }
 */
/**
 * @param {ListNode} head
 * @param {number} k
 * @return {ListNode}
 */

var getKthFromEnd = function (head, k) {
  var fast = head, slow = head;
  while (k--) {
    fast = fast.next;
  }
  while (fast) {
    fast = fast.next;
    slow = slow.next;
  }
  return slow;
}
```

https://leetcode-cn.com/problems/lian-biao-zhong-dao-shu-di-kge-jie-dian-lcof/

## 删除链表的节点

给定单向链表的头指针和一个要删除的节点的值，定义一个函数删除该节点。
返回删除后的链表的头节点。

**示例 1：**

```bash
输入: head = [4,5,1,9], val = 5
输出: [4,1,9]
解释: 给定你链表中值为 5 的第二个节点，那么在调用了你的函数之后，该链表应变为 4 -> 1 -> 9.
```

**示例 2：**

```bash
输入: head = [4,5,1,9], val = 1
输出: [4,5,9]
解释: 给定你链表中值为 1 的第三个节点，那么在调用了你的函数之后，该链表应变为 4 -> 5 -> 9.
```

**题解：**

```bash
/**
 * Definition for singly-linked list.
 * function ListNode(val) {
 *     this.val = val;
 *     this.next = null;
 * }
 */
/**
 * @param {ListNode} head
 * @param {number} val
 * @return {ListNode}
 */

var deleteNode = function (head, val) {
  if (head.val === val) return head.next;
  var curr = head, prev = head;
  while (curr) {
    if (curr.val === val) {
      prev.next = curr.next;
      return head;
    } else {
      prev = curr;
      curr = curr.next;
    }
  }
  return head;
}
```

https://leetcode-cn.com/problems/shan-chu-lian-biao-de-jie-dian-lcof/

## 回文链表

编写一个函数，检查输入的链表是否是回文的。

**示例：**

```bash
输入： 1->2
输出： false

输入： 1->2->2->1
输出： true
```

**题解：**

```bash
/**
 * Definition for singly-linked list.
 * function ListNode(val) {
 *     this.val = val;
 *     this.next = null;
 * }
 */
/**
 * @param {ListNode} head
 * @return {boolean}
 */

var isPalindrome = function (head) {
  var fast = head, slow = head;
  while (fast && fast.next) {
    fast = fast.next.next;
    slow = slow.next;
  }
  var pre = null, next = null;
  while (slow) {
    next = slow.next;
    slow.next = pre;
    pre = slow;
    slow = next;
  }
  var node = head;
  while (pre) {
    if (pre.val !== node.val) return false;
    pre = pre.next;
    node = node.next;
  }
  return true
}
```

https://leetcode-cn.com/problems/palindrome-linked-list-lcci/

## 移除链表中所有等于 val 的结点

删除链表中等于给定值 val 的所有节点。

**示例：**

```bash
输入: 1->2->6->3->4->5->6, val = 6
输出: 1->2->3->4->5
```

**题解：**

```bash
/**
 * Definition for singly-linked list.
 * function ListNode(val) {
 *     this.val = val;
 *     this.next = null;
 * }
 */
/**
 * @param {ListNode} head
 * @param {number} val
 * @return {ListNode}
 */

var removeElements = function (head, val) {
  var newHead = new ListNode(null);
  var prev = newHead, curr = head;
  newHead.next = head;

  while (curr) {
    if (curr.val === val) {
      prev.next = curr.next;
      curr = prev.next;
    } else {
      prev = curr;
      curr = curr.next;
    }
  }
  return newHead.next;
}
```

https://leetcode-cn.com/problems/remove-linked-list-elements/
