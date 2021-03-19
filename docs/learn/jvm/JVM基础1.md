---
typora-copy-images-to: upload
---

[TOC]

# JVM

## 概述

### 什么叫虚拟机？

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200721223925145.png" alt="image-20200721223925145" style="zoom:50%;" />

### 什么叫java虚拟机

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200721224029081.png" alt="image-20200721224029081" style="zoom:50%;" />

### jvm的位置

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200721224104031.png" alt="image-20200721224104031" style="zoom:35%;" />

### JVM整体结构

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200721224159442.png" alt="image-20200721224159442" style="zoom:50%;" />

## 类加载

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200721224626639.png" alt="image-20200721224626639"  />

### 类加载器与类的加载过程

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200721225225163.png" alt="image-20200721225225163" style="zoom:65%;" />



​		 

​			<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20200818102710.png" style="zoom:50%;" />

  		类加载器子系统负责从文件系统或者网络中加载Class文件，Class文件在文件开头有特定的**文件标识**，而检查Class文件的文件标识是在链接阶段的验证步骤完成的

![image-20200721231349634](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200721231349634.png )

​																					图中就是所谓的“文件标识”

​		   ClassLoader只负责class文件的加载，至于它是否可以运行，则由Excution Engine（执行引擎）决定

​		  加载的类信息存放于一块称为方法区的内存空间。除了类的信息外，方法区中还会存放运行时常量池信息，可能还包括字符串字面量、数字常量（这部分常量信息是Class文件中常量池部分的内存映射）

#### 类的加载过程

##### 1.loading



<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200721230832827.png" alt="image-20200721230832827" style="zoom:67%;" />

##### 2.Linking

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200721231804916.png" alt="image-20200721231804916" style="zoom:67%;" />

###### 2.1 验证：确保被加载的类的正确性

  确保Class文件的字节流中包含的信息符合当前虚拟机的要求，并且不会危害虚拟机自身的安全。

- 文件格式验证：验证字节流是否符合Class文件格式的规范，如：是否以模数0xCAFEBABE开头、主次版本号是否在当前虚拟机处理范围内等等。
- 元数据验证：对字节码描述的信息进行语义分析，以保证其描述的信息符合Java语言规范的要求；如：这个类是否有父类，是否实现了父类的抽象方法，是否重写了父类的final方法，是否继承了被final修饰的类等等。
- 字节码验证：通过数据流和控制流分析，确定程序语义是合法的、符合逻辑的，如：操作数栈的数据类型与指令代码序列能配合工作，保证方法中的类型转换有效等等。
- 符号引用验证：确保解析动作能正确执行；如：通过符合引用能找到对应的类和方法，符号引用中类、属性、方法的访问性是否能被当前类访问等等。

  验证阶段是非常重要的，但不是必须的。可以采用**-Xverify:none**参数来关闭**大部分**的类验证措施。

######  2.2 准备：为类的静态变量分配内存，并将其赋默认值

  为类的**静态变量**分配内存并设置类变量初始值，这些内存都将在方法区中分配。对于该阶段有以下几点需要注意：

- 只对static修饰的静态变量进行内存分配、赋默认值（如0、0L、null、false等）。
- 对final的**静态字面值常量**直接赋初值（赋初值不是赋默认值，如果不是字面值静态常量，那么会和静态变量一样赋默认值）。

 ###### 2.3 解析：将常量池中的符号引用替换为直接引用（内存地址）的过程

  符号引用就是一组符号来描述目标，可以是任何字面量。属于编译原理方面的概念如：包括类和接口的全限定名、字段的名称和描述符、方法的名称和描述符。

  直接引用就是直接指向目标的指针、相对偏移量或一个间接定位到目标的句柄。如指向方法区某个类的一个指针

 ##### 3.Initing(为类的静态变量赋初值)

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200721233309273.png" alt="image-20200721233309273" style="zoom:67%;" />

初始化：为类的静态变量赋初值,赋初值两种方式：

- 定义静态变量的指定值。如 private static String x="123";

- 在静态代码块里为静态变量赋值。如 static{ x="123"; }

  注意：只有对类的**主动使用**才会导致类的初始化。 

###### 3.1clinit 与 init

  在编译生成class文件时，编译器会产生两个方法加于class文件中，一个是**类的初始化方法**clinit, 另一个是**实例的初始化方法**init。

3.1.1 clinit (class-init)

  clinit指的是类构造器，主要作用是在类加载过程中的初始化阶段进行执行，执行内容包括**静态变量**初始化和**静态块**的执行。

  注意事项：

