# JVM运行时数据区



## 运行时数据区

 <img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200806160108277.png" alt="image-20200806160108277" style="zoom:40%;" />

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200806160211287.png" alt="image-20200806160211287" style="zoom:40%;" />

																						红色区域：所有线程共享
	
																						灰色区域：每个线程独占
	
																						JDK1.8之后，方法区被称为元数据区（元空间）	

**每一个JVM实例，对应一个Runtime实例（是个单例对象）**

### 线程

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200806161120905.png" alt="image-20200806161120905" style="zoom:40%;" />

什么叫做 ”Java线程准备好”？

当一个线程的 程序计数器、本地方法栈、虚拟机栈分配好内存后，该线程也就准备好了

### JVM系统进程

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200806161653056.png" alt="image-20200806161653056" style="zoom:40%;" />

### 程序计数器（PC计数器）

#### 介绍

比较特殊：既没有OOM（内存溢出），也没有GC（垃圾回收）

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200806163023139.png" alt="image-20200806163023139" style="zoom:50%;" />

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200806163317508.png" alt="image-20200806163317508" style="zoom:50%;" />

​				

#### 作用

指向下一条**即将要执行**的指令的**地址**

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200806162203296.png" alt="image-20200806162203296" style="zoom:50%;" />

					解读图中的意思：

1. PC寄存器是每一个线程独占一份
2. java栈中的每一个栈桢都是一个方法，java栈顶是即将要执行的方法；
3. PC寄存器存储的是 执行方法中即将要执行的下一条指令，比如图中当前PC寄存器指向的是栈顶方法的操作数栈

#### 两个面试问题

##### 问题一

###### 使用PC寄存器存储字节码执行地址有什么用？

###### 为什么使用PC寄存器记录当前线程的执行地址？

答：

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200806164843364.png" alt="image-20200806164843364" style="zoom:50%;" />

##### 问题二

###### PC寄存器为什么被设定成线程私有的？

1. 首先说在程序运行时有很多线程
2. 每个线程在执行的时候独占cpu，也就是每个cpu的每个时间片只能执行一个线程；就会导致每个线程的的PC寄存器所存储的字节码指令不一致
3. 如果PC寄存器是共享的，就会导致JAVA栈桢执行错误

cpu时间片

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200806165903225.png" alt="image-20200806165903225" style="zoom:40%;" />

### 虚拟机栈

#### 概述

##### 虚拟机栈出现的背景

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200806171013346.png" alt="image-20200806171013346" style="zoom:40%;" />

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200806175310217.png" alt="image-20200806175310217" style="zoom:50%;" />

###### java虚拟机栈是什么？

java虚拟机栈，早期也叫JAVA栈，**每个线程**在创建时都会创建一个虚拟机栈（一对一，线程私有），而虚拟机栈的内部保存的是一个个的栈桢（一个栈桢是一个方法），在java程序运行的时候，是对栈从栈顶到栈底的扫描，也就是对应一次次的JAVA方法调用

生命周期和线程保持一致

###### java虚拟机栈作用

主管Java程序的运行，保存方法的局部变量（8中基本数据类型，对象的引用地址）、部分结果，并参与方法的调用和返回

###### 虚拟机栈的特点（无gc，有OOM，操作简单）

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200806182114097.png" alt="image-20200806182114097" style="zoom:30%;" />

##### 栈可能会发生的异常

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200806195100930.png" alt="image-20200806195100930" style="zoom:40%;" />

Stack Overflow发生的情况：死循环或者正常情况下栈溢出

死循环：

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200806195326777.png" alt="image-20200806195326777" style="zoom:50%;" />

				运行后的异常信息：

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200806195347574.png" alt="image-20200806195347574" style="zoom:50%;" />

如果是正常情况下栈溢出：

可以通过 -Xss来设置栈的大小：

```JVM
设置1M的栈大小：
-Xss1m          1M的大小
-Xss1024k			  1M的大小
-Xss1048576			1M的大小
```



#### 栈的存储单位

每个线程都有自己的栈，而栈中的存储单位是 **栈桢**

那栈桢又是什么?

1. 栈桢和方法的关系 ：每个线程上正在执行的每个方法，都对应一个栈桢；也就是说一个栈桢对应一个方法
2. 栈桢内存表现：栈桢是一个内存区块，是一个数据集，维系着方法执行过程中的各种数据信息

#### 栈的运行原理

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200807114354985.png" alt="image-20200807114354985" style="zoom:40%;" />

总结：

1. JAVA栈遵循：先进后出，后进先出

2. 一个线程对应的栈，在一个时间点上，只会有一个正在执行的栈桢

3. 执行引擎所有到字节码指令 对应 当前栈桢

4. 如果当前栈桢调用了其他方法，则新的栈桢会被创建出来，放在栈的顶端，成为新的当前栈

   <img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200807115939279.png" alt="image-20200807115939279" style="zoom:39%;" />

总结：

