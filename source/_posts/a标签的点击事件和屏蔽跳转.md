---
title: a标签的点击事件和屏蔽跳转
date: 2019-08-07 10:14:03
tags: 
- html
- js
categories: Programming
---

### **常用的屏蔽href跳转的方法**

不让跳, 一般有两个思路:

1. 第一个思路就是把href干掉, 没有有效地址看你还往哪跳; 
2. 第二个思路就是即使href地址是有效的我也无所谓, 直接把默认跳转这个行为艺术给禁咯.

个人认为第一种思路是简单粗暴的, 是不可取的, 第二种才是正确的途径. 我们应当保证HTML的**语义化**和**可访问性**, a标签就是链接嘛, 哪有链接没有有效地址的道理.(有时候有的人的存在把a当按钮用的情况, 是错误滴),  既然没地址那就说明不该用这个标签. 如果用户禁止了浏览器的JavaScript怎么办?  那就啥也看不见了?  所以第二种思路从事正确的, 即使JavaScript不起作用了, 效果差点,  至少能知道是个啥东西嘛.

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

上面两种方法的href地址都是没有意义的, 如果是一个包含有意义的href地址,并且onclick之后禁止跳转到href地址的a标签呢? 

我们需要对事件处理函数的工作机制有一定的了解: 在给某一个元素添加事件处理函数后, 一旦发生预定事件, 相应的 `JavaScript` 代码就会得到执行. 那些 `JavaScript` 代码可以返回一个结果, 而这个结果将被传递给那个事件处理函数. 例如, 我们可以给某个链接添加一个 `onclick` 事件处理函数, 并让这个处理函数触发的代码返回布尔值 `true` 或 `false`. 这样一来, 当这个链接被点击时, 如果那段 `JavaScript` 代码返回给 `onclick` 事件处理函数的值是 `true`, `onclick` 事件处理函数将认为"这个链接被点击了" 然后默认跳转; 反之, 如果那段 `JavaScript` 代码返回给 `onclick` 事件处理函数的值是 `false`, `onclick`事件处理函数将认为"这个链接没有被点击" 也就不会跳转咯.


```html
<a href="https://www.baidu.com" onclick="someFunction;return false;">test</a>
```
以上例子, 达到了a标签在包含有意义的href地址的情况下被点击之后执行相应函数却不跳转. 因为通过return false让它得到的反馈是自己没被点, 实际上点过了. 大傻X, 被骗了吧, 哈哈哈.

----

#### **方法四(思路2)**

```html
<a id="test" href="https://www.baidu.com">test</a>
```

```javascript
const test = document.querySelector("#test");
test.onclick(function(event) {
    event.preventDefault();
})
```

以上例子通过取消点击事件的默认行为来禁止跳转. 

----

**参考:**

+ [a标签加入单击事件 屏蔽href跳转页面](https://zjf201172653.iteye.com/blog/1936591)
+ [a 标签的 href 属性和 onclick 事件](http://www.xinxiaoyang.com/programming/2017-03-06-a-tag-with-onclick-event/)
+ [MDN - void 运算符](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/void)
+ JavaScript DOM编程艺术 - 章节4.3 p57 (事件处理函数)














