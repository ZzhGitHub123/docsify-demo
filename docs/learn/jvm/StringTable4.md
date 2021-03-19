# StringTable

## 1. String的基本特性

### final不可被继承

### Serilaizable支持序列化

### Comparable

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200901190740.png" alt="image-20200901182730872" style="zoom:33%;" />

​																	jdk1.8及以前String使用的是char数组，jdk1.9及以后使用的是byte数组。

问题：为什么要将char数组 改为byte数组 ?

答：**节约内存空间**：因为开发人员通过**数据**发现人们使用的字符串值是**拉丁字符（A-Z a-z)居多**而之前使用的char数组每一个**char占用两个字节**而**拉丁字符只需要一个字节**就可以存储，**剩下的一个字节就浪费了**，造成**内存**的浪费，导致**gc**频繁。因此在jdk9中将String底层的实现改为了byte数组。

### 不可变性

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200901190741.png" alt="image-20200901184820040" style="zoom:50%;" />

以上三种操作均不会改变原先的 字面量，而是生成了一个新的字面量

### 字符串常量池中不会存储相同内容的字符串

字符串的字面量在常量池中只会有一份

### String  Pool的HashTable

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200901190742.png" alt="image-20200901190714506" style="zoom:33%;" />

图总结：

1. StringTable的底层是个HashTable（数组+链表）
2. -XX:StringtableSize=x 可以设置StringTable的长度
3. JDK6中的StringYable是固定的（长度1009），且对 -XX:StringTableSize长度没有要求
4. JDK7中的StringYable默认长度 60013
5. JDK8中的StringTable的长度最小1099

## 2. String的内存分配

String的两种将字面量存储到常量池中的方法：

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200916155804.png" alt="image-20200902134511835" style="zoom:33%;" />

​														如果是String str = new String("abc123");则不会使用常量池来存储

## 3. String的基本操作

1. " + "操作

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200916155805.png" alt="image-20200902162755284" style="zoom: 50%;" />

Str1 != Str4的原因是：Str4 = str3 + "abc"的操作，在字节码的执行过程: 

先new 出一个**StringBuilder**，然后append（str3）,然后append（”abc“）,最后通过**toString**方法new出一个String对象，将这个String对象的引用赋值给str4

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200916155806.png" alt="image-20200902163303060"  />

## 4. String拼接操作



<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200916155807.png" alt="image-20200902164342882" style="zoom:33%;" />

图总结：

1. 常量与常量的拼接：String str ="123" + "abc";  这种操作就是在编译器优化的，直接编译成"123abc"，存储到常量池

2. 常量池中不会有相同的常量："123abc"这个字面量在常量池中只会有一份

3. 在拼接的时候如果有变量，那么最终的拼接的结果是在堆中。之所最终结果在堆中，是因为拼接的原理是StringBuilder

   示例代码1：

   ![image-20200902164943310](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200916155808.png)

   字节码指令：

   ![image-20200902165032719](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200916155809.png)

过程是：先new 出一个**StringBuilder**，然后**append**（str3）,然后**append**（”abc“）,最后通过**toString**方法new出一个String对象，将这个String对象的引用赋值给str4

​				示例代码2：如果 字符串拼接的时候，**拼接的变量都是final类型的字符串**，则在**编译期**就完成拼接，不使用StringBuilder

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200916155810.png" alt="image-20200906185301440" style="zoom:33%;" />

**所以说在开发时，对于 类、方法、基本数据类型、引用数据类型，能用fianl修饰就用final修饰**

### 字符串编译器直接拼接操作和append操作的效率对比

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200916155811.png" alt="image-20200906191307195" style="zoom:43%;" />

从以上程序可知：当循环一万次 进行 字符串拼接操作时，直接拼接的效率远远低于 append

#### 原因

1. 底层原理	

   Method1：字符串拼接操作的底层原理是Stringbuilder.append，每进行一次拼接都需要new一个StringBuilder对象，然后再append；

   Method2：直接使用StringBuilder进行append，不需要每次拼接都new StringBuilder

2. 对象的GC：

   Mehtod1：每次拼接都重新new一个StringBuilder 、生成新的String字面量、新的String对象（拼接完成后调用toSting）,而之前的对象就需要通过GC来销毁

   Mehtod2：一直是一个Stirngbuilder对象

#### Method2的改进的方法

因为StringBuilder的底层是通过char[ ]来存储的，如果空间不足时（StringBuidler默认数组容量是16），则需要扩容；这个也是非常耗时 ；

为了减少扩容的时间消耗，我们可以一开始就指定一个初始容量；

可以使用：StringBuilder ss = new StringBuilder( 1000);来指定初始容量为1000



append的底层代码：

先判断容量是否足够



<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200916155812.png" alt="image-20200906192257207" style="zoom:43%;" />

当空间不足，则调用Arrays.copy进行扩容

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200916155813.png" alt="image-20200906192346819" style="zoom:43%;" />

   

## 5. intern()的使用

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200916155814.png" alt="image-20200906193834833" style="zoom:53%;" />

先看第一段代码：

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200916155815.png" alt="image-20200906213737609" style="zoom:100%;" />

JDK6：

![image-20200906214609222](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200916155816.png)

注：**图中绿色线条代表 string 对象的内容指向。 黑色线条代表地址指向。**

如上图所示。首先说一下 jdk6中的情况，在 jdk6中上述的所有打印都是 false 的，因为 jdk6中的**常量池是放在 Perm 区**中的，Perm 区和正常的 JAVA Heap 区域是**完全分开**的。上面说过如果是使用**引号声明的字符串都是会直接在字符串常量池中生成**，而 n**ew 出来的 String 对象是放在 JAVA Heap 区域**。所以拿一个 JAVA Heap 区域的对象地址和字符串常量池的对象地址进行比较肯定是不相同的，即使调用`String.intern`方法也是**没有任何关系**的。