1. 不同线程的栈桢不存在相互引用的情况
2. 返回结果是 依次向下传递的
3. 无论正常return返回或者抛出异常都会将该栈桢弹出

#### 栈桢的内部结构

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200807134411583.png" alt="image-20200807134411583" style="zoom:45%;" />

##### <img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200807134734161.png" alt="image-20200807134734161" style="zoom:50%;" />	

##### 局部变量表（本地变量表）

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200807173828568.png" alt="image-20200807173828568" style="zoom:40%;" />

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200807173654215.png" alt="image-20200807173654215" style="zoom:40%;" />

总结：

1. 局部变量表是一个数字数组，存储：方法参数、方法内部变量、返回地址
2. 方法内部的变量是安全的，不存在线程安全问题；且只能被当前方法调用
3. 一个方法能嵌套调用的方法数量是有限的（和栈的大小有关），如果方法内部嵌套的方法过多，则会StackOverFlow异常

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200810120018121.png" alt="image-20200810120018121" style="zoom:50%;" />

------

  ###### 关于slot的理解

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200807175851924.png" alt="image-20200807175851924" style="zoom:39%;" />



<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200807180422104.png" alt="image-20200807180422104" style="zoom:40%;" />

总结

1. 局部变量的下标从0开始，最基本的存储单元是Slot（变量槽）

2. 变量槽中可存储的类型：基本数据类型、引用数据类型、returnAddress类型

3. bit<=32分配一个槽；bit=64（Long，Double）分配两个槽

4. 方法的局部变量分配到变量槽中 的顺序，是按照 方法中定义变量的顺序

5. 当前方法是构造方法或者非静态方法，则index =0 的Slot上放的是this

   <u>这也就是为什么静态方法不能使用this的原因</u>

   <img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200807181427445.png" alt="image-20200807181427445" style="zoom:33%;" />

   																				非静态方法中，slot槽中第一个变量是this

slot的重复利用

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200807181745300.png" alt="image-20200807181745300" style="zoom:40%;" />

代码：

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200807182232210.png" alt="image-20200807182232210" style="zoom:40%;" />

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200807182158288.png" alt="image-20200807182158288" style="zoom:40%;" />



补充说明

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200807185858447.png" alt="image-20200807185858447" style="zoom:40%;" />

总结：

1. 栈桢越大，则栈能存储的栈就越少
2. 局部变量表中的变量是重要的 垃圾回收根节点，对应于 可达性算法的根节点

------

##### 操作数栈

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200807191043352.png" alt="image-20200807191043352" style="zoom:33%;" />

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200807191127869.png" alt="image-20200807191127869" style="zoom:50%;" />

------

##### 代码追踪

##### 栈顶缓存技术

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200810115713558.png" alt="image-20200810115713558" style="zoom:45%;" />

##### 动态链接（或指向运行时常量池的方法引用）

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200810115807460.png" alt="image-20200810115807460" style="zoom:50%;" />

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200810164203663.png" alt="image-20200810164203663" style="zoom:40%;" />

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200810164843746.png" alt="image-20200810164843746" style="zoom:50%;" />

例子：

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200810164445932.png" alt="image-20200810164445932" style="zoom:50%;" />

##### 

 









#### 方法的调用：解析与分派

###### 静态与动态链接

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200810170931992.png" alt="image-20200810170931992" style="zoom:43%;" />

静态链接:

	<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200810171124680.png" alt="image-20200810171124680" style="zoom:50%;" />

动态链接：

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200810171707316.png" alt="image-20200810171707316" style="zoom:50%;" />s

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200810172020562.png" alt="image-20200810172020562" style="zoom:50%;" />



				*invokeinterface*表示未确定调用方法的引用
	
	###### 
	
	###### 虚方法与非虚方法

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200810172433923.png" alt="image-20200810172433923" style="zoom:50%;" />

![image-20200810172907435](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200810172907435.png)



###### 方法重写的本质

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200810174853171.png" alt="image-20200810174853171" style="zoom:50%;" />

问题引出

问题1：如果是虚方法，岂不是每次调用都需要去再走一遍这样的流程?非常浪费时间、拉低性能，那怎么办呢？

为了节省时间，提高性能，我们建立**虚方法表**

问题2：那非虚方法需不要？

不需要，因为非虚方法是静态确定的方法；而非虚方法是动态确定的

###### 虚方法表

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200810180343040.png" alt="image-20200810180343040" style="zoom:40%;" />

```java
   总结：
   
   1. 虚方法表是在”方法区“建立的
   2. 每个类对应一个虚方法表
   3. 虚方法表在 类加载的linking阶段 被创建并初始化
      在类变量初始值准备好之后，虚方法表也初始化完毕 
```

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200810182546415.png" alt="image-20200810182546415" style="zoom:50%;" />   	







##### 方法的返回地址

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200810185948598.png" alt="image-20200810185948598" style="zoom:50%;" />



<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200810190045807.png" alt="image-20200810190045807" style="zoom:50%;" />