1. **如果类中没有静态变量或静态代码块，那么clinit方法将不会被生成**。

   没有任何的变量和代码块：

   <img src="../../Library/Application%20Support/typora-user-images/image-20201119173312597.png" alt="image-20201119173312597" style="zoom:50%;" />

   有非静态的变量，也不会生成cinit

   <img src="../../Library/Application%20Support/typora-user-images/image-20201119173436168.png" alt="image-20201119173436168" style="zoom:50%;" />

   存在静态的变量：

   <img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20201119173553.png" alt="image-20201119173553809" style="zoom:59%;" />

2. 在执行clinit方法时，必须先执行**父类**的clinit方法。

3. clinit方法**只执行一次**。

4. static变量的赋值操作和静态代码块的合并顺序由源文件中出现的顺序决定。如下代码所示：

```java
public class TestClass {
    public static void main(String[] args) {
        ClassInit init=ClassInit.newInstance();
 
        System.out.println(init.x);
        System.out.println(init.y);
    }
}
class ClassInit{
    private static ClassInit init=new ClassInit();
    public static int x;
    public static int y=5;
    static{
        x=10;
        y=10;
    }
    private ClassInit(){
        x++;
        y++;
    }
    public static ClassInit newInstance(){
        return init;
    }
}
```

![image-20201119153344959](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/20201119153345.png)

解读：

1. 类加载的准备阶段：为类的静态变量分配内存并设置类变量**初始值**，即x=0,y=0

2. 第一行调用了ClassInit的**构造方法**（new ClassInit(); ），于是开始执行构造方法中的++自增操作，此时x=1,y=1

3. **继续执行**

   ```java
   public static int x;
   public static int y=5;
   ```

此时，因为x没有被赋值，所以x保持x=1

y被赋值为5

4. **继续执行**

   ```java
   static{
       x=10;
       y=10;
   }
   ```

此时，x=10,y=10

------

3.1.2  init

  init指的是实例构造器，主要作用是在类实例化过程中执行，执行内容包括成员变量初始化和代码块的执行。

  注意事项：

1. 如果类中没有**静态**成员变量和**静态**代码块，那么**clinit方法将不会被生成**。

  2. 在执行init方法时，必须先执行**父类**的init方法。

3. init方法**每实例化一次就会执行一次**。

4. init方法先为（非静态、非final）实例变量分配内存空间，再执行赋默认值，然后根据源码中的**顺序执行赋初值或代码块**。如下代码所示：

```java
public class TestClass {
    public static void main(String[] args) {
        ClassInit init=new ClassInit();
    }
}
 
class ClassInit{
    public int x;
    public int y=111;
    public ClassInit(){
        x=1;
        y=1;
    }
    {
        x=2;
        y=2;
    }
    {
        x=3;
        y=3;
    }
}
//实例化步骤为：先为属性分配空间，再执行赋默认值，然后按照顺序执行代码块或赋初始值，最后执行构造方法
//根据上述代码，init方法的伪代码如下：
init(){
	  x=0;//赋默认值
    y=0;//赋默认值 ----init：x,y赋默认值
    x=0;//public int x,没有进行赋值操作，继续保持初始值
    y=111;//public int y=111 赋初值
  
    x=2;//从上到下执行第一个代码块
    y=2;//从上到下执行第一个代码块
    x=3;//从上到下执行第二个代码块
    y=3;//从上到下执行第二个代码块
    //ClassInit();执行构造方法
    x=1;//最后执行构造方法
    y=1;//最后执行构造方法
}
------------------------------------------------------------------------------------------------------------
public class TestClass1 {
    public static void main(String[] args) {
        ClassInit1 init = new ClassInit1();
        System.out.println(init.x);
        System.out.println(init.y);
    }
}

class ClassInit1 {
    {
        x = 3;
        y = 3;
    }

    {
        x = 2;
        y = 2;
    }
    public int x;
    public int y = 111;

    public ClassInit1() {
        System.out.println(x);
        System.out.println(y);
        x = 1;
        y = 1;
    }

}
//如果上述代码的成员变量x,y的定义在类最后时，那么init方法的伪代码如下：
init(){
	  x=0;//赋默认值
    y=0;//赋默认值----init：x,y赋默认值
    x=3;//从上到下执行第一个代码块
    y=3;//从上到下执行第一个代码块
    x=2;//从上到下执行第二个代码块
    y=2;//从上到下执行第二个代码块
  
    x=2;// public int x;没有对x进行赋值，x继续保持2
    y=111;//public int y = 111;赋初值
    //ClassInit();执行构造方法
    x=1;//最后执行构造方法
    y=1;//最后执行构造方法
}
```

​	注：<clinit>是用来加载static类变量的赋值操作和static代码块中的语句（**对static域和static代码块初始化的逻辑全部封装在<clinit>方法中***）<init>是类的构造函数所以二者的执行顺序的是：<clinit>---><init>（可以参考 https://www.cnblogs.com/czwbig/p/11155555.html）

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200721234340519.png" alt="image-20200721234340519" style="zoom:50%;" />

