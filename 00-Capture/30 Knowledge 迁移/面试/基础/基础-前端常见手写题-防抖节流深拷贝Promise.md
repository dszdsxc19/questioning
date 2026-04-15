---
tags:
  - 前端
  - 面试
  - 手写题
  - JavaScript
created: 2026-03-03
type: capture
status: inbox
updated: 2026-04-14
source_type: migrated_note
source_path: "面试/基础/基础-前端常见手写题-防抖节流深拷贝Promise.md"
---

# 前端常见手写题

这四个手写题堪称前端面试的"试金石"。对于具备两年半开发经验的前端工程师来说，面试官期待看到的不仅是基础的实现，更看重你对边界情况、执行上下文 (`this`)、参数传递以及底层逻辑的思考。

简单死记硬背代码片段收效甚微。更高效的复习方法是理解它们试图解决的业务痛点。以下为你逐一拆解这四大核心考点。

## 1. 防抖 (Debounce)

### 核心逻辑

在事件被高频触发时，只在最后一次事件触发后的指定时间执行回调。如果在等待时间内事件再次被触发，则重新计算等待时间。你可以将其想象成进电梯，只要一直有人进去，电梯门就会一直开着等待，直到一段时间没人进去了，门才会关闭。

### 面试核心考察点

正确绑定 `this` 上下文并传递 `arguments` 参数。

```javascript
function debounce(fn, delay) {
  let timer = null;
  return function (...args) {
    // 每次触发都清除上一次的定时器
    if (timer) {
      clearTimeout(timer);
    }
    // 重新开启一个新的定时器
    timer = setTimeout(() => {
      fn.apply(this, args);
    }, delay);
  };
}
```

## 2. 节流 (Throttle)

### 核心逻辑

高频触发事件时，在固定的时间窗口内只执行一次回调函数。这类似于游戏中的技能冷却机制，无论你按键多快，技能也必须等待冷却时间结束才能再次释放。

### 面试核心考察点

控制执行频率。常见的实现方式有时间戳和定时器两种，这里展示更易于控制首尾执行状态的定时器版本。

```javascript
function throttle(fn, delay) {
  let timer = null;
  return function (...args) {
    // 如果定时器还在，说明还在冷却中，直接跳过
    if (timer) return;

    // 开启定时器，到达时间后执行逻辑并清空定时器
    timer = setTimeout(() => {
      fn.apply(this, args);
      timer = null;
    }, delay);
  };
}
```

## 3. 深拷贝 (Deep Clone)

### 核心逻辑

在内存中开辟一块全新的空间，完全复制原始对象的所有嵌套属性，使得新旧对象互不干扰。面试官通常会先问 `JSON.parse(JSON.stringify(obj))` 有什么缺点，此时你需要指出它会丢失函数、`undefined`、`Symbol`，并且无法处理循环引用。

### 面试核心考察点

使用递归处理深度嵌套，同时必须使用 `WeakMap` 记录已经拷贝过的对象，防止因为循环引用导致调用栈溢出。

```javascript
function deepClone(obj, hash = new WeakMap()) {
  // 处理 null、undefined 和非对象类型
  if (obj === null || typeof obj !== 'object') return obj;
  // 处理日期和正则对象
  if (obj instanceof Date) return new Date(obj);
  if (obj instanceof RegExp) return new RegExp(obj);

  // 检查是否已经拷贝过该对象，解决循环引用
  if (hash.has(obj)) return hash.get(obj);

  // 保持原型链，创建新对象或数组
  let cloneObj = new obj.constructor();
  hash.set(obj, cloneObj);

  // 递归拷贝属性
  for (let key in obj) {
    if (Object.prototype.hasOwnProperty.call(obj, key)) {
      cloneObj[key] = deepClone(obj[key], hash);
    }
  }
  return cloneObj;
}
```

## 4. Promise 全家桶

### 核心逻辑

主要用于处理复杂的异步并发场景。家族成员包括 `Promise.all`、`Promise.race`、`Promise.allSettled` 和 `Promise.any`。在手写题中，`Promise.all` 的出场率极高。

### 面试核心考察点

`Promise.all` 需要等待所有传入的 Promise 成功才返回结果数组，任何一个失败都会立即触发整体的拒绝状态。关键在于保证输出结果的顺序与输入顺序完全一致。

```javascript
Promise.myAll = function (promises) {
  return new Promise((resolve, reject) => {
    // 处理传入的可能是不具备迭代器属性的值
    if (!Array.isArray(promises)) {
      return reject(new TypeError('Argument must be an array'));
    }

    const result = [];
    let completedCount = 0;
    const len = promises.length;

    if (len === 0) return resolve(result);

    for (let i = 0; i < len; i++) {
      // 用 Promise.resolve 包装一下，防止传入的元素不是 Promise
      Promise.resolve(promises[i]).then(
        (value) => {
          // 必须按索引赋值，保证输出顺序
          result[i] = value;
          completedCount++;
          if (completedCount === len) {
            resolve(result);
          }
        },
        (reason) => {
          // 只要有一个失败，整体立即失败
          reject(reason);
        }
      );
    }
  });
};
```

---

## 复习建议

针对这几个难点，最好的复习方案脱离死记硬背代码，自己新建一个 HTML 文件，写几段测试用例在浏览器控制台里单步调试，看看数据流向和 `this` 的变化。这样在面试时即使遇到变种题，也能游刃有余。