异常退出不会给上层调用者返回任何的值，但是会通过异常表来记录，并且会返回到该方法被调用的位置，如果调用方法有异常处理，则会进行异常处理

##### 一些附加信息

![image-20200810190300440](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200810190300440.png)

##### 栈的相关面试题



1. 举例栈溢出（StackOverFlow）的情况？.

   出现这种情况的原因是 栈空间不足

   ```
   a. 方法无限递归/循环，没有结束条件
   	解决方法：先确认是否是代码问题，如果不是，则调整栈的大小（-Xss）
   
   ```

2. 调整栈的大小（-Xss），能保证不溢出吗？

   ```markdown
   不能，原因是同上（方法无限递归/循环）
   ```

3. 分配的 栈内存越大越好？

```markdown
首先说总内存是有限的
如果栈内存的越大，对于栈来说出现StackOverFlow的几率变小
但是会挤占其他模块的（比如堆、方法区）的内存；
同样的创建的进程/线程数也会对应的减少
```

4. 虚拟机栈有垃圾回收（GC）吗？

```
没有
```

5. 方法中定义的**局部变量**是否存在线程安全问题？

   分情况

   如果该局部变量，仅仅只是该方法内部在用，则是安全的

   <img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200811135333836.png" alt="image-20200811135333836" style="zoom:50%;" />

   	如果是方法的形参，则是不安全的

   <img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200811135358142.png" alt="image-20200811135358142" style="zoom:50%;" />

   	如果返回了，也是不安全的

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200811135422536.png" alt="image-20200811135422536" style="zoom:50%;" />

### 本地方法栈

#### 本地方法

##### 什么是本地方法

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200811135923891.png" alt="image-20200811135923891" style="zoom:40%;" />

##### 为什么是要使用本地方法

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200811141712049.png" alt="image-20200811141712049" style="zoom: 30%;" />

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200811142254367.png" alt="image-20200811142254367" style="zoom:33%;" />

总结：

1. java刚出现的时候，正是c/c++盛行的时候，所以必须依赖于c/c++才能迅速成长
2. 涉及到和硬件交互或者考虑到执行效率的问题，还是必须依赖于c/c++

###### 现状

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200811142148978.png" alt="image-20200811142148978" style="zoom:33%;" />

#### 本地方法栈

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200811220419916.png" alt="image-20200811220419916" style="zoom:40%;" />

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200811220845328.png" alt="image-20200811220845328" style="zoom:40%;" />

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200811221040562.png" alt="image-20200811221040562" style="zoom:30%;" />



### 堆

#### 堆的核心概述

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200813165637732.png" alt="image-20200813165637732" style="zoom:33%;" />



<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200813173715878.png" alt="image-20200813173715878" style="zoom:30%;" />

 

总结：

1. 一个JVM实例（一个main函数） 对应一个堆内存
2. 堆内存大小在JVM启动的时候就确定了，要么是默认的，要么是通过(-Xms -Xmx)显式设置
3. 堆不是完全线程共享的，TLAB是线程私有的缓冲区

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200813180212043.png" alt="image-20200813180212043" style="zoom:50%;" />

总结：

1. 不是所有的对象都会分配在堆中，比如基本数据类型
2. 栈桢中（局部变量表）保存着堆对象的引用
3. 方法结束，栈桢中堆对象的引用也就销毁了，此时堆中的对象是没有被引用的，根据可达性算法，此时堆中的对象应该被回收，但是不会随着方法的结束（栈桢的弹出）而回收该对象，是随着GC的时候释放堆中的对象
4. 栈是没有gc的，只有堆/方法区才有GC，并且堆是GC的重点区域

##### 内存细分

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200813182154801.png" alt="image-20200813182154801" style="zoom:36%;" />

																										JDK1.8之后，永久区变为元空间

#### 设置堆内存大小与OOM

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200813183657842.png" alt="image-20200813183657842" style="zoom:40%;" />

总结：

1. -Xms (-XX:InitialHeapSize)

   ms : memory start

   堆内存初始值

2. -Xmx(-XX:MaxheapSize)

   mx :memory max

   堆内存最大值

3. 设置-Xms,-Xmx的目的：①节省内存空间 ②避免GC后重新分隔计算堆区

4. 堆有默认的初始大小

   

#### 年轻代与老年代

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200813230227568.png" alt="image-20200813230227568" style="zoom:73%;" />

图解：

1. 对象分为两类：瞬时对象、生命周期非常长的对象
2. Java堆分为：年轻代和老年代
3. 年轻代分类：Eden、Survivor0（from区）、Survivor1（to区）

##### 参数

###### -XX:NewRatio



<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200813230549442.png" alt="image-20200813230549442" style="zoom:63%;" />



参数：-XX:NewRatio = 2,表示老年代占2，年轻代占1，也就是说 老年代占堆的2/3,年轻代1/3

![image-20200813231721323](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200813231721323.png)

													jinfo -flag NewRatio XXXX：	查看代码号为XXXX的程序的，新生代和老年代的比例

###### -XX:SurvivorRatio

