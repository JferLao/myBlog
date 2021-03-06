#### 前言
谈到JavaScript的内存机制,其实这不是前端开发者经常讨论到的一个问题。对于一些强类型的语言底层都会有内存管理接口,
而JavaScript这种弱类型语言却像“自动控制内存管理”一般。所以今天就从内存、JS内存空间和JS内存管理两方面来探讨一下，JavaScript的内存机制。

# 什么是内存
>在硬件层面上，计算机内存由大量的触发器缓存的。每个触发器包含几个晶体管，能够存储一位，单个触发器都可以通过唯一标识符寻址，因此我们可以读取和覆盖它们。因此，从概念上讲，可以把的整个计算机内存看作是一个可以读写的巨大数组。

作为人类，我们并不擅长用比特来思考和计算，所以我们把它们组织成更大的组，这些组一起可以用来表示数字。8位称为1字节。除了字节，还有字(有时是16位，有时是32位)。

很多东西都存储在内存中:

- 程序使用的所有变量和其他数据。
- 程序的代码，包括操作系统的代码。

JS有一套完整的内存管理机制，所以下面就来介绍一下关于JS的内存机制。

# JS的内存空间
在一般的定义中，JS的内存空间分为栈（stack)和堆(heap),当然在一些资料中还存在池(一般会被归到栈内),每个空间负责
存储不同的数据。其中栈存放变量、基本数据类型的数据，栈存放复杂对象、引用类型数据，池一般存放常量。这样的存储分配组成了JS的内存空间。


## 栈
> 栈的特点是"LIFO，即后进先出（Last in, first out）"。数据存储时只能从顶部逐个存入，取出时也需从顶部逐个取出。
所以任何不在栈顶的元素都无法访问。

用下面这张图来理解栈。栈中的数据，是按照一定顺序进入栈内的，因此想要获取某个数据，就得从栈顶开始一个接着一个取出，直到取出想要的数据。

