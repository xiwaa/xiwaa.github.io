---
title: window.onload和$(document).ready
date: 2019-08-08 09:19:14
tags:
- js
- DOM
categories: Programming
---

###	二者的区别

首先需要明确window.onload属性是原生js的, 而$(document).ready是属于jQuery的, 要用后者需要先引入jQuery.

二者的区别在于$(document).ready事件发生在**DOM已经完全加载(也就是DOM tree已经构建好), 做好了给JacaScript操作它们的准备了**之后, 而window.onload事件发生在整个窗口页面和资源完全加载之后, 它不仅仅需要DOM加载完成, 还需要图片、iframe、CSS文件、JS等这些内容都加载好.  所以window.onload()事件发生的更晚是显而易见的.

来瞄一眼jQuery的文档是如何描述的:

> In most cases, the script can be run as soon as the DOM hierarchy has been fully constructed.
>
> A page can't be manipulated safely until the document is "ready." jQuery detects this state of readiness for you. Code included inside `$( document ).ready()` will only run once the page Document Object Model (DOM) is ready for JavaScript code to execute. Code included inside `$( window ).on( "load", function() { ... })` will run once the entire page (images or iframes), not just the DOM, is ready.

二者还有个 区别是window.onload只能用一次而$(document).ready可以多次使用(例如有多个js库时可能需要多次用`ready`从而做各自特定需要的操作). window.onload这种页面加载事件, 一个页面就能用一次, 很容易理解嘛, 如果硬要多次使用的话那就会造成后面的覆盖掉前面的, 就跟变量重新赋值似的.

``` 
window.onload = function() {
    console.log("1");
}

window.onload = function() {
	console.log("2");
}
// 最终的结果就是后一个覆盖掉前一个, 打印出2.
```

正常的做法是把函数加到一个window.onload里面:

```javascript
window.onload = function() {
    func1();
    func2();
    func3();
}
```

上面这样挺好了, 可是如果函数太多的话也挺难看的, 也不够灵活. 所以可以如下定义一个addLoadEvent函数:

```javascript
function addLoadEvent(func) {
    const oldOnload = window.onload;
    if (typeof	window.onload != "function") {
        window.onload = func;
    } else {
        window.onload = function() {
			lodOnload();
            func();
        }
    }
}
```

以上代码做了如下事情:

1. 把现有的window.onload事件处理函数的值存入变量oldonload。
2. 如果在这个处理函数上还没有绑定的任何函数，则使用window.οnlοad=func直接调用，不需要加括号

3. 如果在这个处理函数上已经绑定了一些函数，就把新的函数追加到现在指令的末尾。

现在, 如果某个函数需要在window.onload事件触发的时候立即执行的话, 我们只需要像下面这样:

addLoadEvent(func1);

addLoadEvent(func2);

你爱加多少加多少, 爱在哪儿加在哪儿加, 也很清晰明白.

--------

###	 DOMContentLoaded事件



当初始的 **HTML** 文档被完全加载和解析完成之后，**DOMContentLoaded** 事件被触发，而无需等待样式表、图像和子框架的完成加载。另一个不同的事件 `load `应该仅用于检测一个完全加载的页面。 在使用 `DOMContentLoaded` 更加合适的情况下使用 [`load`](https://developer.mozilla.org/en-US/docs/Mozilla_event_reference/load) 是一个令人难以置信的流行的错误, 这种错误很明显会使运行速度变慢, 所以要谨慎。

注意：**DOMContentLoaded** 事件必须等待其所属script之前的样式表加载解析完成才会触发。

```javascript
document.addEventListener('DOMContentLoaded',function(){
    //需要执行的代码；
},false)
```

DOMContentLonded效果跟$(document).ready相似, 但是不同浏览器有兼容问题. 例如IE低版本浏览器就没有这个事件，而所有浏览器均支持的onreadystatechange 事件能辨识readyState 属性的改变，在readyState 属性为complete时，DOM树构建完毕，这样就可以封装一个兼容低版本的函数.

```javascript
function DOMReady(func){
    if(document.addEventListener){   //高级浏览器支持      		  
        document.addEventListener('DOMContentLoaded',function(){
            func && func();
        },false)
    }else{                    //低版本浏览器使用(IE)
        document.attachEvent("onreadychange", function(){
            if(document.readyState == 'complete'){
                func && func();
            }
        })
    }
}
```

说明: IE 没有document.addEventListener()方法, 所以第一个if排除IE了;  else这里就是针对IE了, 由于IE没有addEventListener所以用的attachEvent绑定事件., 真他喵蛋疼.

--------

**参考:**

+ [ $(document).ready() vs window.onload() vs $(window).load()](https://www.techiediaries.com/javascript/document-ready-vs-window-onload-vs-window-load/)
+ [stackoverflow \- Is $(document).ready() also CSS ready?](https://stackoverflow.com/questions/1324568/is-document-ready-also-css-ready)
+ [MDN \- DOMContentLoaded](https://developer.mozilla.org/zh-CN/docs/Web/Events/DOMContentLoaded)
+ [简书 \- DOMContentLoaded](https://www.jianshu.com/p/9997836f2351)
+ [关于JS的window.onload()方法](https://blog.csdn.net/baidu_25343343/article/details/72639093)
+ [What, exactly, is the DOM?](https://bitsofco.de/what-exactly-is-the-dom/)