这个参数的意义是：新生代中eden，from，to的比例

这个参数oracle官方给出的默认-XX:SurvivorRatio =8，也就是说 新生代中eden，from，to的比例：8：1：1

但实际上: **这个参数默认确实是8**，但是**实际运行的时候不是这样的**，因为有个”自适应的内存分配策略“在搞鬼

		![image-20200813233048799](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200813233048799.png)
	
			**Eden : Survivor0: Survivor1 =  6:1:1,而不是8：1：1**，这个发现可以在面试的时候说，可以证明我们是真的操作过

那怎么才能变成8：1：1？

在jvm参数中明确指定：-XX:SurvivorRatio =8

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200813233507530.png" alt="image-20200813233507530" style="zoom:33%;" />

然后运行程序，打开jvisual VM：

![image-20200813233427867](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200813233427867.png)

											此时就变成了 8:1:1



##### 总结

![image-20200813234156350](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200813234156350.png)

图解

1. hotSopt中，默认的比例 Eden:from:to = 8:1:1
2. 可以通过-XX:SurvivorRatio参数来设定这个比例
3. 除了对象大到Eden区装不下，否则new的对象一定是在Eden区
4. 绝大部分的新对象在 新生代就销毁了：比如方法中定义的局部变量
5. 可以通过 -Xmn来设置新生代最大内存，但是如果同时也设置了 -XX:NewRatio = 2，那也是按照-Xmn来设置新生代最大内存，会忽略-XX:NewRatio = 2

#### 图解对象分配过程

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200814175345636.png" alt="image-20200814175345636" style="zoom:43%;" />

##### 图解:

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200814175727789.png" alt="image-20200814175727789" style="zoom:50%;" />

图1：Eden区创建了5个对象，红色的表示没有引用的，绿色的表示还在被引用的；当eden区满了之后，会发生YGC，此时，会回收红色的对象，而绿色的对象会进入survivor0区，且年龄为1

```
问题1：如果survivor满了之后会触发YGC？

			答：不会，只有当Eden区满了之后，触发YGC，这时候才会 回收survivor的内存；也就是说 survivor即便是满了，也不会主动触发YGC，而是被动触发；
问题2：那survivor满了，此时YGC也没有触发，那survivor怎么办？

```

图2：

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200814180522106.png" alt="image-20200814180522106" style="zoom:33%;" />

解读：eden区内存满了触发GC：

1. eden区中未被GC的对象会进入survivor区
2. 同时也会检测survivor0和survivor1中的对象是否还在被引用，如果还在被引用的，则从 s0进入s1（空的），或者从s1进入s0（空的），**任何时刻总是有一个Survivor区是空的**
3. 初次进入survivor的对象的age=1 , survivor0和survivor1中的对象也会进行来回倒腾，倒腾一次age+1，当对象的age满足进入 老年代的要求，即可进入老年代区

图3：

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200814181118994.png" alt="image-20200814181118994" style="zoom:50%;" />

解读：

1. 当eden区触发 GC的时候，如果survivor中的对象有age = 15（达到进入老年代的阈值），则会分配到 老年代中

```
问题1：有没可能survivor区中的对象age不满足阈值的时候，也可以进入老年代？

问题2：有没有可能对象一开始就分配到了老年代区域？
```



##### 总结

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200814181806648.png" alt="image-20200814181806648" style="zoom:30%;" />

```
IBM研发发现，80%的对象在eden区就被销毁了
```

##### 对象分配流程图

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200814183221337.png" alt="image-20200814183221337" style="zoom:50%;" />







#### MinorGC（YGC），Major GC，Full GC

 ##### 概述

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200814190305784.png" alt="image-20200814190305784" style="zoom:50%;" />

##### Minor GC

![image-20200814190703509](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200814190703509.png)

图解：

1. 当eden满了之后会触发MinorGC，survivor区此时被动GC；且survivor区满了，也不会触发GC
2. Minor GC会引发STW（stop the work），暂停其他用户线程（防止在回收的时候，用户线程继续制造垃圾）

##### Minor GC(老年代GC)

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200814191155292.png" alt="image-20200814191155292" style="zoom:50%;" />

##### FULL GC

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200814191456480.png" alt="image-20200814191456480" style="zoom:50%;" />

#### 堆空间分代思想

##### 为什么需要把堆分代管理？不分代就不能正常工作了？

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200816182838854.png" alt="image-20200816182838854" style="zoom:43%;" />

#### ![image-20200816183309505](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200816183309505.png)

图解：

1.  对象的生命周期不同，大多（70%-99%）对象属于临时对象（消亡的比较快）

2. 按照生命周期的长短分别管理：

   a.可以针对不同的对象，设置不同的GC器，以提高gc的效率，提高GC性能

   b.在gc的时候，不用对所有的对象同时gc



#### 内存分配策略

  <img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200816184142290.png" alt="image-20200816184142290" style="zoom:50%;" />

图解;

