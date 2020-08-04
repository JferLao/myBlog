# this、apply、call、bind详谈

作为一个经典的问题，几乎每个面试都会涉及到关于this的问题，我也对这个问题产生过许多疑问。本文从用法比较和底层的源码实现来谈谈this的理解。

## 理解this指向
在《JavaScript高级程序设计》一书有提到过：this对象是在运行时基于函数的执行环境绑定的。在浏览器环境下全局函数中this等同于window，当函数作为某个方法调用时，this等于那个对象。匿名函数具有全局性因此其this对象通常指向window

这段话其实很容易理解，但是很多人却依然分不清this指向。我曾在一篇文章看到一句话：**this永远指向最后调用它的那个对象**。这句话可以更深刻的理解this的指向，那么下面通过几个例子来分析这句话的含义。
```
例子1:
var name = "window"
function fun() {
    var name = "fun"
    console.log(`this为:${this},this.name为:${this.name}`);
    //this为:[object Window],this.name为:window
}
fun()
console.log(`全局this为:${this}`);  //全局this为:[object Window]
```
这个例子很容易明白,因为在全局环境中this对象指向是window,那么在调用函数fun()实际上也是在全局环境对象调用func(),就是window对象调用了fun函数,
所以this指向最后调用它的对象,就是window,所以输出的结果也为window

```
例子2:
var name = "window"

var obj = {
    name: 'obj',
    func: function() {
        console.log(`输出的this.name为${this.name}`);
    }
}
console.log(this.name); //window
obj.func() //输出的this.name为obj
window.obj.func()//输出的this.name为obj
```

上面的例子2就更能解释**this永远指向最后调用它的那个对象**这句话了,全局环境this指向的就是window,func()最后是被obj调用了,所以此时的this指向obj,this.name就为'obj'，
假如在obj对象里面没有定义name这个属性，this.name返回就会为undefined。最后的window.obj.func()和obj.func()在非严格模式下其实是一致的。


然后我们再来看一个更特别的例子:
```
例子3:
var name = "window"
var obj = {
    name: 'obj',
    func: function() {
        console.log(`输出的this.name为${this.name}`);
    }
}
var f = obj.func

// var f = function() {
//     console.log(`输出的this.name为${this.name}`);
// }
f() //输出的this.name为window

```
为什么结果this会指向window呢?还是**this永远指向最后调用它的那个对象**这句话能解释清楚。obj对象将方法func赋值给了变量f，但是很重要的一点是，此时没有调用func(),所以此时this指向就肯定不为obj,就可以把赋值这一步看做下面注释内容的那一步。最后是window对象调用了这个方法，this就是指向了window。

通过上面3个例子,可以更好理解this的指向:**this永远指向最后调用它的那个对象**,通过这句话能够解决出大部分的this指向问题。

## 改变this指向
上面一段解释清楚了this的指向，那么在这一段我将解释如何改变this的指向。

通常改变this指向的方法有
+ new构造函数创建实例改变this指向
+ 使用apply、call、bind改变
+ 使用ES6的箭头函数
+ 在函数内使用_this=this

