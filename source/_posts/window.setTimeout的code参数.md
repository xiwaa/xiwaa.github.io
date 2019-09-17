---
title: 关于setTimeout中code参数、附加参数和匿名函数的使用
date: 2019-08-11 16:34:08
tags: 
- js
categories: Programming
---

### `setTimeout()`的定义("废话")

`window.setTimeout()`方法设置一个定时器, 该定时器在定时器到期后执行指定的一段代码.

**语法**

> let timeoutID = scope.setTimeout(function, [, delay, param1, param2, ...]);
>
> let  timeoutID = scope.setTimeout(function, [, delay]);
>
> **let timeoutID = scope.setTimeout(code, [, delay])**

**参数**

**function:**

function是你想要到期时间(delay 毫秒)之后执行的函数.

**code:** (可选)

这是一个可选语法, 你可以使用字符串字面量而不是function, 在delay指定毫秒之后编译和执行字符串(使用该语法是**不推荐**的, 原因和`eval()`一样, 有安全风险.)

**delay:** (可选)

延迟的毫秒数, 函数的调用会在该延迟之后发生. 如果省略该参数, delay默认取0, 意味着"马上"执行, 或者尽快执行. 不管是哪种情况, 实际的延迟时间可能回避期待的(delay毫秒数)值长. 原因请查看 [Reasons for delays longer than specified](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/setTimeout#Reasons_for_delays_longer_than_specified).

**附加参数:**

**param1, ... , paramN** (可选) [**这玩意儿很有用哟**]

附加参数, 一旦计时器到期, 他们会作为参数传递给`function`.

> **备注**：需要注意的是，IE9 及更早的 IE 浏览器不支持向回调函数传递额外参数(本文第二段第1种语法).

**返回值**

返回值是一个正整数, 表示定时器的编号. 这个值可以传递给`clearTimeout()`来取消该定时器.

需要注意的是`setTimeout()`和`setInterval()`共有一个编号池, 技术上讲, `clearTimeout()`和`clearInterval()`可以互换. 但是, 为了避免混淆, 不要混用取消定时函数.

在同一个对象上(一个window或worker), `setTimetout()`或者`setInterval()`在后续的调用不会重用同一个定时器编号. 但是不同的对象使用独立的编号池.

--------

### `code`参数、附加参数和匿名函数

为什么要写这篇幼儿园作文呢? 因为看书的时候发现一个以前不知道的地方--`setTimeout()`可以接受一个`code`参数, 也就是一个字符串. 当计时器到期后解析执行这个字符串里面的内容. 

我竟然以为只能传函数呢,  啊, 我湿了, 流下失败的泪水, 我是傻X, 我在浅色床单哭泣, 可恶, 坐Uber, 去北极, 自杀吧... 下面放在书上看到的代码:

```javascript
// 一个移动DOM元素的函数, 通过修改绝对定位元素的left和top属性值实现. 需要的参数是: 元素ID, 最终水平位置(left), 最终垂直位置(top), 时间间隔.
// 通过递归的方式来用setTimeout实现了setInterval的效果, 这就是个例子, 实际情况肯定直接上setInterval咯.
function moveElement(elementID, finalX, finalY, interval) {
    if (!document.getElementById) {return false;}
    if (!document.getElementById(elementID)) {return false;}
    let elem = document.getElementById(elementID);
    console.log(elem);
    let posX = parseInt(elem.style.left);
    let posY = parseInt(elem.style.top);
    if (posX === finalX && posY === finalY) {return true;}

    if (posX < finalX) {
        posX++;
    }
    if (posX > finalX) {
        posX--;
    }

    if (posY < finalY) {
        posY++;
    }
    if (posY > finalY) {
        posY--;
    }
    elem.style.left = posX + "px";
    elem.style.top = posY + "px";

    // 注意下面这行, 拼接了一个字符串作为code参数
    let repeat = `moveElement("${elementID}", ${finalX}, ${finalY}, ${interval})`;
    let movement = setTimeout(repeat, interval);
}
```

以上代码展示了`setTimeout`的`code`参数的用法,  虽然有点骚, 但是这样用是不安全的. 那么咋整? 可以用匿名函数呀! 上面代码最后两行可以这样写:

```javascript
let movement = setTimeout(function() {
    moveElement(elementID, finalX, finalY, interval);
}, interval);
```

看官大老爷可能要说了:"你他喵傻X, `setTimeout()`支持附加参数, 附加参数会在定时器到期时作为参数传入`function`参数代表的函数里面, 你他喵倒是用啊, 多打字不累的么?" 好, 咱们用:

```javascript
let movement = setTimeout(moveElement, interval, elementID, finalX, finalY, interval);
```

现在简洁清爽多了! 可是IE不兼容啊, 哈哈哈. 所以不考虑兼容IE的话`附加参数`爽歪歪, 兼容IE的话就只好当个老实人, 匿名函数用起来.

--------

**参考:**

+ [MDN \- window.setTimout()](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/setTimeout)

+ [stackoverflow - How can I pass a parameter to a setTimeout() callback?](https://stackoverflow.com/questions/1190642/how-can-i-pass-a-parameter-to-a-settimeout-callback)