1. 如果eden区装的下 新对象，则直接分配在eden区，否则分配在 老年代，如果老年代也装不下，则oom
2. 对象经过多次gc后，age满足了进入老年代的阈值，则在gc'后直接进入老年代
3. 当survivor中的对象： age=x的对象的大小总和 > survivor区的一半，则survivor中age>=x的对象均直接进入老年代，而不必等到age满足老年代的阈值

##### 对象分配流程图

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200814183221337.png" alt="image-20200814183221337" style="zoom:50%;" />

#### 为对象分配内存：TLAB

##### 为什么会有TLAB



#### ![image-20200816185405083](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200816185405083.png)

1. 首先说堆区是线程共享的
2. 如果在并发环境下，从堆区中划分内存空间是线程不安全的
3. 为避免多个线程同时操作同一个地址，我们需要加锁，但是加锁又影响了性能，所以这时候 TLAB应运而生



##### 什么是TLAB？

![image-20200816185837442](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200816185837442.png)

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200816185940885.png" alt="image-20200816185940885" style="zoom:43%;" />



##### TLAB再说明

![image-20200816190322299](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200816190322299.png)

图解：

1. 因为TLAB占用eden区的空间较小，所以不是所有的对象都能成功分配在eden区
2. 可以通过参数：-XX:UseTLAB 来控制TLAB的开关,默认情况下是开启的
3. 默认情况下，TLAB空间仅占eden区1%的空间，但是可以通过 -XX:TLABWasteTargetPercent来分配
4. 如果对象不能分配在TLAB区，则会分配在eden区且通过加锁，确保数据操作的原子性

```
问题：堆空间中所有的对象都是共享的吗？

答:
不是，堆空间中存在 线程私有的TLAB

问题：什么是TLAB？为什么要有TLAB？那对象分配在TLAB的流程是？
```

##### TLAB对象分配过程

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200816190951626.png" alt="image-20200816190951626" style="zoom:50%;" />

#### 堆空间的参数设置

-XX:+PrintFlagsInitial : 查看所有参数的默认值

-XX:+PrintFlagsFianl : 查看所有参数的最终值（可能存在修改）

-Xms : 初始堆空间大小（默认是物理内存的1/64）

```
-Xms:1024  (byte为单位)
-Xms:1K
-Xms:1M


```

-Xmx堆空间的最大值（默认是物理内存的1/4）

```
-Xmx:1024  (byte为单位)
-Xmx:1K
-Xmx:1M
```

-Xmn : 新生代的大小

```
-Xmn:1024  (byte为单位)
-Xmn:1K
-Xmn:1M
```

-XX:NewRatio : 配置新生代与老年代在堆结构中的占比

```
-XX:NewRatio:8
意义： 老年代 ：新生代 = 8:1

问题：这个值设置的比默认值小？
	此时，老年代的占比变小，新生代的占比变大；minor gc次数变少，但是major gc、full gc次数变多
问题：这个值设置的比默认值大？
  此时。老年代的占比变大，新生代的占比变小；minor gc次数变多，则阻塞stw（用户线程）的次数也就变多了
```

-XX:SurvivorRatio ： 设置 eden、s0、s1空间的比例

```
-XX:SurvivorRatio:8
意义： eden ：s0:s1 = 8:1:1


问题：这个值设置的比默认值小？
	此时，eden内存变小，s0/s1变大，则发生ygc的次数变多，影响stw；且一次进入老年的对象也会变多
问题：这个值设置的比默认值大？
  	此时，eden内存变大，s0/s1变小，ygc次数变少，但是进入s0/s1的对象变多，甚至当s0/s1装不下的时候，进入老年代的对象也随之增多
```

-XX:MaxTenuringThreshold : 设置新生代垃圾的最大年龄（进入老年代的年龄）

```90
-XX:MaxTenuringThreshold:15  (默认是15)


问题：这个值设置的比默认值小？
	进入老年代的对象变多，触发major gc的概率变高
问题：这个值设置的比默认值大？
  	留存在s0/s1的对象变多，新生代的内存压力变大；
```

-XX:+PrintGCDetails :输出详细的GC处理日志

-XX:HandelPromotionFailure :是否设置空间分配担保

![image-20200816194445257](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200816194445257.png)

					这张图是对-XX:HandelPromotionFailure参数的解释

jdk1.7及之后，-XX:HandelPromotionFailure无法显视声明 ，且-XX:HandelPromotionFailure= true

校验规则为：

老年代的**连续空间** > 新生代所有对象总和大小或者

老年代的**连续空间** > 历次晋升到老年代的对象的平均大小

则进行minor gc，因为这时候认为老年代是可以装的下的

否则进行full gc





##### 哪种gc会影响stw？

1. Serial GC：Full GC整个过程STW，Young GC整个过程STW

2. Parallel GC：Full GC整个过程STW，Young GC整个过程STW

3. CMS GC：Full GC整个过程STW，Young GC整个过程STW，Old GC只有两个小阶段STW