关于new操作符创建实例改变this指向,我在我的另一篇文章[JavaScript的面向对象编程](https://github.com/JferLao/blog/blob/master/%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E7%BC%96%E7%A8%8B.md)一文中有对new操作符进行的操作进行分析,其改变this的指向实则也是依靠call、apply等方法。所以在本文中对new操作符不过多分析，有兴趣的朋友可以到JavaScript面向对象编程一文中了解。

下面就来讲讲关于**apply、call、bind**是如何改变this的指向的

首先先谈谈apply和call，因为两者基本类似，实现的原理也基本类似，区别也仅仅是因为传参的不同而已。

### apply()和call()
apply方法传入两个参数,一个是this指向的对象,另一个是作为函数参数所组成的数组
> 用法:func.apply(this指向的对象, ['参数数组']); 
> 用法:func.call(this指向的对象,参数1,参数2,参数3...)

call方法第一个参数也是this指向，后面传入的是一个参数列表
```
例子4:
var obj = {
    name: 'Tom'
}

var name = 'Lisa'
function say(s1, s2) {
    console.log(`${this.name} 说${s1},${s2}`);
}
say('hello', 'world') //Lisa 说hello,world
say.apply(obj, ['bye', 'bye']) //Tom 说bye,bye
say.call(obj, 'hello', 'friend')    //Tom 说hello,friend
```

上面的例子4是apply()平常的用法,可以看到say()方法调用时在全局环境中,所以this指向也是指向全局window。当使用apply()改变this指向obj，这个时候this.name则指向了obj.name。假如在apply中不传入参数，相当于s1，s2都为undefined

而call()的用法,和apply()只是区别于传参的不同,实际改变this指向的效果是一样的。

那么调用call和apply实则做了什么呢？

#### 手写apply()
```
function getSymbol(obj) {
    // 确定返回值是独一无二的
    var key = '00' + Math.random()
        // 如果已存在则自递归调用函数
    if (obj.hasOwnProperty(key)) {
        arguments.callee(obj)
    } else {
        return key
    }
}
Function.prototype.myApply = function(that, params) {
    that = that || window //确保有没有传入指向this的对象
    if (!params) {
        params = []
    }
    var fn = getSymbol(that)    
    that[fn] = this    
    var result = that[fn](...params)    //执行这个函数
    delete that[fn]         //删除fn这个属性,因为实际上这个属性不存在
    return result           //返回执行函数的结果
}
// 测试
var obj = {
    name: 'Tom'
}
var name = 'Lisa'
function say(s1, s2) {
    console.log(`${this.name} 说${s1},${s2}`);
}
say.myApply(obj,['bye', 'bye']) //Tom 说bye,bye
```

上面有一步that[fn]=this,相当于obj.fn=this,因为看做say调用myApply(),所以此时的myApply()内部的this就是say对象,也可以看成obj={fn:say},另外一方面对于say方法内部this就指向了obj。定义的result用来保存执行say方法的结果，最后把这个结果返回回去。
          这个时候say方法中的this就完成了重新指向了

#### 手写call()
call其实和apply很相似,只是基于传入的参数不同罢了,稍微修改下即可.
```
function getSymbol(obj) {
    // 确定返回值是独一无二的
    var key = '00' + Math.random()
        // 如果已存在则自递归调用函数
    if (obj.hasOwnProperty(key)) {
        arguments.callee(obj)
    } else {
        return key
    }
}

Function.prototype.myCall = function(that, ...params) {
    that = that || window //确保有没有传入指向this的对象
    var fn = getSymbol(that)
    that[fn] = this
    var result = that[fn](...params) //执行这个函数
    delete that[fn] //删除fn这个属性,因为实际上这个属性不存在
    return result //返回执行函数的结果
}

// 测试
var obj = {
    name: 'Tom'
}
var name = 'Lisa'

function say(s1, s2) {
    console.log(`${this.name} 说${s1},${s2}`);
}
say.myCall(obj, 'hello', 'world') //Tom 说bye,bye
```

两个方法十分类似,但其实在处理参数时我使用了ES6的rest参数其实不是最好的方法。但是鉴于参数arguments是类数组无法使用数组的方法，并且这个是实现call()和apply()的方法,使用Array.prototype.slice.call(arguments,1)不太恰当。最好的方法就是把arguments转换为数组再操作。

当然对于call()和apply()除了改变this指向,其实它还有其他用法
1. 借用别的对象的方法(继承)
```
例子5:
var Person1  = function () {
    this.name = 'linxin';
}
var Person2 = function () {
    this.getname = function () {
        console.log(this.name);
    }
    Person1.call(this);
}
var person = new Person2();
person.getname();       // linxin
```

2. 调用函数
```
例子6:
function func() {
    console.log('linxin');
}
func.call();            // linxin
```


### bind()
在 EcmaScript5 中扩展了叫 bind 的方法，在低版本的 IE 中不兼容。它和 call 很相似，接受的参数有两部分，第一个参数是指向this的对象，第二部分参数是个列表，可以接受多个参数。

但有一点非常不同的是bind()方法不会立即执行,而是返回一个改变this之后的函数,而原来函数中的this没有被改变
```
例子7:
var obj = {
    name: 'linxin'
}
function func() {
    console.log(this.name);
}
var func1 = func.bind(obj);
func1();                        // linxin
```

#### 手写bind()
```
Function.prototype.myBind = function() {
    var self = this // 保存原函数,例子里的func
    context = Array.prototype.shift.call(arguments) // 保存需要绑定的this上下文
    args = Array.prototype.slice.call(arguments) // 剩余的参数转为数组
    return function() { // 返回一个新函数
        self.apply(context, Array.prototype.concat.call(args, Array.prototype.slice.call(arguments)));
    }
}

var obj = {
	name: 'Tom'
}
function say(s1, s2) {
	console.log(`${this.name} 说${s1},${s2}`);
}
say.myBind(obj, 'a', 'b', 'c')()		//Tom 说a,b
```

上面实现的方式其实很简单,就是获取参数中然后返回一个方法调用call或者apply

### 使用ES6的箭头函数
ES6的箭头函数出现其实是为了解决ES5中关于this的坑的。**箭头函数的this始终指向函数定义时的this，而非执行时。**，箭头函数需要记着这句话：“箭头函数中没有 this 绑定，必须通过查找作用域链来决定其值，如果箭头函数被非箭头函数包含，则 this 绑定的是最近一层非箭头函数的 this，否则，this 为 undefined”,下面这个例子可以很清晰解答这个问题
```
例子8:
var name = "window";
var a = {
    name: "object",

    func1: function() {
        console.log(this.name)	//'object'
    },
    func2: function() {
        setTimeout(function() {
            console.log(this); //window
        }, 100);
        setTimeout(() => {
            console.log(this); //a
        })
    }
};
a.func1()
a.func2() 
```
调用第一个func1很简单,因为a调用的所以this指向a,第二个就产生差异了。理想情况因为是a调用func2所以this理想情况是指向a的。但是setTimeout可以看做特殊
情况，其实际上是全局对象即window对象调用的，所以产生误差this指向了window。这种情况在使用了箭头函数内沿着作用域链寻找非箭头函数，此时找到应该为func2，那因为func2由a调用，所以this指向了a。



### 在函数内部使用 _this = this
使用这种方法可以更好的减少错误,只需要将调用函数的对象保存在变量_this之中,然后再函数中使用_this就不会发生改变了
```
var name = "windowsName";
var a = {
    name: "Cherry",
    func1: function() {
        console.log(this.name)
    },
    func2: function() {
        let _this = this
        setTimeout(function() {
            console.log(_this); //a
        }, 100);
        setTimeout(() => {
            console.log(_this); //a
        })
    }
};
a.func2()
```


通过上面一篇文章分享我对this的一些理解。这些理解基于在书籍和网上资料总结得出一些看法，可能还存在偏差所以也希望大家相互学习过程中指出我的错误，给我一些不错的建议。当然因为我
开始写博客不久，一些看法可能前面很多人总结过了，而且文采还有逻辑也没有那么好，文章质量没有那么好。但我也希望能收获大家一些正面的鼓励，在摸索中成长！