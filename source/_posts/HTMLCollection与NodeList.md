---
title: HTMLCollection与NodeList
tags: 
- html
- js
categories: Programming
---

###	**一、HTMLCollection**

HTMLCollection对象是一个包含了元素（元素顺序为文档流中的顺序）的通用集合（generic collection）, 它提供了用来从该集合中选择元素的方法和属性。

**HTML DOM 中的 `HTMLCollection` 是即时更新的（当其所包含的文档结构发生改变时, 它会自动更新, 所以遍历的时候需要注意.**

> *注意：由于历史原因（DOM4之前，实现该接口的集合只能包含 HTML 元素），该接口被称为`HTMLCollection`。*
>
> *DOM有个节点树的概念, 节点(node)有不同种类, 例如**元素节点**、文本节点、属性节点... HTMLCollection中包含的HTML元素实际上就是**元素节点***
>
> *nodeType一共有12种类: 元素节点 , 属性节点, 文本节点 , CDATA节点 , 实体引用名称节点, 实体名节点 , 处理指令节点 , 注释节点 , 文档节点 , 文档类型节点 , 文档片段节点, DTD声明节点*             

####	**属性:**	

+ HTMLCollection.length	

​		返回集合当中子元素的数目.

*使用像element.getElementsByTagName()等方式获取元素的时候返回的就是HTMLCollection对象而不是数组, 所以无法使用forEach这样的数组方法遍历, 不过使用for循环遍历是可以的. 不过可以使用Array.from(someHTMLCollection)这样的方式将其转换成数组.*

#### **方法:**

- HTMLCollection.item()	通过索引返回其中的元素

+ HMLCollection.namedItem()

​		根据 `Id `返回指定节点，或者在不存在id的情况下根据字符串所表示的 `name` 属性来匹配。根据 `name `匹配只能作为最后的依赖，并且只有当被引用的元素支持 `name` 属性时才能被匹配。如果不存在符合给定 `name `的节点，则返回 `null`。

--------

###	**二、NodeList**

`NodeList`对象是一个节点的集合, 是由`Node.childNodes`和`document.querySelectorAll()`返回的. NodeList是一个类数组(`Array-like`)的对象.

在一些情况下, `NodeList`是一个实时的集合, 也就是说如果文档中的节点树发生变化, 已经存在的实时`NodeList`对象也会随之变化. 例如, `Node.childNodes`是实时的:

```javascript
const parent = document.getElementById("parent");
const chld_nodes = parent.childNodes;
console.log(child_nodes.length);	// 我们假设结果为2
parent.appendChild(document.createElement("div"));	
console.log(chid_nodes.length);		//结果变成3了
```

在其他情况下, `NodeList`是一个静态集合, 也就意味着随后对文档对象模型的任何改动都不会影响集合的内容. 比如`document.querySelectorAll()` 会返回一个静态的`NodeList`. 最好牢记这种不同, 尤其是在对`NodeList`进行遍历的时候.

#### **属性:**

+ NodeList.length()

#### **方法:**

- [`NodeList.item()`](https://developer.mozilla.org/zh-CN/docs/Web/API/NodeList/item)

- [`NodeList.entries()`](https://developer.mozilla.org/zh-CN/docs/Web/API/NodeList/entries)

    Returns an [`iterator`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Iteration_protocols), allowing code to go through all key/value pairs contained in the collection. (In this case, the keys are numbers starting from 0 and the values are nodes.)

- [`NodeList.forEach()`](https://developer.mozilla.org/zh-CN/docs/Web/API/NodeList/forEach)

    Executes a provided function once per `NodeList` element, passing the element as an argument to the function.

- [`NodeList.keys()`](https://developer.mozilla.org/zh-CN/docs/Web/API/NodeList/keys)

    Returns an [`iterator`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Iteration_protocols), allowing code to go through all the keys of the key/value pairs contained in the collection. (In this case, the keys are numbers starting from 0.)

- [`NodeList.values()`](https://developer.mozilla.org/zh-CN/docs/Web/API/NodeList/values)

    Returns an [`iterator`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Iteration_protocols) allowing code to go through all values (nodes) of the key/value pairs contained in the collection.
    
    

--------

### **总结:**

二者的主要区别:

+ `NodeList`中可包含所有12中节点类型, `HTMLCollection`中只能包含元素节点这一种.
+ `NodeList`是类数组对象, 拥有`forEach()`, `entries()`, `keys()`等方法而`HTMLCollection`没有.
+ `HTMLCollection`是实时的, 而`NodeList`既可以是实时(`node.childNodes`返回的是实时的)也可以是静态的(`node.querySelectorAll()`返回的是静态的).

另外提一下, 这里谈论的`NodeList`的静态集合跟使用`jQuery`获取元素是相似的, 都是获取了就不变了. 好像有点像`Linux`上做快照(`snapshot`), 就记录那一瞬间, 哈哈.

--------

### ***闲扯环节***

上面已经写得很清楚了, 记住区别就行. 下面介绍一下stackoverflow上一位答主判断`HTMLCollection`和`NodeList`什么时候静态什么时候实时的代码, 或许可以帮助理解.

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>isLive</title>
</head>
<body>
    <div class="c" name="myDiv">C1</div>
    <div class="c" name="myDiv">C2</div>
    <script>
        window.onload = function () {
            function isLive(collection) {
                if (collection.length < 1) {
                    return undefined; 
                }
                let body = document.getElementsByTagName('body')[0];
                let len1 = collection.length;
                let clone = collection.item(0).cloneNode();
                clone.style.display = "none";
                body.appendChild(clone);
                let len2 = collection.length;
                body.removeChild(clone);
                return len2 !== len1;
            }



            divs1 = document.getElementsByClassName('c');
            console.log("document.getElementsByClassName('c'): ",divs1.toString()); //"[object HTMLCollection]"


            divs2 = document.querySelectorAll('.c');
            console.log("document.querySelectorAll('.c'): ",divs2.toString()); //"[object NodeList]"

            divs3 = document.getElementsByName('myDiv');
            console.log("document.getElementsByName('myDiv'): ",divs3.toString()); //"[object NodeList"]

            console.log("isLive(divs1)",isLive(divs1)); //true
            console.log("isLive(divs2)",isLive(divs2)); //false
            console.log("isLive(divs3)",isLive(divs3)); //true
        }
    </script>
</body>
</html>
```

`isLive`函数做的事情就是: 先获取集合,  将其`lengh`赋值个`len1`, 然后从集合里面克隆一个元素并且添加到`body`下面, 接着将length赋值给`len2`, 然后为了不影响页面而删除添加的元素.如果是实时的那么集合的lenght肯定会跟着变化, 第二次赋值时的`lengh`就会和比第一次大1; 反之如果是静态的那么`length`根本不会有变化. 所以代码最后就比较了 `len1 `和 `len2 `相不相等, 做出了静态与实时的判断.

--------

**参考:**

+ [Understanding the difference between an HTMLCollection and a NodeList](https://teamtreehouse.com/community/understanding-the-difference-between-an-htmlcollection-and-a-nodelist)
+ [Difference between HTMLCollection, NodeLists, and arrays of objects](https://stackoverflow.com/questions/15763358/difference-between-htmlcollection-nodelists-and-arrays-of-objects)
+ [MDN \- HTMLCollection](https://developer.mozilla.org/en-US/docs/Web/API/HTMLCollection)
+ [MDN \- NodeList](https://developer.mozilla.org/en-US/docs/Web/API/NodeList)
+ [W3C \- Node Types](https://www.w3school.com.cn/jsref/prop_node_nodetype.asp)