4. G1 GC：Full GC整个过程STW，Young GC整个过程STW，Mixed GC由全局并发标记和对象复制组成，全局并发标记其中两个小阶段STW，其它并发

5. Shenandoah GC/ZGC：它们都是回收堆的一部分，所以没有Full GC（Full GC是指回收整个堆，与之相对的是Partial GC，比如CMS GC的Old GC和G1的Mixed GC均属于此类）的概念

#### 堆是分配对象的唯一选择吗?

说到jvm的性能瓶颈，我们首先想到的就是堆，如果能减少gc 次数（较少stw），那么性能也就上去了

##### 逃逸分析技术引出

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200816202611580.png" alt="image-20200816202611580" />

##### 逃逸分析技术概述

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200816202807963.png" alt="image-20200816202807963" style="zoom:50%;" />

图解;

1. 逃逸分析：分析对象的作用域

   a. 如果在方法中new，且只是在方法中使用，没有return该对象，则认为没有发生逃逸，该对象可以在栈上分配

   b.其余的都是在 堆上分配

###### 例子

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200816203133640.png" alt="image-20200816203133640" style="zoom:50%;" />

sb.toString() 相当于new了一个String串，而sb对象就始终只是在方法内部使用

##### 逃逸分析参数

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200816203333188.png" alt="image-20200816203333188" style="zoom:50%;" />![image-20200816203403979](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200816203403979.png)

##### 总结

![image-20200816203409769](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200816203409769.png)

```
原因：
1.避免线程安全问题

2.将对象在栈上分配，缓解堆内存的压力，减少gc，提升性能
```



##### 代码优化

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200816225252644.png" alt="image-20200816225252644" style="zoom:50%;" />

###### 栈上分配

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200816224913466.png" alt="image-20200816224913466" style="zoom:50%;" />

不开启逃逸分析:

-Xms200m -Xmx200m -XX:-DoEscapeAnalysis  -XX:+PrintGCDetails

控制台表现：会有gc，且耗时45ms

![image-20200816223812515](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200816223812515.png)



jvm内存表现:: 存活的实例数多

![image-20200816223920031](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200816223920031.png)

开启逃逸分析：

jvm参数 ： 

-Xms20m -Xmx20m -XX:+DoEscapeAnalysis  -XX:+PrintGCDetails

控制台表现：不发生gc，且耗时短

![image-20200816224016263](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200816224016263.png)

内存表现：实例数较少

![image-20200816223453091](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200816223453091.png)



###### 标量替换

参数：-XX:+EliminateAllocations 默认是开启的

![image-20200816230304332](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200816230304332.png)

总结来说，就是如果对象只是在方法内部使用，且-XX:+EliminateAllocations ，则会被拆解成一个个的成员变量

如下代码：alloca方法中的User，其实是被 从聚合量拆成了标量

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200816230040113.png" alt="image-20200816230040113" style="zoom:50%;" />

好处：因为未发生逃逸，在栈上分配空间

##### 逃逸分析总结

![image-20200816230915472](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200816230915472.png)

1. 无法保证逃逸分析的性能消耗一定高于他的消耗
2. 逃逸分析的过程就是一个相对耗时的过程
3. 在极端情况下，逃逸分析的过程会白白浪费

#### 堆小结

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200817211821943.png" alt="image-20200817211821943" style="zoom:50%;" />





### 方法区

![image-20200818213312142](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200818213332.png)



<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200818213624.png" alt="image-20200818213623873" style="zoom:50%;" />

#### 1 、堆、栈、方法区之间的交互关系

图解：

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200818213756.png" alt="image-20200818213756487" style="zoom:55%;" />

```
当new一个对象时，java栈中会存储这个对象的引用；堆中存储这个实例；方法区中存储这个对象的数据类型
```

#### 2、方法区的理解

##### 2.1方法区的基本理解

![image-20200818220410895](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200818220411.png)

图总结:

1. 方法区和堆一样是线程共享的区域

2. 方法区的实际物理空间是可以不连续的

3. 方法区的大小决定了系统可以保存多少个类，如果超出了就会OOM

   在jdk1.7及之前是OOM: PermGen space(永久代OOM)

   在jdk1.8及之后是OOM: MetaSpace(元空间OOM)

   这种情况多出现在：加载第三方的jar包，因为第三方的类太多，导致方法区的OOM；反射动态生成大量的类，导致方法区的OOM

##### 2.2 HotSpot中方法区的演进

![image-20200818221601608](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200818221601.png)

1. Jdk1.7及之前方法区 称为永久代；jdk1.8及之后方法区 被称为元数据

2. Jdk1.7和1.8的改变不只是方法区名称的变化，**更大的变化是存储结构的变化**

   jdk1.7之前永久代是 **存储在jvm虚拟内存**当中的，通过参数-XX:MaxPermSize来控制，且这个参数的值不会很大，所以容易OOM:PermGen space

   jdk1.8的元空间是 **使用本地内存**，受限于机器的内存，不容易OOM：MetaSpace

   



#### 3、设置方法区大小与OOM

