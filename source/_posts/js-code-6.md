---
title: JS 之 浅拷贝和深拷贝
tags: [JS, 浅拷贝, 深拷贝]
index_img: https://ss2.bdstatic.com/70cFvnSh_Q1YnxGkpoWK1HF6hhy/it/u=2050318681,1081448419&fm=26&gp=0.jpg
date: 2020-05-28 21:46:16
---

## 浅拷贝 和 深拷贝

**浅拷贝**

1. 手动实现

```bash
const shallowClone = (target) => {
  if (typeof target === 'object' && target !== null) {
    const cloneTarget = Array.isArray(target) ? []: {};
    for (let prop in target) {
      if (target.hasOwnProperty(prop)) {
          cloneTarget[prop] = target[prop];
      }
    }
    return cloneTarget;
  } else {
    return target;
  }
}
```

2. Object.assign 浅拷贝

```bash
let obj = { name: 'sy', age: 18 };
const obj2 = Object.assign({}, obj, {name: 'sss'});
console.log(obj2);//{ name: 'sss', age: 18 }
```

3. concat 浅拷贝

```bash
let arr = [1, 2, 3];
let newArr = arr.concat();
newArr[1] = 100;
console.log(arr);//[ 1, 2, 3 ]
```

4. slice 浅拷贝

```bash
let arr = [1, 2, {val: 4}];
let newArr = arr.slice();
newArr[2].val = 1000;
```

5. ...展开运算符

```bash
let arr = [1, 2, 3];
let newArr = [...arr];   //跟arr.slice()是一样的效果
```

**深拷贝**

```bash
JSON.parse(JSON.stringify());
```