![栈](https://static001.geekbang.org/resource/image/5e/05/5e2bb65019053abfd5e7710e41d1b405.png)
### 栈内的数据——基本数据类型
上面有提到过栈主要存储的是基本数据类型的数据。基本数据类型占用空间小，大小固定，通过暗值访问，属于被频繁使用的数据。存储在栈中操作会更加容易。

#### 变量类型和内存关系
基本数据类型（ES6之后新增Symbol）：
1. Sting
2. Number
3. Boolean
4. null
5. undefined
6. Symbol

一般对于基本数据类型的保存和操作我们通过下面几个图例来解释解释。

![栈内存](https://upload-images.jianshu.io/upload_images/599584-cce8e155e19593fb.png)
```
var a=20
var b='abc'
var c=true
var d={m:20}
```


对于上面这个图，变量a,b,c的值都是基本数据类型,而d属于对象是引用数据类型我们下一章节再谈。变量定义按照顺序,先进后出,最终得到实现栈。

#### 变量复制
对于基本数据类型来讲,**变量复制其实可以看做是直接拷贝复制变量具体值的过程**。
```
//代码
var a = 20;	//a=20
var b = a;	//b=a=20
b = 30;		//b=30 a=20
```

![变量复制](https://s2.51cto.com/oss/201809/26/8b0dc56f320de7674e6fe797df27acde.jpeg)


## 堆
> 堆的特点是"无序"的key-value"键值对"存储方式
堆就像是图书馆的书架一样，想要什么书就通过key（书名）找value（书）一样，这个例子十分贴切。并且堆的存取方式跟顺序没有关系，不局限出入口，只关注键值。


![堆](https://img2018.cnblogs.com/blog/1028513/201902/1028513-20190212172154517-1712159024.jpg)

### 堆内的数据——引用数据类型

#### 变量类型和内存关系
上文讲过，堆内一般存储的是引用数据类型的数据，这类数据占据空间大，并且大小不确定。如果存储在栈中，很大程度会影响程序运行的性能。
一般应用类型数据在**栈**中存储指针，指针指向堆中数据的起始地址。当需要这个值时，先在栈中找到变量名，然后通过存储在变量名具体值的指针在堆中地址寻找，直到找到目标地址。

引用数据类型:
1. Object
2. Function
3. Array
4. Date
5. RegExp

![栈内存](https://upload-images.jianshu.io/upload_images/599584-cce8e155e19593fb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
还是上面这个图我们来看下，其中d就是引用数据类型的变量名，在这个变量名的具体值实际上保存的一个指针。这个指针指向堆内存的具体地址，找到了{m:20}最后返回。

>因此当我们要访问堆内存中的引用数据类型时，实际上我们首先是从变量中获取了该对象的地址指针， 然后再从堆内存中取得我们需要的数据。

#### 变量复制
对于引用数据类型的复制就相对复杂过基本数据类型了,因为其复制的变量的具体值是一个**指针**。对于引用数据类型来讲，因为复制的
**被复制的变量和复制的变量都指向堆内的同一个地址**。当其中一个变量发生变化，其通过指针改变堆内地址的数据也会影响其他变量。
```
var m = { a: 10, b: 20 }		//m={ a: 10, b: 20 }
var n = m;						//m={ a: 10, b: 20 }  n={ a: 10, b: 20 }
n.a = 15;						//m={ a: 15, b: 20 }  n={ a: 15, b: 20 }
```

# JS的内存管理
在谈完js内存空间之后，我们应当讨论一下这两个内存空间存储的方式的优缺点。但在这之前我们先来了解一下JS的内存管理机制，通过了解JS的内存机制我们才能更好的认识栈和堆的优缺点。

## 内存生命周期
在所有的解释里都奉行:**无论使用哪种编程语言,内存的生命周期都是一样的:分配内存=>使用内存=>释放内存**，当然在不同生命周期内执行的内容也不同。
![内存生命周期](https://image.fundebug.com/2018-12-18-01.png)

- **分配内存**： 内存是由操作系统分配的，它允许您的程序使用它。在JS环境中（包括浏览器和nodeJS），声明变量、函数、对象时，系统会自动分配内存给我们
- **使用内存**： 使用分配到的内存进行读写操作，例如使用变量，调用函数等都是使用内存的操作。
- **释放内存**： 释放所有不再使用的内存,使之成为自由内存,并可以被重利用。当然在JS环境下有垃圾回收机制回收，开发者也可以主动回收不再使用的函数变量以及对象等。

对于分配内存和使用内存在上面的内容或多或少也有提到过了，下面就讲讲在JS内存管理中比较关键的释放内存这一个周期。

## 释放内存
对于释放内存来讲,这个生命周期在内存管理中是最重要的，但也是最困难的，因为你不确定何时不再需要分配的内存并且什么时候释放它。
所以在JS环境内嵌入了**垃圾自动回收机制**，跟踪内存分配和使用，以便找到不再使用的内存，然后释放它。

首先先了解下经常谈到的两个算法:标记清除和引用计数算法.

### 引用计数垃圾清除算法
>这是最简单的垃圾收集算法。如果没有指向对象的引用，则认为该对象是“垃圾可回收的”

下面用网上两张图来说明一下引用计数算法,首先是第一张图:

![引用计数](https://image.fundebug.com/2018-12-18-08.png)

当然看完第一图我觉得就可以很清楚的理解**没有被引用就会被清除**这个说法。当变量被引用时会将被引用次数保存起来，当被引用次数
变为零时就将其释放。这个时候资源就回释放然后把内存还给操作系统。

![引用计数2](https://image.fundebug.com/2018-12-18-09.png)

上面第二个图可以看到假如两个对象互相引用，形成循环。虽然函数执行后它们实际上是无用的，但是因为它们一直被引用，导致这个内存被一直占着无法释放，最后无法清除。
这个最典型的**循环引用**问题。当然在现代浏览器当中，常常会因为循环引用问题导致内存泄漏。也正是因为这个原因，产生了**标记清除算法**来解决这一问题。



### 标记-清除垃圾清除算法
>该算法能够判断出某个对象是否可以访问,对象是否可以获得，从而知道该对象是否有用。

根据这个算法，假设从一个根节点对象(在JavaScript内，根对象可以看做全局对象)开始，定期的发生过程。垃圾回收清除从根对象开始，
找出所有根对象引用的对象，再向下找出所有引用对象引用的对象，最终得到可以从根节点开始所有被用到的对象，和所有不被用到的对象。最后那些不被用到的对象就会被清除掉。

![标记清除](https://pic4.zhimg.com/v2-f79a0f0a6d3c485bce3768af596e7b9c_b.webp)

像上面图演示一样，从根对象找出所有用到的绿色的对象，找出所有不被用到的蓝色的对象，最后把这些蓝色对象清除掉即完成了一次清除过程。详细流程就如下面一样说明一般：

流程: 
1. 垃圾收集器构建一个“根”列表,用于保存引用的全局变量。在JavaScript中,“window”对象是一个可作为根节点的全局变量。
2. 然后，算法检查所有根及其子节点，并将它们标记为活动的(这意味着它们不是垃圾)。任何根不能到达的地方都将被标记为垃圾。
3. 最后，垃圾收集器释放所有未标记为活动的内存块，并将该内存返回给操作系统。

当然说到标记-清除算法解决了循环引用这个问题，其实下面的图也可以很直观看出来，因为从根对象开始，循环引用的对象根本不属于活跃的节点内，自然被清除了，就不会发生内存泄漏这一问题了。

![标记清除1](https://image.fundebug.com/2018-12-18-12.png)

****

因为JavaScript的数据是存储在堆和栈两种内存空间的,所以接下来当然需要讨论下栈中的垃圾数据”和“堆中的垃圾数据”是如何回收的。

### 调用栈中的数据是如何回收的

原始类型的数据被分配到栈中，引用类型的数据会被分配到堆中。还有一个**记录当前执行状态的指针（称为 ESP）**，
指向调用栈中函数的执行上下文，表示当前正在执行的函数。

当前函数执行完毕之后,ESP就睡下移到下一个函数的执行上下文,**这个下移操作就是销毁函数执行上下文的过程**。

![从栈中回收 执行上下文](https://static001.geekbang.org/resource/image/b8/f3/b899cb27c0d92c31f9377db59939aaf3.jpg)

从图中可以看出，当 showName 函数执行结束之后，ESP 向下移动到 foo 函数的执行上下文中，上面 showName 的执行上下文虽然保存在栈内存中，但是已经是无效内存了。
比如当 foo 函数再次调用另外一个函数时，这块内容会被直接覆盖掉，用来存放另外一个函数的执行上下文。

>JavaScript 引擎会通过向下移动 ESP 来销毁该函数保存在栈中的执行上下文。

### 堆中的数据是如何回收的

栈中通过ESP指针控制垃圾回收,那么堆中数据没那么容易控制，就需要**JavaScript的垃圾回收器了**,接下来就来通过 Chrome 的 JavaScript 引擎 V8 来分析下堆中的垃圾数据是如何回收的。

垃圾回收的策略都是建立在**代际假说**的基础之上的。

代际假说有以下两个特点：
- 第一个是大部分对象在内存中存在的时间很短，简单来说，就是很多对象一经分配内存，很快就变得不可访问；
- 第二个是不死的对象，会活得更久

V8基于这两个特点，把堆分为**新生代**和**老生代**两个区域，**新生代中存放的是生存时间短的对象，老生代中存放的生存时间久的对象**。

- 副垃圾回收器，主要负责新生代的垃圾回收，新生区通常只支持 1～8M 的容量
- 主垃圾回收器，主要负责老生代的垃圾回收。老生区支持的容量就大很多了

V8 把堆分成两个区域——新生代和老生代，并分别使用两个不同的垃圾回收器。其实**不论什么类型的垃圾回收器，它们都有一套共同的执行流程**。
1. 标记空间中**活动对象**和**非活动对象**。所谓活动对象就是还在使用的对象，非活动对象就是可以进行垃圾回收的对象。
2. 回收**非活动对象**所占据的内存。其实就是在所有的标记完成之后，统一清理内存中所有被标记为可回收的对象。
3. 内存整理,整理这些内存碎片(频繁回收对象后，内存中就会存在大量不连续空间)

那么接下来，我们就按照这个流程来分析新生代垃圾回收器（副垃圾回收器）和老生代垃圾回收器（主垃圾回收器）是如何处理垃圾回收的。

>垃圾收集算法主要依赖的是引用。在内存管理的环境中，一个对象如果有访问另一个对象的权限（隐式或者显式），叫做一个对象引用另一个对象。

#### 副垃圾回收器
副垃圾回收器主要负责新生区的垃圾回收。而通常情况下，大多数小的对象都会被分配到新生区，
所以说这个区域虽然不大，但是垃圾回收还是比较频繁的

![新生区要划分为对象区域和空闲区域](https://static001.geekbang.org/resource/image/4f/af/4f9310c7da631fa5a57f871099bfbeaf.png)

>新生代中用 Scavenge 算法来处理。所谓 Scavenge 算法，是把新生代空间对半划分为两个区域，一半是对象区域，一半是空闲区域，

1. 新加入的对象都会存放到对象区域，当对象区域快被写满时，就需要执行一次垃圾清理操作。
2. 在垃圾回收过程中，首先要对对象区域中的垃圾做标记；标记完成之后，就进入垃圾清理阶段
3. 副垃圾回收器会把这些**存活的对象复制到空闲区域**中，同时它还会把这些对象有序地排列起来，这个过程相当于完成了内存整理操作
4. 复制后空闲区域就没有内存碎片了。
5. 完成复制后，**对象区域**与**空闲区域**进行**角色翻转**，也就是原来的对象区域变成空闲区域，原来的空闲区域变成了对象区域
6. 这样就完成了垃圾对象的回收操作，同时这种角色翻转的操作还能让新生代中的这两块区域无限重复使用下去。


由于新生代中采用的 **Scavenge 算法**，所以每次执行清理操作时，都需要将存活的对象从对象区域复制到空闲区域。但复制操作需要时间成本，
如果新生区空间设置得太大了，那么每次清理的时间就会过久，所以为了执行效率，一般新生区的空间会被设置得比较小。

也正是因为**新生区的空间不大**，所以很容易被存活的对象装满整个区域。为了解决这个问题，JavaScript 引擎采用了**对象晋升策略**，
也就是经过两次垃圾回收依然还存活的对象，会被移动到**老生区**中。

#### 主垃圾回收器
主垃圾回收器主要负责老生区中的垃圾回收。除了新生区中晋升的对象，一些大的对象会直接被分配到老生区。
因此老生区中的对象有两个特点，**一个是对象占用空间大**，另一个是**对象存活时间长**。

由于老生区的对象比较大，若要在老生区中使用 Scavenge 算法进行垃圾回收，复制这些大的对象将会花费比较多的时间，从而导致回收执行效率不高，同时还会浪费一半的空间。
因而，主垃圾回收器是采用**标记 - 清除（Mark-Sweep）**的算法进行垃圾回收的。

1. 标记阶段就是从一组根元素开始，递归遍历这组根元素，在这个遍历过程中，能到达的元素称为活动对象，没有到达的元素就可以判断为垃圾数据。垃圾数据被标记,活动数据不被标记
2. 清除阶段,清除标记的数据
3. 整理数据,使用**标记 - 整理（Mark-Compact）算法**,让所有存活的对象都向一端移动，然后直接清理掉端边界以外的内存

![清除过程](https://static001.geekbang.org/resource/image/d0/85/d015db8ad0df7f0ccb1bdb8e31f96e85.png)

![标记整理过程](https://static001.geekbang.org/resource/image/65/8c/652bd2df726d0aa5e67fe8489f39a18c.png)

### 全停顿
> V8 是使用副垃圾回收器和主垃圾回收器处理垃圾回收的，不过由于 JavaScript 是运行在主线程之上的，一旦执行垃圾回收算法，都需要将正在执行的 JavaScript 脚本暂停下来，待垃圾回收完毕后再恢复脚本执行。我们把这种行为叫做**全停顿（Stop-The-World）**。

比如堆中的数据有 1.5GB，V8 实现一次完整的垃圾回收需要 1 秒以上的时间，这也是由于垃圾回收而引起 JavaScript 线程暂停执行的时间，
若是这样的时间花销，那么应用的性能和响应能力都会直线下降。

![全停顿](https://static001.geekbang.org/resource/image/98/0c/9898646a08b46bce4f12f918f3c1e60c.png)

在 V8 新生代的垃圾回收中，因其空间较小，且存活对象较少，所以全停顿的影响不大，但老生代就不一样了。
如果在执行垃圾回收的过程中，占用主线程时间过久，就像上面图片展示的那样，花费了 200 毫秒，在这 200 毫秒内，
主线程是不能做其他事情的。比如页面正在执行一个 JavaScript 动画，
因为垃圾回收器在工作，就会导致这个动画在这 200 毫秒内无法执行的，这将会造成页面的卡顿现象。

为了降低老生代的垃圾回收而造成的卡顿，V8 将标记过程分为一个个的子标记过程，同时让垃圾回收标记和 JavaScript 应用逻辑交替进行，
直到标记阶段完成，我们把这个算法称为**增量标记（Incremental Marking）算法**。

![增量标记算法](https://static001.geekbang.org/resource/image/de/e7/de117fc96ae425ed90366e9060aa14e7.png)

使用增量标记算法，可以把一个完整的垃圾回收任务拆分为很多小的任务，这些小的任务执行时间比较短，可以穿插在其他的 JavaScript 任务中间执行，这样当执行上述动画效果时，
就不会让用户因为垃圾回收任务而感受到页面的卡顿了。

## 内存泄漏
上面提到，当变量对象无法被清除就有可能发生内存泄漏，那到底什么是内存泄漏呢？
>本质上讲, 内存泄露就是不再被需要的内存, 由于某种原因, 无法被释放.

### 如何识别内存泄漏
在阮一峰老师的《JavaScript 内存泄漏教程》一文有提到过：按照法则如果连续五次五次垃圾回收之后，内存占用一次比一次大，就有内存泄漏。这就要求实时查看内存占用。

那么实际操作可以通过Chrome 浏览器查看内存占用和 Node 提供的process.memoryUsage方法来测试：

#### 浏览器测试
具体步骤如下：
1. 打开开发者工具，选择 Timeline 面板。
2. 在顶部的**Capture**字段里面勾选 Memory。
3. 点击左上角的录制按钮。
4. 在页面上进行各种操作，模拟用户的使用情况。
5. 一段时间后，点击对话框的 stop 按钮，面板上就会显示这段时间的内存占用情况。
6. 如果内存占用基本平稳，接近水平，就说明不存在内存泄漏，反之就是内存泄漏了。
![浏览器测试](http://www.ruanyifeng.com/blogimg/asset/2017/bg2017041704.png)
![浏览器测试1](http://www.ruanyifeng.com/blogimg/asset/2017/bg2017041705.png)
![浏览器测试2](http://www.ruanyifeng.com/blogimg/asset/2017/bg2017041706.png)

#### 命令行
具体步骤如下：
1. 通过输出**console.log(process.memoryUsage())**得到数据
2. **process.memoryUsage**返回一个对象，包含了 Node 进程的内存占用信息。该对象包含四个字段，单位是字节
	- rss（resident set size）：所有内存占用，包括指令区和堆栈。
	- heapTotal："堆"占用的内存，包括用到的和没用到的。
	- heapUsed：用到的堆的部分。
	- external： V8 引擎内部的 C++ 对象占用的内存。
3. 判断内存泄漏，以**heapUsed**字段为准

![命令行](http://www.ruanyifeng.com/blogimg/asset/2017/bg2017041702-1.png)

### 常见内存泄漏场景
#### 1.全局变量
前文提到过在页面中的全局变量, 只有当页面卸载后才会被销毁。所以当我们把变量定义到全局时就有可能发生内存泄漏。

**解决方法——手动清除**：当我们使用完全局变量后，对其重新赋值为 null。或者使用严格模式也是不错的选择！

#### 2. 未销毁的定时器和回调函数
像setInterval这种定时器如果不使用clearInterval() 手动清除，那么它会一直留在全局环境内不被清除。当然回调函数一样。

**解决方法**：需要确保在完成它们之后进行显式删除它们（或者对象将无法访问）。

#### 3. DOM
有时候我们在操作DOM节点时，可能有的节点从树中移除了，但保留了DOM节点的引用,导致GC没有回收。

**解决方法**：手动解除赋值null

### 闭包
最后我想谈谈闭包这个问题。在很多文章都把闭包归入造成内存泄漏的场景。但试想平日的开发场景中我们经常有使用到闭包，那岂不是日常开发会出现很多内存泄漏的问题？
关于这个问题我查阅了相关内容资料，最贴合说法就是：**老浏览器（主要是IE6）由于垃圾回收有问题导致很容易出现内存泄漏。但是那是浏览器实现的bug。**所以在现代浏览器中，
闭包其实已经不存在内存泄漏这个问题了。但同样的我也看到了这样的说法：**闭包本身不会造成内存泄漏，但闭包过多很容易导致内存泄漏。**

所以关于闭包的思考，我觉得还是得通过日常开发场景中用像上面提到的方法进行测试。但现有的结论就是：**闭包本身不会造成内存泄漏**。


# 总结
其实这篇文章写作的初衷是为了巩固自己对一些知识点的理解，也希望能帮到一些朋友。当然当中的内容也是看了很多文章和资料得出的思考，所以其实和你们之前看到的内容
有相似的地方其实也算正常的。同时我也希望这篇文章能够得到一些评论和建议，补足我在这方面的知识。如果喜欢这篇文章也可以给我个赞或者star，谢谢阅读。