##### 版本比较

###### JDK1.7



<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200819194155.png" alt="image-20200819194155137" style="zoom: 33%;" />

###### JDK1.8

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200819194657.png" alt="image-20200819194657821" style="zoom:50%;" />



在JDK1.8中查看Permsize(永久代大小)：

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200819193545.png" alt="image-20200819193545499" style="zoom:50%;" />

														可以发现，该参数是找不到的，是因为JDK1.8中这个参数已经被替换为了:MetaSpaceSize

官方文档：

![image-20200819193824354](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200819193824.png)

意思是在JDK1.8中 -XX:MaxPermSize和  -XX:PermSize	已经被替换为了 -XX:MaxMetaspaceSize 和-XX:MetaspaceSize

###### OOM

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200819214652.png" alt="image-20200819214651908" style="zoom:50%;" />

 ```
   图总结:
   
   1. 通过运维导出的dump文件在jprofiler上进行分析，确认内存中的对象是否有必要，分清楚是内存泄露（Memory Leak）和内存溢出（Memory Overflow）
   
   2. 如果是内存泄露（Memory Leak），可以通过分析泄露对象 是通过怎样的路径 与GC ROOTS相关联的（也就是通过引用连来分析对象无法自动回收的原因），同时通过分析引用连也可以定位出泄露代码的位置
   
   3. 如果是内存溢出（Memory Overflow），也就是说内存中的对象确实都还活着，那就应该：
   
      a. 检查虚拟机的堆参数（-Xms -Xmx）,可以结合物理内存适当的调大
   
      b.从代码上检查 某些对象是否 生命周期过长
 ```

   



#### 4、方法区的内部结构

##### 方法区存储什么？

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200819220100.png" alt="image-20200819220100892" style="zoom:33%;" />

##### 类型信息

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200819220437.png" alt="image-20200819220437278" style="zoom:50%;" />

针对第四个解释下:

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200819220627.png" alt="image-20200819220627059" style="zoom: 33%;" />

图中代码的 方法所实现的接口：Comparable,Serializible就会在方法区中存储成一个有序列表，顺序就是impements的顺序

##### 域（类中的属性）信息

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200819220923.png" alt="image-20200819220923479" style="zoom:33%;" />

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200819221114.png" alt="image-20200819221114027" style="zoom:33%;" />



###### non-final的类变量

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200819223425.png" alt="image-20200819223425870" style="zoom:33%;" />

###### final和static修饰的属性

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200819223527.png" alt="image-20200819223527243" style="zoom:33%;" />

通过javap -v -p class文件，来查看反编译后的字节码文件：

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200819222831.png" alt="image-20200819222831103" style="zoom:33%;" />

count这个值，是在linking阶段中的prepare阶段初始化为0，在<clinit>阶段赋值

number这个值，是在linking阶段中的prepare阶段直接赋值为2



##### 方法信息

![image-20200819222123707](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200819222123.png)





##### 运行时常量池

###### 为什么需要常量池

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200819224712.png" alt="image-20200819224712850" style="zoom: 33%;" />

图总结：

1. java字节码中需要很多的常量来支持，如果直接存到字节码中，将会很大
2. 使用常量池来保存这部分常量，字节码只需要保存这部分常量的引用

###### 常量池中有什么？

首先明确，常量池是Class文件的一部分，用于存放编译期间生成的各种字面量和符号引用

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200819225705.png" alt="image-20200819225705358" style="zoom:33%;" />

代码分析：

源代码：

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200819231808.png" alt="image-20200819231808355" style="zoom:43%;" />

使用jclasslib分析：

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200819230141.png" alt="image-20200819230141183" style="zoom:43%;" />

																						图：查看MethodAreaTest类的域信息

先分析：域名为zhaozihuiInt的域信息：



可以看到Name：co_info #7   意思是：zhaozihuiInt的域名对应常量池中7的位置

下图中的String，即为域zhaozihuiInt的域名

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200819230309.png" alt="image-20200819230309778" style="zoom:43%;" />

可以看到Decription：co_info #8   意思是：zhaozihuiInt的域值对应常量池中8的位置

下图中的String，即为域zhaozihuiInt的域的类型为int

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200819230349.png" alt="image-20200819230348885" style="zoom:43%;" />



再来看看域zhaozihuiStr

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200819231019.png" alt="image-20200819231019755" style="zoom:43%;" />

常量池中9位置处的信息：

String即为域名

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200819231110.png" alt="image-20200819231110194" style="zoom:50%;" />

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200819225831.png" alt="image-20200819225831771" style="zoom:33%;" />

常量池中10位置处的信息：

描述的是域的类型为 java.lang.String

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200819231215.png" alt="image-20200819231215673" style="zoom:50%;" />



###### 运行时常量池

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200819233024.png" alt="image-20200819233023983" style="zoom:33%;" />

图片总结:

