---
title: 数据结构和算法 - 树
date: 2020-05-29 17:59:13
tags:
  - 数据结构
  - 算法
  - 树
categories:
  - [数据结构和算法]
# top_image: https://rmt.dogedoge.com/fetch/fluid/storage/bg/dojm2h.png?w=1920&q=100&fmt=webp
excerpt: 树中的每个元素我们叫作"节点"，用来连线相邻节点之间的关系，我们叫作"父子关系"。同一个父节点的子节点互称为兄弟节点。没有父节点的节点叫作根节点。没有子节点的节点叫做叶子节点或叶节点。节点的高度 = 节点到叶子节点的最长路径(边数)；节点的深度 = 根节点到这个节点所经历的边的个数；节点的层数 = 节点的深度 + 1；树的高度 = 根节点的高度。
---

树中的每个元素我们叫作"节点"，用来连线相邻节点之间的关系，我们叫作"父子关系"。同一个父节点的子节点互称为兄弟节点。没有父节点的节点叫作根节点。没有子节点的节点叫做叶子节点或叶节点。

- 节点的高度 = 节点到叶子节点的最长路径(边数)
- 节点的深度 = 根节点到这个节点所经历的边的个数
- 节点的层数 = 节点的深度 + 1
- 树的高度 = 根节点的高度

## 二叉树

每个节点最多有两个叉，也就是两个子节点，分别是左子节点和右子节点。

编号 2 的二叉树中，叶子节点全都在最底层，除了叶子节点外，每个节点都有左右两个子节点，这种二叉树就叫做满二叉树。
编号 3 的二叉树中，叶子节点都在最底下两层，最后一层的叶子节点都靠左排列，并且除了最后一层，其他层的节点个数都要达到最大，这种二叉树叫做完全二叉树。
满二叉树是完全二叉树的一种特殊情况。二叉树既可以用链式存储，也可以用数组顺序存储。数组存储的方式比较适合完全二叉树，其他类型的二叉树用数组存储会比较浪费时间。

二叉树遍历：前序遍历、中序遍历、后序遍历
前序遍历：对于树中任意节点，先打印这个节点，然后打印它的左子树，最后打印它的右子树
中序遍历：对于树中任意节点，先打印它的左子树，然后再打印它本身，最后打印它的右子树
后序遍历：对于树中任意节点，先打印它的左子树，然后打印它的右子树，最后打印节点本身

二叉查找树
二叉查找树是二叉树中最常用的一种类型，也叫二叉搜索树。二叉查找树要求，在树中任意一个节点，其左子树中的每个节点的值，都要小于这个节点的值，而右子树节点的值都要大于这个节点的值。二叉查找树支持快速查找、插入、删除操作。二叉查找树的中序遍历可以输出有序的数据序列，时间复杂度为 O(n)。

## 二叉树的镜像

请完成一个函数，输入一个二叉树，该函数输出它的镜像。

**例如:**
输入：

```bash
     4
   /   \
  2     7
 / \   / \
1   3 6   9
```

镜像输出：

```bash
     4
   /   \
  7     2
 / \   / \
9   6 3   1
```

**示例 1：**

```bash
输入：root = [4,2,7,1,3,6,9]
输出：[4,7,2,9,6,3,1]
```

**题解：**

```bash
/**
 * Definition for a binary tree node.
 * function TreeNode(val) {
 *     this.val = val;
 *     this.left = this.right = null;
 * }
 */

/**
 * @param {TreeNode} root
 * @return {TreeNode}
 */

var mirrorTree = function(root) {
    if (!root) return root;
    const temp = root.left;
    root.left = root.right;
    root.right = temp;

    mirrorTree(root.right);
    mirrorTree(root.left);

    return root;
};
```

https://leetcode-cn.com/problems/er-cha-shu-de-jing-xiang-lcof/

## 二叉树的深度

输入一棵二叉树的根节点，求该树的深度。从根节点到叶节点依次经过的节点（含根、叶节点）形成树的一条路径，最长路径的长度为树的深度。

**例如：**

给定二叉树 [3,9,20,null,null,15,7]，

```bash
    3
   / \
  9  20
    /  \
   15   7
```

返回它的最大深度 3 。

**题解：**

```bash
/**
 * Definition for a binary tree node.
 * function TreeNode(val) {
 *     this.val = val;
 *     this.left = this.right = null;
 * }
 */

/**
 * @param {TreeNode} root
 * @return {number}
 */

var maxDepth = function (root) {
    if (!root) return 0;
    return Math.max(maxDepth(root.left), maxDepth(root.right)) + 1;
}
```

https://leetcode-cn.com/problems/er-cha-shu-de-shen-du-lcof/

## 从上到下打印二叉树 Ⅰ

从上到下打印出二叉树的每个节点，同一层的节点按照从左到右的顺序打印。

例如:
给定二叉树: [3,9,20,null,null,15,7],

```bash
    3
   / \
  9  20
    /  \
   15   7
```

返回： [3,9,20,15,7]

**题解：**

```bash
/**
 * Definition for a binary tree node.
 * function TreeNode(val) {
 *     this.val = val;
 *     this.left = this.right = null;
 * }
 */

/**
 * @param {TreeNode} root
 * @return {number[]}
 */

var levelOrder = function(root) {
    if (!root) return [];
    let queue = [root], res = [];
    while (queue.length) {
        const firstNode = queue.shift();
        res.push(firstNode.val)

        firstNode.left && queue.push(firstNode.left);
        firstNode.right && queue.push(firstNode.right);
    }
    return res;
};
```

