---
title: JavaScript中的this
date: 2019-08-05 11:45:35
tags: js
categories: Programming
---

###	一、理解常规函数中this

JavaScript 有一个特殊的关键字 `this`，它可以在方法中使用以指代当前对象。**执行上下文(execution context)**意味着函数是在这里调用的, 每个函数在运行的时候都有个指向执行上下文的东西, 它就是`this`. 要理解`this`, **我们只需要搞清楚函数是在什么时候什么地方如何被调用**的即可, 并不需要关注函数在哪里定义或声明.

​		`this`是使用[`call()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/call)方法调用函数时传递的第一个参数, 他可以在函数调用时修改, 在函数没有调用的时候, this的值是无法确定的.

​		如果没有使用过[`call()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/call)方法来调用函数的话, 上面的对于this的定义可能不太明白.那么我们需要先理解函数调用的两种方法:



####	(一)纯粹的函数调用

第一种方法最常见, 例子如下:

```javascript
function test(name) {
	console.log(name);
    console.log(this);
}
test("Jerry");
```

这种方法我发平常使用的最多,但是这种函数调用方法只是一种简写,它完整的写法是下面这样的:

```javascript
function test(name) {
    console.log(name);
    console.log(this);
}
test.call(undefined, "Tom");	
```

注意上面的`call`方法, **`call`方法接收的第一个参数就是this, 这里我们传了一个`undefined`**. 那么,依据定义,函数执行之后`console.log()`出来的会是`undefined`吗? 不是!

> 传入null或者undefined默认代表window对象

所以在这里调用test函数的是window, `this`就是指向的window全局对象. 执行上下文也就是全局执行上下文了. 



####	 (二)对象中函数的调用

例子:

```javascript
const obj = {
    name: "Jerry",
    greet: function() {
        console.log(this.name);
	}
}
obj.greet();	// 第一种调用方式
obj.greet.call(obj) 	//第二种调用方式
```

以上例子中的第一种调用方式实际上只是第二种方式的语法糖, 第二种才是完整的调用方法, **第二种方法厉害的地方就在于它可以手动指定this.**

手动指定`this`的例子:

```javascript
const obj = {
    name: "Jerry",
    greet: function() {
		console.log(this.name);
    }
}
obj.greet.call({name: "Trump"}); 	// 打出来是 Trump
```

上面例子`call`方法调用函数时传入的是一个对象, 这个对象就是手动指定的`this`, 因此`greet()`行数中`console.log(this.name)`打印出来的就是Trump了. 这个例子也验证了文章第一段所说的:

> 我们只需要搞清楚函数是在什么时候什么地方如何被调用的即可, 并不需要关注函数在哪里定义或声明.



####	(三)构造函数中的this

构造函数中的`this`有一点特殊, 每个构造函数在new之后都会返回一个实例对象,这个对象就是`this`,也就是执行上下文.

例子:

```javascript
function Test() {
	this.name = "Tom";
}
let p = new Test();
console.log(typeof p); 	// object
console.log(p.name); 	// Tom
```



#### 	(四)window.setTimeout() 和window.setInterval()中函数的调用

他们两个函数中的`this`有些特殊,里面的`this`默认是`window`对象.



***简单总结一下：函数完整的调用方法是使用call方法，包括`test.call(context, name)`和`obj.greet.call(context,name)`，这里的context就是函数调用时的上下文，也就是`this`，只不过这个this是可以通过call方法来修改的；构造函数稍微特殊一点，它的`this`直接指向new之后返回的对象；`window.setTimeout()`和`window.setInterval()`默认的是`this`是window对象。***



### 二、理解箭头函数中的this

**箭头函数表达式**的语法比[函数表达式](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/function)更简洁，并且没有自己的[this](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/this)，[arguments](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/arguments)，[super](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/super)或 [new.target](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/new.target)。这些函数表达式更适用于那些本来需要匿名函数的地方，并且它们不能用作构造函数。



####	(一)箭头函数不绑定层this

在箭头函数出现之前，每个新定义的函数都有它自己的 `this`值（在构造函数的情况下是一个新对象，在严格模式的函数调用中为 undefined，如果该函数被作为“对象方法”调用则为基础对象等）。`This`被证明是令人厌烦的面向对象风格的编程。**箭头函数不会创建自己的this,它只会从自己的作用域链的上一层继承this。**

不使用箭头函数的例子:

```javascript
const obj = {
    a: function() {
        console.log(this);
    }
}
obj.a();	// 打印出的是obj对象
```

使用箭头函数的例子：

```javascript
const obj ={
    a: () => console.log(this)
}
obj.a();	// 打印出来的是window
```

上面这个例子中, 从作用域链上层(这里的上层也就是window)继承了this

再来个例子:

```javascript
function test() {
                const myObj = {
                    name: "hejian",
                    greeting: () => console.log(this)
                }
                return myObj.greeting();
            }
test();	// 打印出来的是window
```

以上这个例子, 如果箭头函数像普通函数一样默认绑定`this`的话, 它的this应该指向`myObj`. 但是由于 **箭头函数不会创建自己的this,它只会从自己的作用域链的上一层继承`this`**,  所以这里就向作用域链的上级`test()`这一层作用域中查询,test()是个普通函数默认有this, 那么查询到这里就结束了. 那么这个this指向哪里呢? 答案就是指向window! 为了方便理解最后一行的text()可以用这样写: `test.call(undefined)`

#### (二)不能用call方法修改箭头函数的this

由于 箭头函数没有自己的this指针，通过 [`call()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/call) *或* [`apply()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/apply) 方法调用一个函数时，只能传递参数（不能绑定this---译者注），他们的第一个参数会被忽略。（这种现象对于bind方法同样成立---译者注）

```javascript
let adder = {
  base : 1,
  add : function(a) {
    var f = v => v + this.base;
    return f(a);
  },
  addThruCall: function(a) {
    var f = v => v + this.base;
    var b = {
      base : 2
    };        
    return f.call(b, a);
  }
};

console.log(adder.add(1));         // 输出 2
console.log(adder.addThruCall(1)); // 仍然输出 2（而不是3 ——译者注）
```

如上所示, 企图用call方法作用与箭头函数来手动指定this是行不通的.





**参考:**

1. [掘金 \- JS中的箭头函数与this](https://juejin.im/post/5aa1eb056fb9a028b77a66fd)
2. [JavaScript - all about this keyword](https://codeburst.io/all-about-this-and-new-keywords-in-javascript-38039f71780c)
3. [MDN \- 使用对象](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Functions)
4. [MDN \- 使用函数](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Working_with_Objects)

