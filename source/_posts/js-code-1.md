---
title: JS 之 函数柯里化
tags: [JS, 函数柯里化]
# index_img: https://ss2.bdstatic.com/70cFvnSh_Q1YnxGkpoWK1HF6hhy/it/u=2050318681,1081448419&fm=26&gp=0.jpg
date: 2020-05-28 21:46:16
---

## 函数柯里化

函数柯里化是把接受多个参数的函数变换成接受一个单一参数（最初函数的第一个参数）的函数，并且返回接受余下的参数而且返回结果的新函数技术。

**1. 函数柯里化的主要作用:**

- 参数复用
- 提前返回 - 返回接受余下的参数且返回结果的新函数
- 延迟执行 - 返回新函数，等待执行

**2. 函数柯里化的使用**

```bash
function sum (a, b, c) {
    return a + b + c;
}
```

以上代码如果用函数柯里化的方式实现，就变成以下形式

```bash
function sum (a) {
  return function(b) {
      return function(c) {
          return a + b + c;
      }
  }
}
console.log(sum(1)(2)(3));   // 6
```

**3. 实现柯里化函数**

实现一个 curry 函数，可以将所有普通函数进行柯里化

```bash
function curry (fn, args = []) {
    return function (){
        let rest = [...args, ...arguments];
        if (rest.length < fn.length) {
            return curry.call(this,fn,rest);
        } else{
            return fn.apply(this,rest);
        }
    }
}
```

如果将上面的 sum 函数进行柯里化，便可以直接作为参数传入 curry 函数中

```bash
let sumFn = curry(sum);
console.log(sumFn(1)(2)(3));   // 6
console.log(sumFn(1)(2, 3));   // 6
```
