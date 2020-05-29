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

链表不需要一块连续的内存空间，它通过"指针"将一组零散的内存块串联起来使用，其中，我们把内存块称为链表的"结点"。为了将所有结点串起来，每个链表的结点除了存储数据之外，还需要记录链上的下一个结点的地址，我们把这个记录下一个结点地址的指针叫作后继指针next。我们习惯性的将第一个结点叫作头结点，把最后一个结点叫做尾节点。尾结点指向一个空地址NULL，表示这是链表上的最后一个结点。常见的链表结构分别是单链表、双向链表、循环链表。
- 循环链表是一种特殊的单链表，与单链表唯一的区别就是尾结点指针是指向链表的头结点。
- 双向链表它支持两个方向，每个结点不止有一个后继指针next指向后面的节点，还有一个前驱指针prev指向前面的结点。


## 合并两个有序链表
```bash
/**
 * function ListNode(val) {
 *  this.val = val;
 *  this.next = null;
 * }
 */
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
```bash
var hasCycle = function (head) {
  while (head) {
    if (head.flag) return true;
    head.flag = true;
    head = head.next;
  }
  return false;
}
```
https://leetcode-cn.com/problems/linked-list-cycle/solution/pan-duan-yi-ge-dan-lian-biao-shi-fou-you-huan-by-u/


## 链表翻转
```bash
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
```bash
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


## 删除链表的倒数第N个结点
```bash
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



## 链表倒数第k个节点
```bash
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
```bash
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
```bash
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


## 移除链表中所有等于val的结点
```bash
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
