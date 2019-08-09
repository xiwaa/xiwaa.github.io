---
title: window.getComputedStyle(可获取伪类元素样式)
date: 2019-08-09 17:05:57
tags: 
- js
- css
categories: Programming	
---

### 一、window.getComputedStyle 定义

**语法: window.getComputedStyle(element[, pseudoElt]);**

| Parameter       | Description                                         |
| :-------------- | :-------------------------------------------------- |
| *element*       | Required. The element to get the computed style for |
| *pseudoElement* | Optional. A pseudo-element to get                   |

```javascript
let elem1 = document.getElementById("elemId");
let style = window.getComputedStyle(elem1, null);

// 它等价于
// let style = document.defaultView.getComputedStyle(elem1, null);
```

一般来说直接`window.getComputedStyle`就可以了, 使用`document.defaultView.getComputedStyle`是为firefox3.6上访问子框架时做兼容, 保险起见用第二种吧.

`getComputedStyle`获取特定元素最终应用了**经过计算(computed)**的所有的CSS属性和属性的值, 它返回的是一个CSS样式申明对象 `object CSSStyleDeclaration` . 这个对象是只读的 .

**经过计算的样式(computed style)** 是应用了来自不同来源的样式之后(来源包括内联样式、内部样式、外部样式表、继承的样式、浏览器赋予的默认样式)最终呈现时那个值. 好像有点绕, 举个栗子: 一个p标签浏览器默认会给它一些样式, 例如padding、width、height之类, 我们可能用了内部样式表将它的height重新定义了, 然后我们可能又在外部样式表或者通过JavaScript把它的height给改了, 那么`经过计算的` height就是最终呈现出来的时候的那个值咯 . 前面那一通对height的操作就是**计算**咯.

--------

### 二、getComputedStyle VS element.style

1. `element.style`只能获取到在element元素中`style`属性里的样式(内联样式), 对于继承的或者浏览器默认赋予的样式这些是没有办法获取的, 而`getComputedStyle`就是全能型选手了, 从上面的语法部分也看到了, 它支持两个参数, 第一个很明显是要获取样式的元素, 第二个就厉害了可以获取伪类的样式. -- See my cow pussy(看我牛逼不)? 以前完全不知道伪类元素还可以获取样式, 长见识了.

获取伪类元素的样式:

```javascript
<style>
    h3::after {
        content: "rocks!";
    }
</style>

<h3>generated content</h3> 

<script>
    let h3 = document.querySelector('h3'), 
    result = document.defaultView.getComputedStyle(h3, '::after').getPropertyValue("content");
    console.log(`the generated content is: ${result}`); 
    // the generated content is: "rocks!"
</script>
```

`getComputedStyle.getPropertyValue()`方法用于获取特定属性的值,  参见上面第11行.

2. `getComputedStyle` 是只读的, 只能获取样式, 不能设置. `element.style`即可读也可写, 可攻可受、可进可退, 简直欢喜无量, 不可言说...

```javascript
let elem = document.getElementById("elem");
let elemHeight = elem.style("height"); // 获取样式
elem.style("height", "20px"); // 设置样式
```

### 三、getComputedStyle VS currentStyle

IE6-8不支持 `getComputedStyle`, 它们有个 `currentStyle` 属性, `element.currentStyle`返回元素当前应用的最终CSS属性值, 包含页面内部的style属性和外部引用的CSS文件. 除了不能获取伪类元素的样式, 其他跟getComputedStyle差不多. 为了兼容可如下操作(以获取height为例):

```javascript
element.currentStyle? element.currentSyle["height"]: document.defaultView.getComputedStyle(element, null).getProperty("height");
```

--------

**参考:**

+ [MDN \- Window.getComputedStyle()](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/getComputedStyle)
+ [MDN \- Examples of web and XML development using the DOM](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model/Examples)
+ [获取元素CSS值之getComputedStyle方法熟悉](https://www.zhangxinxu.com/wordpress/2012/05/getcomputedstyle-js-getpropertyvalue-currentstyle/)  
+ [JavaScript Window getComputedStyle() Method](https://www.w3schools.com/jsref/jsref_getcomputedstyle.asp)