输出结果:

![image-20200721234405245](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200721234405245.png)

分析:因为jvm保证类在初始化的时候是sync加锁状态，所以导致其他的线程阻塞；所以结果是只有线程2在对ThreadTest进行初始化；虚拟机必须保证一个类的<Clinit>()方法在多线程下被同步加锁

#### 类加载器的分类

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200723224808941.png" alt="image-20200723224808941" style="zoom:67%;" />

Boostrap ClassLoader负责加载java核心类库

Extension ClassLoader、System ClassLoader及其他负责加载用户自定义的类

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200723225527089.png" alt="image-20200723225527089" style="zoom:50%;" />

##### BootStrapClassLoader

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200723230021746.png" alt="image-20200723230021746" style="zoom: 50%;" />![image-20200723230246152](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200723230246152.png)

##### ExtClassLoader

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200723230021746.png" alt="image-20200723230021746" style="zoom: 50%;" />![image-20200723230246152](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200723230246152.png)![image-20200723230433227](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200723230433227.png)

##### AppClassLoadder

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200723230021746.png" alt="image-20200723230021746" style="zoom: 50%;" />![image-20200723230246152](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200723230246152.png)![image-20200723230433227](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200723230433227.png)

##### ClassLoader

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200726225235365.png" alt="image-20200726225235365" style="zoom:65%;" />

除了Bootstrap ClassLoader，其他类加载器均继承ClassLoder

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200726230124278.png" alt="image-20200726230124278" style="zoom:95%;" />

ExtClassLoader和AppClassLoader都是在Launcher类定义的内部类

###### 获取ClassLoader几种方式

![image-20200726230310790](https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200726230310790.png)

### 用户自定义类加载器

#### 为什么要自定义类加载器?

1. 隔离加载类
2. 修改类加载的方式
3. 扩展加载源
4. 防止源码泄露

#### 用户自定义类加载器的实现步骤

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200804225141106.png" alt="image-20200804225141106" style="zoom:67%;" />

### 双亲委派机制

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200804225907695.png" alt="image-20200804225907695" style="zoom:70%;" />

​																								JVM对class文件的加载是懒加载

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200804230055798.png" alt="image-20200804230055798" style="zoom:65%;" />

​								

###### 例子自定义java.lang.String类：

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200804230548901.png" alt="image-20200804230548901" style="zoom:67%;" />

如果运行：

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200804230621369.png" alt="image-20200804230621369" style="zoom:80%;" />

这是为什么呢？

**因为java开头的包，一定是引导类记载器去加载的**；而上述代码自定义了一个java.lang.String，类加载器发现是java开头的，于是交给引导类加载器去加载，而引导类加载器加载的是jdk里边的java.lang.String,而jdk里边的java.lang.String里边肯定是没有main方法的，所以报错：找不到main方法

###### 例子自定义java.lang.zhaozihui类：

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200806143852647.png" alt="image-20200806143852647" style="zoom:50%;" />

运行结果：

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200806144040331.png" alt="image-20200806144040331" style="zoom:45%;" />

#### 双亲委派机制的优势

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200806144346316.png" alt="image-20200806144346316" style="zoom:50%;" />

说明：

1. 如果java coder自定义了java.lang.String类，则在运行的时候根本不会去加载自定义的类，因为jvm发现要加载的类是java开头的，则会交给BootStrap ClassLoader去加载，而此时我们自定义的java.lang.String中有main函数，则会报错：java.lang.String中不存在main方法

   <img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200804230548901.png" alt="image-20200804230548901" style="zoom:50%;" />

2. 如果java coder自定义了java.lang.zhaozihui类，则在运行的时候，vm发现要加载的类是java开头的，则会交给BootStrap ClassLoader去加载，而jdk的java.lang下根本没有zhaozihui类，就会报安全错误

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200806143852647.png" alt="image-20200806143852647" style="zoom:42%;" />

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200806144040331.png" alt="image-20200806144040331" style="zoom:40%;" />

#### 沙箱安全机制

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200806144954384.png" alt="image-20200806144954384" style="zoom:35%;" />

​														其实就是上边自定义的 java.lang.String的例子；沙箱安全机制是为了防止恶意程序的攻击

#### 其他

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200806153907593.png" alt="image-20200806153907593" style="zoom:35%;" />

##### 对类加载器的引用

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200806154015743.png" alt="image-20200806154015743" style="zoom:35%;" />

##### 类的主动使用和被动使用

<img src="https://zhaozihui-typro.oss-cn-beijing.aliyuncs.com/typora/image-20200806154219455.png" alt="image-20200806154219455" style="zoom:35%;" />

那区别是什么呢？

主动使用：会导致类的初始化

被动使用：不会导致类的初始化

注意这里的初始化指的是：类加载过程中的第三个阶段initing阶段

