JDK7：

再说说 jdk7 中的情况。这里要明确一点的是，在 **Jdk6 以及以前**的版本中，字符串的**常量池是放在堆的 Perm** 区的，Perm 区是一个类静态的区域，主要存储一些加载类的信息，常量池，方法片段等内容，默认大小只有4m，一旦常量池中大量使用 intern 是会直接产生`java.lang.OutOfMemoryError: PermGen space`错误的。 所以在 **jdk7** 的版本中，**字符串常量池**已经从 Perm 区**移到**正常的 **Java Heap** 区域了。为什么要移动，Perm 区域太小是一个主要原因，当然据消息称 jdk8 已经直接取消了 Perm 区域，而新建立了一个元区域。应该是 jdk 开发者认为 Perm 区域已经不适合现在 JAVA 的发展了。

正式因为字符串常量池移动到 JAVA Heap 区域后，再来解释为什么会有上述的打印结果。

![image-20200906214915102](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200916155817.png)

- 在第一段代码中，先看 s3和s4字符串。`String s3 = new String("1") + new String("1");`，这句代码中现在生成了2最终个对象，是字符串常量池中的“1” 和 JAVA Heap 中的 s3引用指向的对象。中间还有2个匿名的`new String("1")`我们不去讨论它们。此时s3引用对象内容是”11”，**但此时常量池中是没有 “11”对象的**（这里可以通过字节码验证）。
- 接下来`s3.intern();`这一句代码，是将 s3中的“11”字符串放入 String 常量池中，因为此时常量池中不存在“11”字符串，因此常规做法是跟 jdk6 图中表示的那样，在常量池中生成一个 “11” 的对象，关键点是 jdk7 中常量池不在 Perm 区域了，这块做了调整。常量池中不需要再存储一份对象了，可以直接存储堆中的引用。这份引用指向 s3 引用的对象。 也就是说引用地址是相同的。
- 最后`String s4 = "11";` 这句代码中”11”是显示声明的，因此会直接去常量池中创建，创建的时候发现已经有这个对象了，此时也就是指向 s3 引用对象的一个引用。所以 s4 引用就指向和 s3 一样了。因此最后的比较 `s3 == s4` 是 true。
- 再看 s 和 s2 对象。 `String s = new String("1");` 第一句代码，生成了2个对象。常量池中的“1” 和 JAVA Heap 中的字符串对象。`s.intern();` 这一句是 s 对象去常量池中寻找后发现 “1” 已经在常量池里了。
- 接下来`String s2 = "1";` 这句代码是生成一个 s2的引用指向常量池中的“1”对象。 结果就是 s 和 s2 的引用地址明显不同。图中画的很清晰。



第二段代码：

![image-20200906214235688](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200916155818.png)

JDK6的解释：

还是那句话，堆和perm区没有什么关系

JDK7

![image-20200906215015294](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200916155819.png)

- 来看第二段代码，从上边第二幅图中观察。第一段代码和第二段代码的改变就是 `s3.intern();` 的顺序是放在`String s4 = "11";`后了。这样，首先执行`String s4 = "11";`声明 s4 的时候常量池中是不存在“11”对象的，执行完毕后，“11“对象是 s4 声明产生的新对象。然后再执行`s3.intern();`时，常量池中“11”对象已经存在了，因此 s3 和 s4 的引用是不同的。
- 第二段代码中的 s 和 s2 代码中，`s.intern();`，这一句往后放也不会有什么影响了，因为对象池中在执行第一句代码`String s = new String("1");`的时候已经生成“1”对象了。下边的s2声明都是直接从常量池中取地址引用的。 s 和 s2 的引用地址是不会相等的。

总结：从上述的例子代码可以看出 jdk7 版本对 intern 操作和常量池都做了一定的修改。主要包括2点：

- 将String常量池 从 Perm 区移动到了 Java Heap区
- `String#intern` 方法时，如果存在堆中的对象，会直接保存对象的引用，而不会重新创建对象。

![image-20200906215520683](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200916155820.png)

### 练习1：

![image-20200906220045885](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200916155821.png)

但是在JDK6中，是true，false，因为JDK6中，s.intern()是新new了一个'ab'字面量在perm区的常量池，intern又把字符串常量池的地址返回给s2

### 练习1变形：

![image-20200906220724863](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200916155822.png)

## 6. StringTable的垃圾回收





## 7.G1中的String去重操作

首先明确的是，去重操作不是对字符串常量池的去重；而是对String内部的char[ ]数组的去重

![image-20200916154356963](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200916155823.png)

![image-20200916154506898](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200916155824.png)



## 8、面试题

![image-20200906194026147](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200916155825.png)



### 题目一：new String("ab")会创建几个对象?

答案：两个，String对象和”ab“字面量

那怎么证明是两个呢？看字节码来证明

![image-20200906194344799](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200916155826.png)

第0行：new了一个String对象

第4行：ldc命令的意思是从常量池中取出"ab"字面量放到栈顶

所以是两个对象

### 题目二：new String("a") + new String("b")

![image-20200906194949822](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200916155827.png)

第一行：因为涉及字符串的拼接操作，所以会用Stringbuidler来完成拼接，这里会new Stringbuidlder

第7-13：先new一个String，再ldc从常量池中取字面量”a“

第19-25：先new一个String，再ldc从常量池中取字面量”b“

第31行：Stringbuidler.toString生成一个String对象



总结：**按顺序**生成的对象有：

两个字面量"a"和”b“

一个Stringbuidler对象

两个String对象

toString产生的一个String对象

总共是：6个

**值得注意的是并没生成“ab”这个字面量，而题目一中生成了“ab”**











