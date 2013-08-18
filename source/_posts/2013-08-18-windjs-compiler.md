---
published: 
  - true
  - "true"
layout: post
title: JIT compiler in windjs
comments: "true"
categories: nodejs
---

javascript是一个神奇的语言，在“简单”的语法规则背后可以作出许多匪夷所思的事情，例如[windjs](windjs.org)中的compile，某种程度上实现了在运行时对函数进行重新编译，甚至重载了javascript最基本的关键字`if`, `for`等。

且看一个延时输出fibonacci数列的例子

	var fibo = eval(Wind.compile('async', function(){
    	var a = 1, b = 1;
    	console.log(a);
    	$await(Wind.Async.sleep(1000));
    	console.log(b);
    	while (true){
        	$await(Wind.Async.sleep(1000));
        	var c = a + b;
        	console.log(c);
        	a = b;
        	b = c;
    	}   
	}));

这段代码想要表达的就是忽略掉中间那堆`eval(Wind.compile('async', ...))`之后的内容，即定义一个函数。

	var fibo = function(n) {
		...
	}

由于这个函数体内需要用到一些异步返回的操作`sleep()`，没有被javascript直接支持，作者想到的办法是重新用`async`编译这个函数。阅读windjs的代码，可以知道这个compiler实现的基本步骤是。

1. 把函数转为string
2. 用自己编写的解析器将这个string翻译为string形式的新的函数代码
3. 用eval的方式执行翻译后函数代码，生成异步执行的任务对象

```
// Compiled: 
/* async << function () { */     (function () {
                                     var _builder_$0 = Wind.builders["async"];
                                     return _builder_$0.Start(this,
                                         _builder_$0.Delay(function () {
/*     var a = 1, b = 1; */                  var a = 1, b = 1;
/*     console.log(a); */                    console.log(a);
/*     $await(Wind.Async.sleep(1000)); */    return _builder_$0.Bind(Wind.Async.sleep(1000), function () {
/*     console.log(b); */                        console.log(b);
                                                 return _builder_$0.While(function () {
/*     while (true) { */                             return true;
                                                 },
                                                     _builder_$0.Delay(function () {
/*         $await(Wind.Async.sleep(1000)); */            return _builder_$0.Bind(Wind.Async.sleep(1000), function () {
/*         var c = a + b; */                                 var c = a + b;
/*         console.log(c); */                                console.log(c);
/*         a = b; */                                         a = b;
/*         b = c; */                                         b = c;
                                                             return _builder_$0.Normal();
                                                         });
                                                     })
/*     } */                                      );
                                             });
                                         })
                                     );
/* } */                          })
//@ sourceURL=wind/0_anonymous.js
```

比较原始代码和翻译后的代码，可以看出他们的可执行部分是一模一样的，有区别的部分是`flow control`的关键词`while`和特殊指令`$await`，并且函数的执行被包装到`async`这个`builder`中。

我们暂时不去深入`windjs`如何通过这种方式实现异步执行，从上面的基本分析中我们可以推断，`windjs`可以并且只能编译一个语法合理的javascript函数，它并不能扩展javascript的关键字，只能以伪函数的方式实现。例如你是找不到一个叫`$await`的函数的定义的。其次，虽然这个函数的语法是遵从javascript的，但其行为却不保证相同，因为`windjs`内部对`if`, `while`这些做了重新解释，还未确知是否对某些形式的表达式会有兼容性问题。

推荐阅读：[专访Wind.js作者老赵（上）](http://windjs.org/cn/blog/2012/07/infoq-interview-windjs-author-1/)。