https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-lcof/

## 从上到下打印二叉树 Ⅱ

从上到下按层打印二叉树，同一层的节点按从左到右的顺序打印，每一层打印到一行。

**例如:**
给定二叉树: [3,9,20,null,null,15,7],

```bash
    3
   / \
  9  20
    /  \
   15   7
```

返回其层次遍历结果：

```bash
[
  [3],
  [9,20],
  [15,7]
]
```

**题解：**

```bash
/**
 * Definition for a binary tree node.
 * function TreeNode(val) {
 *     this.val = val;
 *     this.left = this.right = null;
 * }
 */

/**
 * @param {TreeNode} root
 * @return {number[][]}
 */

var levelOrder = function (root) {
  if (!root) return [];

  let queue = [root], res = [], level = 0;
  while (queue.length) {
    let levelNum = queue.length;
    res[level] = [];

    while (levelNum--) {
      const firstNode = queue.shift();
      res[level].push(firstNode.val)

      firstNode.left && queue.push(firstNode.left)
      firstNode.right && queue.push(firstNode.right)
    }

    level++
  }
  return res;
}
```

https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-ii-lcof/

## 从上到下打印二叉树 Ⅲ

请实现一个函数按照之字形顺序打印二叉树，即第一行按照从左到右的顺序打印，第二层按照从右到左的顺序打印，第三行再按照从左到右的顺序打印，其他行以此类推。

例如:
给定二叉树: [3,9,20,null,null,15,7],

```bash
    3
   / \
  9  20
    /  \
   15   7
```

返回其层次遍历结果：

```bash
[
  [3],
  [20,9],
  [15,7]
]
```

**题解：**

```bash
/**
 * Definition for a binary tree node.
 * function TreeNode(val) {
 *     this.val = val;
 *     this.left = this.right = null;
 * }
 */

/**
 * @param {TreeNode} root
 * @return {number[][]}
 */

var levelOrder = function(root) {
    if (!root) return [];

    let queue = [root], res = [], level = 0, levelNum = 0;
    while(queue.length) {
        levelNum = queue.length;
        res[level] = [];
        while(levelNum--) {
            if(level % 2 === 1) {
                const firstNode = queue.shift();
                res[level].push(firstNode.val);
                firstNode.right && queue.push(firstNode.right);
                firstNode.left && queue.push(firstNode.left);
            } else {
                const firstNode = queue.pop();
                res[level].push(firstNode.val);
                firstNode.left && queue.unshift(firstNode.left);
                firstNode.right && queue.unshift(firstNode.right);
            }
        }
        level++;
    }

    return res;
};
```

https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-iii-lcof/

## 平衡二叉树

输入一棵二叉树的根节点，判断该树是不是平衡二叉树。如果某二叉树中任意节点的左右子树的深度相差不超过 1，那么它就是一棵平衡二叉树。

**示例 1:**

给定二叉树 [3,9,20,null,null,15,7]

```bash
    3
   / \
  9  20
    /  \
   15   7
```

返回 true 。

**示例 2:**

给定二叉树 [1,2,2,3,3,null,null,4,4]

```bash
       1
      / \
     2   2
    / \
   3   3
  / \
 4   4
```

返回 false 。

**题解：**

```bash
/**
 * Definition for a binary tree node.
 * function TreeNode(val) {
 *     this.val = val;
 *     this.left = this.right = null;
 * }
 */

/**
 * @param {TreeNode} root
 * @return {boolean}
 */

var isBalanced = function(root) {
    return _isBalanced(root) !== -1;
};

function _isBalanced(root) {
    if (!root) return 0;
    let left = _isBalanced(root.left);
    if (left === -1) {
        return -1;
    }
    let right = _isBalanced(root.right);
    if (right === -1) {
        return -1;
    }
    return Math.abs(left - right) > 1 ? -1 : Math.max(left, right) + 1;
}
```

https://leetcode-cn.com/problems/ping-heng-er-cha-shu-lcof/

## 对称的二叉树

请实现一个函数，用来判断一棵二叉树是不是对称的。如果一棵二叉树和它的镜像一样，那么它是对称的。

例如，二叉树 [1,2,2,3,4,4,3] 是对称的。

```bash
    1
   / \
  2   2
 / \ / \
3  4 4  3
```

但是下面这个 [1,2,2,null,3,null,3] 则不是镜像对称的:

```bash
    1
   / \
  2   2
   \   \
   3    3
```

**示例 1：**

```bash
输入：root = [1,2,2,3,4,4,3]
输出：true
```

**示例 2：**

```bash
输入：root = [1,2,2,null,3,null,3]
输出：false
```

**题解：**

```bash
/**
 * Definition for a binary tree node.
 * function TreeNode(val) {
 *     this.val = val;
 *     this.left = this.right = null;
 * }
 */

/**
 * @param {TreeNode} root
 * @return {boolean}
 */

var isSymmetric = function(root) {
    if (!root) return true;
    function mirror(left, right) {
        if (left === null && right === null) return true;
        if (left === null || right === null) return false;
        if (left.val !== right.val) return false;

        return mirror(left.left, right.right) && mirror(left.right, right.left);
    }
    return mirror(root.left, root.right)
};
```

https://leetcode-cn.com/problems/dui-cheng-de-er-cha-shu-lcof/