|               |        常量池        |                         运行时常量池                         |      |
| :-----------: | :------------------: | :----------------------------------------------------------: | ---- |
|     所属      |      Class文件       |                            方法区                            |      |
| 编译期/运行时 |        编译期        |                            运行时                            |      |
|   存储内容    | 各种字面量与符号引用 | 不仅会加载编译器确定好的字面量和符号引用，也会包括 运行期解析后获得的方法或者字段引用 |      |
|   引用方法    |         索引         |                           真实地址                           |      |
|      OOM      |                      |                            会发生                            |      |
|    动态性     |          no          |                             yes                              |      |



#### 5、方法区的举例使用



#### 6、方法区的演进细节（面试）





<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200825154435.png" alt="image-20200825154434943" style="zoom:33%;" />

##### JDK1.6

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200825154804.png" alt="image-20200825154803970" style="zoom:33%;" />

StringTable：字符串常量池

##### 1.7

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200825155002.png" alt="image-20200825155002849" style="zoom:33%;" />

**此时的永久代还是用的 虚拟内存**

##### 1.8

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200825155119.png" alt="image-20200825155119076" style="zoom:33%;" />

注意1.8之后的方法区，是存储在 本地内存当中的，方法区的大小受本地内存大小的影响





##### 永久代为什么要被替换成 元空间？（面试会问）



<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200825160149.png" alt="image-20200825160149755" style="zoom:33%;" />

总结：

1. 因为无法预知，要加载多少的类：引入的jar包，以及反射的存在，所以就无法确定永久代的大小，如果设置的永久代大小不合适，很容易出现永久代的OOM

   而元空间是直接使用本地内存的，其大小受本地内存的限制，永久代是基于虚拟内存的

2. 永久代的调优是非常难的

##### 为什么要把StringTable（字符串常量）放到堆中？

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200825161756.png" alt="image-20200825161756106" style="zoom:33%;" />

上图总结：

1. 我们在开发中会有大量的字符串被创建，而永久代的分配的空间又不会很大
2. 永久代的回收频率很低，因为只有在full gc的时候才会去回收，而full gc只有当 老年代或者永久代空间不足的时候才会触发
3. 而放到堆中，一来是因为 minor gc的频率通常高于full gc的，这样就能及时回收内存，而来是因为 老年代有：minor gc和full gc，而永久代只有full gc

#### 7、方法区的垃圾回收

##### 主要回收的两部分内容

```
常量池中废弃的常量 和 不在使用的类型
```

##### 垃圾回收的判断条件

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200825172418.png" alt="image-20200825172418789" style="zoom:33%;" />

#### 8、总结

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200825182338.png" alt="image-20200825182338315" style="zoom:33%;" />

![image-20200825183031539](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200825183031.png)



#### 9、面试题

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200825183301.png" alt="image-20200825183301002" style="zoom:33%;" />

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200825183338.png" alt="image-20200825183338200" style="zoom:33%;" />





### 对象的实例化内存布局和访问定位

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200826170534.png" alt="image-20200826170534021" style="zoom:25%;" />



#### 对象的实例化

先看脑图：[https://www.processon.com/view/link/5f47772f7d9c086b99051214](https://www.processon.com/view/link/5f47772f7d9c086b99051214)

1. 加载类元信息
2. 为对象分配内存
3. 处理并发问题
4. 属性的默认初始化（零值初始化）：基本数据类型为0，对象为null
5. 设置对象的头信息
6. 属性的显式初始化  ：属性直接赋值显式初始化---->代码块中的初始化--->构造器中的初始化

#### 对象的内存布局

##### 布局脑图

![image-20200827172608628](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200827172608.png)

注意图中的”对象头“和”实例数据“ 包含的内容，面试常问的

类：

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200827172758.png" alt="image-20200827172758240" style="zoom:33%;" />

主方法：

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200827171919.png" alt="image-20200827171918807" style="zoom:33%;" />



针对上述程序JVM内存布局图解：

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200827172056.png" alt="image-20200827172056002" style="zoom:33%;" />





#### 对象的访问定位

JVM是如何通过栈帧中的对象引用访问到其内部的对象实例呢？

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200827175629.png" alt="image-20200827175629551" style="zoom:33%;" />

##### 对象访问的两种方式
###### 句柄访问
<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200827174831.png" alt="image-20200827174831530" style="zoom:33%;" />

句柄S访问就是说栈的局部变量表中，记录的对象的引用，然后在堆空间中开辟了一块空间，也就是句柄池
**优点**
reference中存储稳定句柄地址，对象被移动（垃圾收集时移动对象很普遍）时只会改变句柄中实例数据指针即可，reference本身不需要被修改
**缺点**
相较于 "直接指针"，句柄方式 性能低于 直接指针，因为直接指针是直接访问到了对象，而句柄还需要先访问到句柄池，然后再访问到对象实例

###### 直接指针（HotSpot采用）

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200827180713.png" alt="image-20200827180713285" style="zoom:33%;" />

直接指针是局部变量表中的引用，直接指向堆中的实例，在对象实例中有类型指针，指向的是方法区中的对象类型数据

## 

 

