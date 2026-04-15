---
title: "使用 Sentry 做异常监控 - Sentry 是如何做到自动捕获前端应用异常的呢？"
source: "https://juejin.cn/post/7145420611639050271"
author:
  - "[[0o华仔o0]]"
published: 2022-09-20
created: 2026-03-01
description: "本文介绍 Sentry 自动捕获前端异常的机制原理"
tags:
  - "clippings"
  - "error-monitoring"
  - "sentry"
type: capture
status: inbox
updated: 2026-04-14
source_type: migrated_note
source_path: "面试/基础/基础-Sentry异常监控-自动捕获前端异常原理.md"
---
## 前言

自从知道了 `Sentry` 、 `Fundbug` 可用于异常监控之后，小编就一直对它们能自动捕获前端异常的机制非常感兴趣。最近为了解决 `Qiankun` 下 `Sentry` 异常上报不匹配的问题，小编特意去翻阅了一下 `Sentry` 的源代码，在解决了问题的同时，也对 `Sentry` 异常上报的机制有了一个清晰的认识，收获满满。

在这里，小编将自己学习所得总结出来，查漏补缺的同时，也希望能给到同样对 `Sentry` 工作机制感兴趣的同学一些帮助。

## 目录

- [常见的前端异常及其捕获方式](#常见的前端异常及其捕获方式)
  - [js 代码执行时异常](#js-代码执行时异常)
  - [promise 类异常](#promise-类异常)
  - [静态资源加载类型异常](#静态资源加载类型异常)
  - [接口请求类型异常](#接口请求类型异常)
  - [跨域脚本执行异常](#跨域脚本执行异常)
- [Sentry 异常监控原理](#sentry-异常监控原理)
  - [异常详情获取](#异常详情获取)
    - [劫持覆写 window.onerror](#劫持覆写-windowonerror)
    - [劫持覆写 window.unhandledrejection](#劫持覆写-windowunhandledrejection)
    - [特殊上下文标记](#特殊上下文标记)
  - [用户行为获取](#用户行为获取)
    - [1. 收集页面跳转行为](#1-收集页面跳转行为)
    - [2. 收集鼠标-click--键盘-keypress-行为](#2-收集鼠标-click--键盘-keypress-行为)
    - [3. 收集-fetch--xhr-接口请求行为](#3-收集-fetch--xhr-接口请求行为)
    - [4. 收集-console-打印行为](#4-收集-console-打印行为)
- [总结](#总结)

## 常见的前端异常及其捕获方式

前端异常通常可以分为以下几种类型:

- `js` 代码执行时异常；
- `promise` 类型异常；
- `资源加载` 类型异常；
- `网络请求` 类型异常；
- `跨域脚本` 执行异常；

不同类型的异常，捕获方式不同。

### js 代码执行时异常

`js` 代码执行异常，是我们经常遇到异常。

这一类型的异常，又可以具体细分为:

- `Error` ，最基本的错误类型，其他的错误类型都继承自该类型。通过 `Error` ，我们可以自定义 `Error` 类型。
- `RangeError`: 范围错误。当出现堆栈溢出(递归没有终止条件)、数值超出范围(`new Array` 传入负数或者一个特别大的整数)情况时会抛出这个异常。
- `ReferenceError` ，引用错误。当一个不存在的对象被引用时发生的异常。
- `SyntaxError` ，语法错误。如变量以数字开头；花括号没有闭合等。
- `TypeError` ，类型错误。如把 number 当 str 使用。
- `URIError` ，向全局 `URI` 处理函数传递一个不合法的 `URI` 时，就会抛出这个异常。如使用 `decodeURI('%')` 、 `decodeURIComponent('%')` 。
- `EvalError` ， 一个关于 eval 的异常，不会被 javascript 抛出。

**捕获方式**: 通常通过 `try...catch` 语句块来捕获。如果不使用 `try...catch` ，也可以通过 `window.onerror = callback` 或者 `window.addEventListener('error', callback)` 的方式进行全局捕获。

### promise 类异常

在使用 `promise` 时，如果 `promise` 被 `reject` 但没有做 `catch` 处理时，就会抛出 `promise` 类异常。

```javascript
Promise.reject(); // Uncaught (in promise) undefined
```

**捕获方式**: `promise` 类型的异常无法被 `try...catch` 捕获，也无法被 `window.onerror = callback` 或者 `window.addEventListener('error', callback)` 的方式全局捕获。针对这一类型的异常，我们需要通过 `window.onrejectionhandled = callback` 或者 `window.addListener('rejectionhandled'， callback)` 的方式去全局捕获。

### 静态资源加载类型异常

有的时候，如果我们页面的 `img` 、 `js` 、 `css` 等资源链接失效，就会提示资源类型加载如异常。

```html
<img src="localhost:3000/data.png" /> <!-- Get localhost:3000/data.png net::ERR_FILE_NOT_FOUND -->
```

**捕获方式**: 通过 `window.addEventListener('error', callback, true)` 的方式进行全局捕获。

> 注意：使用 `window.onerror = callback` 的方式是无法捕获静态资源类异常的。原因是资源类型错误没有冒泡，只能在捕获阶段捕获，而 `window.onerror` 是通过在冒泡阶段捕获错误，对静态资源加载类型异常无效。

### 接口请求类型异常

在浏览器端发起一个接口请求时，如果请求的 `url` 有问题，也会抛出异常。

不同的请求方式，异常捕获方式也不相同:

- **通过 `fetch` 发起**: 可以通过 `fetch(url).then(callback).catch(callback)` 的方式去捕获异常。
- **通过 `xhr` 实例发起**:
  - 如果是 `xhr.open` 方法执行时出现异常，可以通过 `window.addEventListener('error', callback)` 或者 `window.onerror` 的方式捕获异常。
  - 如果是 `xhr.send` 方法执行时出现异常，可以通过 `xhr.onerror` 或者 `xhr.addEventListener('error', callback)` 的方式捕获异常。

```javascript
// xhr.open 异常
xhr.open('GET', "https://")  // Uncaught DOMException: Failed to execute 'open' on 'XMLHttpRequest': Invalid URL

// xhr.send 异常
xhr.open('get', '/user/userInfo');
xhr.send();  // send localhost:3000/user/userinfo net::ERR_FAILED
```

### 跨域脚本执行异常

当项目中引用的第三方脚本执行发生错误时，会抛出一类特殊的异常。这类型异常和我们刚才讲过的异常都不同，它的 `msg` 只有 `'Script error'` 信息，没有具体的行、列、类型信息。

之所以会这样，是因为浏览器的安全机制：浏览器只允许同域下的脚本捕获具体异常信息，跨域脚本中的异常，不会报告错误的细节。

**捕获方式**: 通过 `window.addEventListener('error', callback)` 或者 `window.onerror` 的方式捕获异常。

如果要获取这类异常的详情，需要做以下两个操作:
1. 在发起请求的 `script` 标签上添加 `crossorigin="anonymous"`
2. 请求响应头中添加 `Access-Control-Allow-Origin: *`

## Sentry 异常监控原理

有效的异常监控需要做到以下 3 点:

1. **异常自动推送**: 线上应用出现异常时，可以及时推送给开发人员
2. **异常详情获取**: 上报的异常，含有异常类型、发生异常的源文件及行列信息、异常的追踪栈信息等详细信息
3. **用户行为获取**: 可以获取发生异常的用户行为，帮助重现问题

### 异常详情获取

为了能自动捕获应用异常， `Sentry` 劫持覆写了 `window.onerror` 和 `window.unhandledrejection` 这两个 `api` 。

#### 劫持覆写 window.onerror

```javascript
oldErrorHandler = window.onerror;
window.onerror = function (msg, url, line, column, error) {
    // 收集异常信息并上报
    triggerHandlers('error', {
        column: column,
        error: error,
        line: line,
        msg: msg,
        url: url,
    });
    if (oldErrorHandler) {
        return oldErrorHandler.apply(this, arguments);
    }
    return false;
};
```

#### 劫持覆写 window.unhandledrejection

```javascript
oldOnUnhandledRejectionHandler = window.onunhandledrejection;
window.onunhandledrejection = function (e) {
    // 收集异常信息并上报
    triggerHandlers('unhandledrejection', e);
    if (oldOnUnhandledRejectionHandler) {
        return oldOnUnhandledRejectionHandler.apply(this, arguments);
    }
    return true;
};
```

#### 特殊上下文标记

为了能获取更详尽的异常信息， `Sentry` 在内部对异常发生的特殊上下文做了标记。这些特殊上下文包括: `dom` 节点事件回调、 `setTimeout` / `setInterval` 回调、 `xhr` 接口调用、 `requestAnimationFrame` 回调等。

##### 1. 标记 setTimeout / setInterval / requestAnimationFrame

`Sentry` 劫持覆写了原生的 `setTimeout` / `setInterval` / `requestAnimationFrame` 方法。新的方法调用时，会使用 `try ... catch` 语句块包裹 `callback` 。

```javascript
var originSetTimeout = window.setTimeout;
window.setTimeout = function() {
    var args = [];
    for (var _i = 0; _i < arguments.length; _i++) {
        args[_i] = arguments[_i];
    }
    var originalCallback = args[0];
    // wrap$1 会对 setTimeout 的入参 callback 使用 try...catch 进行包装
    // 并在 catch 中上报异常
    args[0] = wrap$1(originalCallback, {
        mechanism: {
            data: { function: getFunctionName(original) },
            handled: true,
            // 异常的上下文是 setTimeout
            type: 'setTimeout',
        },
    });
    return original.apply(this, args);
}
```

##### 2. 标记 dom 事件 handler

所有的 `dom` 节点都继承自 `window.Node` 对象， `dom` 对象的 `addEventListener` 方法来自 `Node` 的 `prototype` 对象。

`Sentry` 对 `Node.prototype.addEventListener` 进行了劫持覆写:

```javascript
function xxx() {
    var proto = window.Node.prototype;
    // 覆写 addEventListener 方法
    fill(proto, 'addEventListener', function (original) {
        return function (eventName, fn, options) {
            try {
                if (typeof fn.handleEvent === 'function') {
                    // 使用 try...catch 包括 handleEvent
                    fn.handleEvent = wrap$1(fn.handleEvent.bind(fn), {
                        mechanism: {
                            data: {
                                function: 'handleEvent',
                                handler: getFunctionName(fn),
                                target: target,
                            },
                            handled: true,
                            type: 'instrument',
                        },
                    });
                }
            }
            catch (err) {}
            return original.apply(this, [
                eventName,
                wrap$1(fn, {
                    mechanism: {
                        data: {
                            function: 'addEventListener',
                            handler: getFunctionName(fn),
                            target: target,
                        },
                        handled: true,
                        type: 'instrument',
                    },
                }),
                options,
            ]);
        };
    });
}
```

##### 3. 标记 xhr 接口回调

`Sentry` 先对 `XMLHttpRequest.prototype.send` 方法劫持覆写，然后对 `xhr` 对象的 `onload` 、 `onerror` 、 `onprogress` 、 `onreadystatechange` 方法进行了劫持覆写，使用 `try ... catch` 语句块包裹传入的 `callback` 。

```javascript
fill(XMLHttpRequest.prototype, 'send', _wrapXHR);

function _wrapXHR(originalSend) {
    return function () {
        var args = [];
        for (var _i = 0; _i < arguments.length; _i++) {
            args[_i] = arguments[_i];
        }
        var xhr = this;
        var xmlHttpRequestProps = ['onload', 'onerror', 'onprogress', 'onreadystatechange'];

        // 劫持覆写
        xmlHttpRequestProps.forEach(function (prop) {
            if (prop in xhr && typeof xhr[prop] === 'function') {
                fill(xhr, prop, function (original) {
                    var wrapOptions = {
                        mechanism: {
                            data: {
                                function: prop,
                                handler: getFunctionName(original),
                            },
                            handled: true,
                            type: 'instrument',
                        },
                    };
                    return wrap$1(original, wrapOptions);
                });
            }
        });
        return originalSend.apply(this, args);
    };
}
```

### 用户行为获取

常见的用户行为可以归纳为 `页面跳转` 、 `鼠标 click 行为` 、 `键盘 keypress 行为` 、 `fetch / xhr 接口请求` 、 `console 打印信息` 。

`Sentry` 通过**劫持覆写上述操作涉及的 api** 来收集用户行为。

#### 1. 收集页面跳转行为

`Sentry` 劫持并覆写了原生 `history` 的 `pushState` 、 `replaceState` 方法和 `window` 的 `onpopstate` 。

```javascript
// 劫持覆写 onpopstate
var oldPopState = window.onpopstate;
var lastHref;

window.onpopstate = function() {
    var to = window.location.href;
    var from = lastHref;
    lastHref = to;
    // 将页面跳转行为收集起来
    triggerHandlers('history', {
        from: from,
        to: to,
    });
    if (oldOnPopState) {
        try {
            return oldOnPopState.apply(this, args);
        } catch (e) {}
    }
}

// 劫持覆写 pushState
var originPushState = window.history.pushState;
window.history.pushState = function() {
    var args = [];
    for (var i = 0; i < arguments.length; i++) {
        args[i] = arguments[i];
    }
    var url = args.length > 2 ? args[2] : undefined;
    if (url) {
        var from = lastHref;
        var to = String(url);
        lastHref = to;
        triggerHandlers('history', {
            from: from,
            to: to,
        });
     }
     return originPushState.apply(this, args);
}
```

#### 2. 收集鼠标 click / 键盘 keypress 行为

`Sentry` 做了双保险操作:
- 通过 `document` 代理 `click` 、 `keypress` 事件来收集
- 通过劫持 `addEventListener` 方法来收集

```javascript
function instrumentDOM() {
    var triggerDOMHandler = triggerHandlers.bind(null, 'dom');
    var globalDOMEventHandler = makeDOMEventHandler(triggerDOMHandler, true);

    // 通过 document 代理 click、keypress 事件的方式收集
    document.addEventListener('click', globalDOMEventHandler, false);
    document.addEventListener('keypress', globalDOMEventHandler, false);

    // 劫持覆写 Node.prototype.addEventListener
    fill(proto, 'addEventListener', function (originalAddEventListener) {
        return function (type, listener, options) {
            // click、keypress 事件，要做特殊处理
            if (type === 'click' || type == 'keypress') {
                var el = this;
                var handlers_1 = (el.__sentry_instrumentation_handlers__ = el.__sentry_instrumentation_handlers__ || {});
                var handlerForType = (handlers_1[type] = handlers_1[type] || { refCount: 0 });

                if (!handlerForType.handler) {
                    var handler = makeDOMEventHandler(triggerDOMHandler);
                    handlerForType.handler = handler;
                    originalAddEventListener.call(this, type, handler, options);
                }
                handlerForType.refCount += 1;
            }
            return originalAddEventListener.call(this, type, listener, options);
        };
    });
}
```

#### 3. 收集 fetch / xhr 接口请求行为

`Sentry` 对原生的 `fetch` 和 `xhr` 做了劫持覆写:

```javascript
// 劫持覆写 fetch
var originFetch = window.fetch;
window.fetch = function() {
    var args = [];
    for (var _i = 0; _i < arguments.length; _i++) {
        args[_i] = arguments[_i];
    }
    var handlerData = {
        args: args,
        fetchData: {
            method: getFetchMethod(args),
            url: getFetchUrl(args),
        },
        startTimestamp: Date.now(),
    };
    triggerHandlers('fetch', __assign({}, handlerData));
    return originalFetch.apply(window, args).then(function (response) {
        triggerHandlers('fetch', __assign(__assign({}, handlerData), { endTimestamp: Date.now(), response: response }));
        return response;
    }, function (error) {
        triggerHandlers('fetch', __assign(__assign({}, handlerData), { endTimestamp: Date.now(), error: error }));
        throw error;
    });
}
```

#### 4. 收集 console 打印行为

对 `console` 的 `debug` 、 `info` 、 `warn` 、 `error` 、 `log` 、 `assert` 这几个 `api` 进行劫持覆写:

```javascript
var originConsoleLog = console.log;
console.log = function() {
    var args = [];
    for (var _i = 0; _i < arguments.length; _i++) {
        args[_i] = arguments[_i];
    }
    // 收集 console.log 行为
    triggerHandlers('console', { args: args, level: 'log' });
    if (originConsoleLog) {
        originConsoleLog.apply(console, args);
    }
}
```

## 总结

Sentry 自动捕获前端异常的核心原理可以总结为:

1. **劫持全局错误处理 API**: `window.onerror`、`window.unhandledrejection`
2. **劫持特殊上下文 API**: `setTimeout`/`setInterval`、`addEventListener`、`XMLHttpRequest` 等
3. **劫持用户行为 API**: `history` API、`fetch`、`console` 等

通过在劫持的函数中使用 `try...catch` 包裹回调函数，Sentry 能够在异常发生时捕获完整的错误上下文信息，帮助开发者快速定位问题。
