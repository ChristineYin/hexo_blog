---
title: JS 之 instanceof、typeof
date: 2020-05-28 21:46:16
tags:
  - JS
  - instanceof
  - typeof
categories:
  - [JS]
excerpt: instanceof、typeof
---

## 实现 instanceof

**1. 使用方式**

```bash
a instance Object
```

**2. 实现方法**

```bash
function myInstanceof (target, origin) {
  const proto = target.__proto__;
  if (proto) {
    if (origin.prototype === proto) {
      return true;
    } else {
      return myInstanceof(proto, origin)
    }
  } else {
    return false;
  }
}
```

## 类型判断

```bash
var class2type = {}

// 生成映射
'Boolean Number String Function Array Date RegExp Object Error Null Undefined'
  .split(' ')
  .map(function (item, index) {
    class2type['[object ' + item + ']'] = item.toLowerCase();
  })

function type (obj) {
  if (obj == null) {
    return obj + '';
  }
  return typeof obj === 'object' || typeof obj === 'function'
    ? class2type[Object.prototype.toString.call(obj)] || 'object'
    : typeof obj;
}

function isFunction (obj) {
  return type(obj) === 'function';
}

var isArray =
  Array.isArray ||
  function (obj) {
    return type(obj) === 'array';
  }
```
