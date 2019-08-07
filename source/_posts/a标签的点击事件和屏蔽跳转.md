---
title: a标签的点击事件和屏蔽跳转
date: 2019-08-07 10:14:03
tags: 
- html
- js
categories: Programming
---

### **常用的屏蔽href跳转的方法**

不让跳, 就两个思路嘛:

1. 第一个思路就是把href干掉, 没有有效地址看你还往哪跳; 
2. 第二个思路就是href地址是有效的我也无所谓, 直接把你默认跳转这个行为艺术给禁咯.

#### **方法一(思路1)**

```html
<a href="javascript:void(0)" onclick="someFunction()">foo</a>
```
void 运算符, 对给定的表达式进行求值，然后返回 undefined。

----
#### **方法二(思路1)**

```html 	
<a href="###" onclick="someFunction()">bar</a>
```

----
#### **方法三(思路2)**

上面两种方法的href都没有有意义的href地址, 如果是一个具有有意义的链接,并且onclick之后禁止跳转到href链接的a标签呢? 
我们需要对事件处理函数的工作机制有一定的了解: 在给某一个元素添加事件处理函数后, 一旦发生预定事件, 相应的 `JavaScript` 代码就会得到执行; 那些 `JavaScript` 大妈可以返回一个结果, 而这个结果将被传递会那个是事件处理函数. 例如, 我们可以给某个链接添加一个 `onclick` 事件处理函数, 并让这个处理函数触发的代码返回布尔值 `true` 或 `false`. 这样一来, 当这个连接被点击时, 如果那段 `JavaScript` 代码返回给 `onclick` 事件处理函数的值是 `true`, `onclick` 时间处理函数将认为"这个了解被点击了"; 反之, 如果那段 `JavaScript` 代码返回给 `onclick` 事件处理函数的值是 `false`, `onclick`事件处理函数将认为"这个了解没有被点击".


```html
<a href="https://www.baidu.com" onclick="someFunction;return false;">test</a>
```
以上例子, 达到了a标签在有href地址的情况下被点击之后执行相应函数却不跳转. 因为通过return false让它觉得自己没被点, 实际上早他妈点过了, 哈哈哈.

----

#### **方法四(思路2)**

```html
<a id="test" href="https://www.baidu.com">test</a>
```

```javascript
const test = document.querySelector("#test");
test.onclick(function(e) {
    e.preventDefault();
})
```

以上例子通过取消点击事件的默认行为来禁止跳转. (行为艺术很牛逼么? 说禁你就禁你, 怕不怕?)

----

**参考:**

+ [a标签加入单击事件 屏蔽href跳转页面](https://zjf201172653.iteye.com/blog/1936591)
+ [a 标签的 href 属性和 onclick 事件](http://www.xinxiaoyang.com/programming/2017-03-06-a-tag-with-onclick-event/)
+ [MDN - void 运算符](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/void)
+ JavaScript DOM编程艺术 - 章节4.3 p57 (事件处理函数)














