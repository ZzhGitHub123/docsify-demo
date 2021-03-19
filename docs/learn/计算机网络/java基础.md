[TOC]

# 一、基础

## 1.Java入门

### 1.1 java语言的特点

1. 面向对象(封装、继承、多态)
2. 可移植、平台无关性【JVM】
3. 编译与解释共存

### 1.2 JVM、JDK、JRE

#### 1.2.1 JVM

Java 虚拟机（JVM）是运行 Java 字节码的虚拟机。JVM 有针对不同系统的特定实现（Windows，Linux，macOS），目的是使用相同的字节码，它们都会给出相同的结果。

**什么是字节码?采用字节码的好处是什么?**

> 在 Java 中，JVM 可以理解的代码就叫做`字节码`（即扩展名为 `.class` 的文件），它不面向任何特定的处理器，只面向虚拟机。Java 语言通过字节码的方式，在一定程度上解决了传统解释型语言执行效率低的问题，同时又保留了解释型语言可移植的特点。所以 Java 程序运行时比较高效，而且，由于字节码并不针对一种特定的机器，因此，Java 程序无须重新编译便可在多种不同操作系统的计算机上运行。

**Java 程序从源代码到运行一般有下面 3 步：**

<img src="/Users/zhaozihui/Library/Application%20Support/typora-user-images/image-20210307131600473.png" alt="image-20210307131600473" style="zoom:50%;" />

我们需要格外注意的是 .class->机器码 这一步。在这一步 JVM 类加载器首先加载字节码文件，然后通过解释器逐行解释执行，这种方式的执行速度会相对比较慢。而且，有些方法和代码块是经常需要被调用的(也就是所谓的热点代码)，所以后面引进了 JIT 编译器，而 JIT 属于运行时编译。当 JIT 编译器完成第一次编译后，其会将字节码对应的机器码保存下来，下次可以直接使用。而我们知道，机器码的运行效率肯定是高于 Java 解释器的。这也解释了我们为什么经常会说 Java 是编译与解释共存的语言。

> HotSpot 采用了惰性评估(Lazy Evaluation)的做法，根据二八定律，消耗大部分系统资源的只有那一小部分的代码（热点代码），而这也就是 JIT 所需要编译的部分。JVM 会根据代码每次被执行的情况收集信息并相应地做出一些优化，因此执行的次数越多，它的速度就越快。JDK 9 引入了一种新的编译模式 AOT(Ahead of Time Compilation)，它是直接将字节码编译成机器码，这样就避免了 JIT 预热等各方面的开销。JDK 支持分层编译和 AOT 协作使用。但是 ，AOT 编译器的编译质量是肯定比不上 JIT 编译器的。

**总结：**

​		Java 虚拟机（JVM）是运行 Java 字节码的虚拟机。JVM 有针对不同系统的特定实现（Windows，Linux，macOS），目的是使用相同的字节码，它们都会给出相同的结果。字节码和不同系统的 JVM 实现是 Java 语言“一次编译，随处可以运行”的关键所在。

#### 1.2.2 JDK

* JDK: java development kit, java开发工具包，用来开发Java程序的，针对java开发者；JDK是整个JAVA的核心，包括了Java的运行环境（Java Runtime Environment）、一堆Java工具（javac、java、jdb等）和Java基础的类库（即Java API包括rt.jar）；Java API是Java的应用程序接口，里面有很多已经写好的Java Class，包括一些重要的语言结构以及基本图形，网络和文件I/O等等，我们可以直接调用使用。

- JRE: java runtime environment, java运行时环境，针对java用户；在Java平台下，所有的Java程序都需要在JRE下才能运行。只有JVM还不能进行class的执行，因为解释class的时候，JVM需要调用解释所需要的类库lib。JRE里面有两个文件夹bin和lib，这里可以认为bin就是JVM，lib就是JVM所需要的类库，而JVM和lib合起来就称为JRE。JRE包括JVM和JAVA核心类库与支持文件。与JDK不同，它不包含开发工具—-编译器、调试器和其它工具。



<img src="/Users/zhaozihui/Library/Application%20Support/typora-user-images/image-20210307131846500.png" alt="image-20210307131846500" style="zoom:33%;" />

##### Oracle JDK和Open JDK的区别

1. OpenJDK是开源的，Oracle JDK是官方的
2. OpenJDK 是一个参考模型并且是完全开源的，而Oracle JDK是OpenJDK的一个实现，并不是完全开源的
3. racle JDK 比 OpenJDK 更稳定。OpenJDK和Oracle JDK的代码几乎相同，但Oracle JDK有更多的类和一些错误修复。因此，如果您想开发企业/商业软件，我建议您选择Oracle JDK，因为它经过了彻底的测试和稳定。某些情况下，有些人提到在使用OpenJDK 可能会遇到了许多应用程序崩溃的问题，但是，只需切换到Oracle JDK就可以解决问题
   

#### 1.2.3 JRE

Java目录下其实是有两个jre的

<img src="/Users/zhaozihui/Library/Application%20Support/typora-user-images/image-20210307132206546.png" alt="image-20210307132206546" style="zoom:33%;" />



JDK里面的JRE是JDK自带的为其开发工具提供运行环境的JRE，在JDK中有很多用Java编写的开发工具（如： javac.exe、jar.exe），这些工具的实现代码在JDK下面的lib目录下的tools.jar中，也就是路径：`D:\dev\java\jdk1.8.0_91\lib\tools.jar`里面。

JDK外面JRE用于为Java程序提供运行环境。

##### 两个JRE的异同

- 相同点：这两个JRE都可以作为开发Java程序的运行环境。
- 不同点：**JDK自带**的开发工具**只能**使用JDK自己目录下的JRE，不能使用JDK外面的JRE。



### 1.3编译与解释共存的java

<img src="/Users/zhaozihui/Library/Application%20Support/typora-user-images/image-20210307133518685.png" alt="image-20210307133518685" style="zoom:50%;" />

编译：.java文件--->javac---->.class文件

解释：java解释器 逐行解释.class文件称为机器可执行的二进制机器码

## 2.java语法

### 2.1 字符型常量和字符串常量的区别

1. 形式上: 字符常量是单引号引起的一个字符; 字符串常量是双引号引起的 0 个或若干个字符
2. 含义上: 字符常量相当于一个整型值( ASCII 值),可以参加表达式运算; 字符串常量代表一个地址值(该字符串在内存中存放位置)
3. 占内存大小 字符常量只占 2 个字节; 字符串常量占若干个字节 (**注意： char 在 Java 中占两个字节**)

> 字符封装类 `Character` 有一个成员常量 `Character.SIZE` 值为 16,单位是`bits`,该值除以 8(`1byte=8bits`)后就可以得到 2 个字节

<img src="/Users/zhaozihui/Library/Application%20Support/typora-user-images/image-20210307134033766.png" alt="image-20210307134033766" style="zoom:40%;" />

### 2.2 泛型【待补充】

### 2.3 ==和equals

**`==`** : 它的作用是判断两个对象的`地址`是不是相等。即判断两个对象是不是同一个对象。(**基本数据类型==比较的是值，引用数据类型==比较的是内存地址**)

> 因为 Java 只有值传递，所以，对于 == 来说，不管是比较基本数据类型，还是引用数据类型的变量，其本质比较的都是值，只是引用类型变量存的值是对象的地址。

**`equals()`** : 它的作用也是判断两个对象是否相等，它不能用于比较基本数据类型的变量。`equals()`方法存在于`Object`类中，而`Object`类是所有类的直接或间接父类。

`Object`类`equals()`方法：

```java
public boolean equals(Object obj) {
     return (this == obj);
}
```

`equals()` 方法存在两种使用情况：

- 情况 1【没有覆盖，比较地址】：类没有覆盖 `equals()`方法。则通过`equals()`比较该类的两个对象时，等价于通过“==”比较这两个对象。使用的默认是 `Object`类`equals()`方法。
- 情况 2【覆盖，比较值】：类覆盖了 `equals()`方法。一般，我们都覆盖 `equals()`方法来两个对象的内容相等；若它们的内容相等，则返回 true(即，认为这两个对象相等)。



### 2.4 hashCode和equals

参考：https://www.cnblogs.com/skywang12345/p/3324958.html

#### ==与equals的区别

##### ==的作用

==: 它的作用是判断两个对象的`地址`是不是相等。即，判断两个对象是不是同一个对象(基本数据类型

比的是值，引用数据类型比的是内存地址)

##### equals的作用

equals() : 它的作用也是**判断两个对象是否相等**。但它一般有两种使用情况:

情况 1:类没有覆盖 equals() 方法。则通过 equals() 比较该类的两个对象时，等价于通过 “==”比较这两个对象的内存地址。

情况 2:类覆盖了 equals() 方法。一般，我们都覆盖 equals() 方法来比较两个对象的内容是否相等;若它们的内容相等，则返回 true

#### HashCode与equals

##### 散列码的作用

我们都知道，散列表存储的是键值对(key-value)，它的特点是：能根据“键”快速的检索出对应的“值”。这其中就利用到了散列码！

散列表的本质是通过**数组**实现的。当我们要获取散列表中的某个“值”时，实际上是要获取数组中的某个位置的元素。而数组的位置，就是通过“键”来获取的；更进一步说，数组的位置，是通过“键”对应的散列码计算得到的。

###### hashCode()的作用

&nbsp;  hashCode() 的作用是获取哈希码，也称为散列码；它实际上是返回一个int整数。这个哈希码的作用是确定该对象在哈希表中的**索引位置**。hashCode() 定义在JDK的Object.java中，这就意味着Java中的任何类都包含有hashCode() 函数。

&nbsp;  虽然，每个Java类都包含hashCode() 函数。但是，**仅仅当**创建并某个“类的散列表”(如HashMap，Hashtable，HashSet)时，该类的hashCode() **才有用**(作用是：确定该类的每一个对象在散列表中的位置；其它情况下(例如，创建类的单个对象，或者创建类的对象数组等等)，类的hashCode() 没有作用。在散列表中hashCode() 的作用是获取对象的散列码，进而确定该对象在散列表中的**索引**位置。

###### 举例说明hashcode的作用

<img src="../../../Library/Application%20Support/typora-user-images/image-20210308201004417.png" alt="image-20210308201004417" style="zoom:50%;" />



注意图中的最后一句话：这是在散列表中的情况。在非散列表中一定如此

#### hashCode和equals的关系

##### 1. 第一种 不会创建“类对应的散列表”

不会创建HashSet, Hashtable, HashMap等等这些本质是散列表的数据结构的时候用到该类，

例如：不会创建该类的HashSet集合，在这种情况下，该类的“hashCode() 和 equals() ”**没有半毛钱关系**的，因为没有hash数据结构，就根本不会用到hashCode

##### 2. 第二种 会创建“类对应的散列表”

“会创建类对应的散列表”是说：我们会在HashSet, Hashtable, HashMap等等这些本质是散列表的数据结构中，用到该类。例如，会创建该类的HashSet集合。

在这种情况下，该类的“hashCode() 和 equals() ”是有关系的：

1)、如果两个对象equals()相等，那么它们的hashCode()值一定相同。

2)、如果两个对象hashCode()相等，它们并不一定相等。

因为在散列表中，hashCode()相等，即两个键值对的哈希值相等。然而哈希值相等，并不一定能得出键值对相等。补充说一句：“两个不同的键值对，哈希值相等”，这就是哈希冲突。

此外，在这种情况下。若要判断两个对象是否相等，除了要覆盖equals()之外，也要覆盖hashCode()函数。否则，equals()无效。

例如，创建Person类的HashSet集合，必须同时覆盖Person类的equals() 和 hashCode()方法。

###### 只是覆盖equals()方法

![image-20210308201050836](../../../Library/Application%20Support/typora-user-images/image-20210308201050836.png)



###### 覆盖hashcode()和equlas()

![image-20210308201110262](../../../Library/Application%20Support/typora-user-images/image-20210308201110262.png)





更多关于 `hashcode()` 和 `equals()` 的内容可以查看：[Java hashCode() 和 equals()的若干问题解答](https://www.cnblogs.com/skywang12345/p/3324958.html)

## 3.基本数据类型

### 3.1 自动装箱、拆箱

#### 3.1.1 什么是装箱和拆箱

装箱：将基本数据类型 自动转化为 包装类型

拆箱：将包装类型 自动转化为 基本数据类型



![image-20210308203328084](../../../Library/Application%20Support/typora-user-images/image-20210308203328084.png)

#### 3.1.2装箱和拆箱的原理

![image-20210308204036456](../../../Library/Application%20Support/typora-user-images/image-20210308204036456.png)

　　从反编译得到的字节码内容可以看出，在装箱的时候自动调用的是Integer的valueOf(int)方法。而在拆箱的时候自动调用的是Integer的intValue方法。其他的也类似，比如Double、Character

### 3.2 八种基本类型的包装类和缓存池

#### 自身范围缓存

即：Boolea，byte,char值都是取自缓存

##### Boolean

布尔类型直接把true和false都缓存了

````java
public final class Boolean implements java.io.Serializable,Comparable<Boolean>

{

public static final Boolean TRUE = new Boolean(true);

public static final Boolean FALSE = new Boolean(false);

public static Boolean valueOf(boolean b) {

	return (b ? TRUE : FALSE);

	}
}
````

##### char

ascii 码范围为 0-127, 这里只缓存ascii码范围的char

Character 静态内部类 CharacterCache不过通过下面这段代码可以看出来，他的范围是: 0<=x<=127

````java
public final class Character implements java.io.Serializable, Comparable<Character> {

public static Character valueOf(char c) {
    if (c <= 127) { // 小于等于127，则直接从缓存中取
    		return CharacterCache.cache[(int)c];
		}
		return new Character(c);
	}

private static class CharacterCache {
    private CharacterCache(){}
    static final Character cache[] = new Character[127 + 1];
      static {
			 /***for循环赋值，将0-127的值全部缓存*/
				for (int i = 0; i < cache.length; i++)
      	cache[i] = new Character((char)i);
      }
   }
}
````

##### byte

byte 自身的值的范围是: -128<=x<=127；而byte的缓存是 [-128,127],所以byte一定是从缓存中取的

````java
public final class Byte extends Number implements Comparable<Byte> {

  public static Byte valueOf(byte b) {
      final int offset = 128;
      return ByteCache.cache[(int)b + offset];
  }

private static class ByteCache {
    private ByteCache(){}
    static final Byte cache[] = new Byte[-(-128) + 127 + 1];
    static {
        for(int i = 0; i < cache.length; i++){
          	cache[i] = new Byte((byte)(i - 128));
       	 } 
   		 }
		}
}
````



#### 范围为256的缓存

##### short

如果超过了 -128<=x<=127 这个范围,就不会从缓存中获取了

```` java
public final class Short extends Number implements Comparable<Short> {

  public static Short valueOf(short s) {
        final int offset = 128;
        int sAsInt = s;
        if (sAsInt >= -128 && sAsInt <= 127) { // [-128,127]才会从缓存中取
            return ShortCache.cache[sAsInt + offset];
        }
        return new Short(s);
  	}

private static class ShortCache {

    	private ShortCache(){}

    	static final Short cache[] = new Short[-(-128) + 127 + 1];

      static {

        for(int i = 0; i < cache.length; i++)

        cache[i] = new Short((short)(i - 128));

      }
		}
}


````

##### int (可以通过参数配置上限)

这里的从缓存中获取的代码是一样的,**重点是 IntegerCache 的范围是可以配置的**

从源码中可以看出来 最低值low一定是-128 是不可以修改的,但是上限值high 是可以修改的。

通过jvm参数: -Djava.lang.Integer.IntegerCache.high=1024 修改为1024，判断是否有jvm参数java.lang.Integer.IntegerCache.high

解析为 int 类型(解析失败就用默认值127)，修改最大值和最小值

如果小于127 那么 high = 127，如果 大于 Integer.MAX_VALUE - 129 ,就设置为 Integer.MAX_VALUE - 129

，将 low<=x<=high的值加入到缓存，验证 IntegerCache.high > 127

````java
public final class Integer extends Number implements Comparable<Integer> {

    public static Integer valueOf(int i) {
        if (i >= IntegerCache.low && i <= IntegerCache.high){
          return IntegerCache.cache[i + (-IntegerCache.low)];
        }
        return new Integer(i);
    }

private static class IntegerCache {
      static final int low = -128;
      static final int high;
      static final Integer cache[];

      static {
              // 最大值可能会从java.lang.Integer.IntegerCache.high中取
              int h = 127;
              String integerCacheHighPropValue =
              sun.misc.VM.getSavedProperty("java.lang.Integer.IntegerCache.high");
        
              if (integerCacheHighPropValue != null) {
                  try {
                    int i = parseInt(integerCacheHighPropValue);
                    //比较127和配置的值，取两者之中的最大值
                    i = Math.max(i, 127);
                    // 数组的最大值是 Integer.MAX_VALUE-129
                    h = Math.min(i, Integer.MAX_VALUE - (-low) -1);
                    //Math.min(i, Integer.MAX_VALUE +128 -1);
                  } catch( NumberFormatExceMath.min(i, Integer.MAX_VALUE - (-low) -1);ption nfe) {
                  // If the property cannot be parsed into an int, ignore it.
                  }
              }
              high = h;
              // 数组的最大值是 Integer.MAX_VALUE
              cache = new Integer[(high - low) + 1];
              int j = low;
              for(int k = 0; k < cache.length; k++)
              	cache[k] = new Integer(j++);
              // range [-128, 127] must be interned (JLS7 5.1.7)
              assert IntegerCache.high >= 127;
          }
					private IntegerCache() {}

			}

}
````



##### long

范围: -128<=x<=127

````java
public final class Long extends Number implements Comparable<Long> {

    public static Long valueOf(long l) {
          final int offset = 128;
          if (l >= -128 && l <= 127) { // will cache
          				return LongCache.cache[(int)l + offset];
    			}
      	return new Long(l);
			}
		private static class LongCache {

      private LongCache(){}
      static final Long cache[] = new Long[-(-128) + 127 + 1];
            static {
                for(int i = 0; i < cache.length; i++)
                   cache[i] = new Long(i - 128);
            }
    }
}
````

#### 不被缓存

float,double

### 小知识：boolean的字节数?

在Java中定义的八种基本数据类型中，除了其它七种类型都有明确的内存占用字节数外，就boolean类型没有给出具体的占用字节数，因为对虚拟机来说根本就不存在 boolean 这个类型，boolean类型在编译后会使用其他数据类型来表示，那boolean类型究竟占用多少个字节？

《Java虚拟机规范》一书中的描述：“虽然定义了boolean这种数据类型，但是只对它提供了非常有限的支持。在Java虚拟机中没有任何供boolean值专用的字节码指令，

1. Java语言表达式所操作的boolean值，在编译之后都使用Java虚拟机中的int(4字节)数据类型来代替

2. boolean数组将会被编码成Java虚拟机的byte数组，每个元素boolean元素占8位（1字节）”。

3. 这样我们可以得出boolean类型占了单独使用是4个字节，在数组中又是1个字节。

​		 显然第三条是更准确的说法，那虚拟机为什么要用int来代替boolean呢？为什么不用byte或short，这样不是更节省内存空间吗。大多数人都会很自然的这样去想，我同样也有这个疑问，经过查阅资料发现，使用int的原因是，对于当下32位的处理器（CPU）来说，一次处理数据是32位（这里不是指的是32/64位系统，而是指CPU硬件层面），具有高效存取的特点。

&nbsp;  最后的总结：布尔数据类型只有两个可能的值：真和假。使用此数据类型为跟踪真/假条件的简单标记。这种数据类型就表示这一点信息，但是它的“大小”并不是精确定义的。可以看出，boolean类型没有给出精确的定义，《Java虚拟机规范》给出了4个字节和boolean数组1个字节的定义，具体还要看虚拟机实现是否按照规范来，所以1个字节、4个字节都是有可能的。这其实是运算效率和存储空间之间的博弈，两者都非常的重要。

## 4.方法

### 4.1 重载和重写

**面试时，问：重载（Overload）和重写（Override）的区别？**

答：1. 重载和重写多态的体现，

​    2.区别在于前者实现的是编译时的多态性，而后者实现的是运行时的多态性。

​    3.重载发生在同一个类中，同名的方法如果有不同的参数列表（参数类型不同、参数个数不同或者二者都不同）则视为重载，对返回类型没有要求；

​    4.重写发生在子类与父类之间，方法除了方法体和返回类型（必须是兼容）不同外，其他都相同

重写要求子类被重写方法与父类被重写方法有相同的参数列表，有兼容的返回类型，比父类被重写方法更好访问，不能比父类被重写方法声明更多的异常（里氏代换原则）

|      | 修饰符                               | 返回值                        | 参数类型                    | 发生地点 |
| ---- | ------------------------------------ | ----------------------------- | --------------------------- | -------- |
| 重载 | 不作为参考条件                       | 不作为参考条件                | 个数 或者类型 或者顺序 不同 | 同一个类 |
| 重写 | 子类函数的访问修饰权限不能少于父类的 | 子类返回值的范围 小于等于父类 | z相同                       | 父子类   |

### 4.2 深拷贝与浅拷贝

#### 浅拷贝

浅拷贝：创建一个新对象，然后将当前对象的**非静态字段**复制到该新对象，如果字段是值类型的，那么对该字段执行复制；如果该字段是引用类型的话，则复制引用地址但不复制引用的对象。因此，原始对象及其副本引用同一个对象。

如下：p2是由p1深拷贝过来的

<img src="../../../Library/Application%20Support/typora-user-images/image-20210308224857522.png" alt="image-20210308224857522" style="zoom:50%;" />

#### 深拷贝

深拷贝：创建一个新对象，然后将当前对象的**非静态字段**复制到该新对象，无论该字段是值类型的还是引用类型，都复制独立的一份。当你修改其中一个对象的任何内容时，都不会影响另一个对象的内容。

如下：p2是由p1深拷贝过来的

<img src="../../../Library/Application%20Support/typora-user-images/image-20210308224928243.png" alt="image-20210308224928243" style="zoom:50%;" />



#### 如何实现深拷贝？

##### 让每个引用类型属性内部都重写clone() 方法

<img src="../../../Library/Application%20Support/typora-user-images/image-20210308225020430.png" alt="image-20210308225020430" style="zoom:50%;" />

<img src="../../../Library/Application%20Support/typora-user-images/image-20210308225042990.png" alt="image-20210308225042990" style="zoom:50%;" />

这种做法有个弊端，这里我们Father 类只有一个CopyAddress 引用类型，所以我们只用重写 Address 类的clone 方法，但是如果 CopyAddress 类内部也存在一个引用类型变量，那么我们也要重写其clone 方法，这样下去，有多少个引用类型，我们就要重写多少次，如果存在很多引用类型，那么代码量显然会很大，所以这种方法不太合适。

##### 序列化

&nbsp;  序列化是将对象写到流中便于传输，而反序列化则是把对象从流中读取出来。这里写到流中的对象则是原始对象的一个拷贝，因为原始对象还存在 JVM 中，所以我们可以利用对象的序列化产生克隆对象，然后通过反序列化获取这个对象。

&nbsp;  注意每个需要序列化的类都要实现 Serializable 接口，如果有某个属性不需要序列化，可以将其声明为 transient，即将其排除在克隆属性之外。

<img src="../../../Library/Application%20Support/typora-user-images/image-20210308225129412.png" alt="image-20210308225129412" style="zoom:70%;" />

##### FastJson

![image-20210308225202815](../../../Library/Application%20Support/typora-user-images/image-20210308225202815.png)

其中：

JSON.toJSONString(copyFather)就是在进行序列化

JSON.parseObject 就是在进行反序列化

# 二、面向对象



## 1.类和对象

### 1.1 成员变量和局部变量的区别

1. 从语法形式上看： 成员变量可以被 public,private,static 等修饰符所修饰，而局部变量不能被访问控制修饰符及 static 所修饰；但是，成员变量和局部变量都能被 final 所修饰。
2. 从变量在内存中的存储方式来看:如果成员变量是使用`static`修饰的，那么这个成员变量是属于类的，如果没有使用`static`修饰，这个成员变量是属于实例的。而对象存在于堆内存，局部变量则存在于栈内存。
3. 从变量在内存中的生存时间上看:成员变量是对象的一部分，它随着对象的创建而存在，而局部变量随着方法的调用而自动消失。
4. 成员变量如果没有被赋初值:则会自动以类型的默认值而赋值（一种情况例外:被 final 修饰的成员变量也必须显式地赋值），而局部变量则不会自动赋值。

## 2、面向对象

### 2.1 封装

​		封装是指把一个对象的状态信息（也就是属性）隐藏在对象内部，不允许外部对象直接访问对象的内部信息。但是可以提供一些可以被外界访问的方法来操作属性。

​		就好像我们看不到挂在墙上的空调的内部的零件信息（也就是属性），但是可以通过遥控器（方法）来控制空调。如果属性不想被外界访问，我们大可不必提供方法给外界访问。但是如果一个类没有提供给外界访问的方法，那么这个类也没有什么意义了。就好像如果没有空调遥控器，那么我们就无法操控空凋制冷，空调本身就没有意义了（当然现在还有很多其他方法 ，这里只是为了举例子）。

### 2.2 继承

​		不同类型的对象，相互之间经常有一定数量的共同点。

​		例如，小明同学、小红同学、小李同学，都共享学生的特性（班级、学号等）。同时，每一个对象还定义了额外的特性使得他们与众不同。例如小明的数学比较好，小红的性格惹人喜爱；小李的力气比较大。

​		继承是**使用已存在的类**的定义作为基础建立新类的技术，新类的定义可以增加新的数据或新的功能，也可以用父类的功能，但不能选择性地继承父类。通过使用继承，可以快速地创建新的类，可以提高代码的重用，程序的可维护性，节省大量创建新类的时间 ，提高我们的开发效率。

**关于继承如下 3 点请记住：**

1. 子类拥有父类对象所有的属性和方法（包括私有属性和私有方法），但是父类中的私有属性和方法子类是无法访问，**只是拥有**。
2. 子类可以拥有自己属性和方法，即子类可以对父类进行扩展。
3. 子类可以用自己的方式实现父类的方法。（重写）。

### 2.3 多态

多态，顾名思义，表示一个对象具有多种的状态。具体表现为父类的引用指向子类的实例。

**多态的特点:**

- 【<span style='color:red'>继承/实现</span>】对象类型和引用类型之间具有继承（类）/实现（接口）的关系；
- 【<span style='color:red'>运行时</span>】引用类型变量发出的方法调用的到底是哪个类中的方法，必须在程序运行期间才能确定；
- 多态不能调用“只在子类存在但在父类不存在”的方法；
- 【<span style='color:red'>重写</span>】如果子类重写了父类的方法，真正执行的是子类覆盖的方法，如果子类没有覆盖父类的方法，执行的是父类的方法。

## 3.其他知识点

### 3.1StringBuilder和StringBuffer

#### 可变性

简单的来说：`String` 类中使用 final 关键字修饰字符数组来保存字符串，`private final char value[]`，所以`String` 对象是不可变的。

> 补充（来自[issue 675](https://github.com/Snailclimb/JavaGuide/issues/675)）：在 Java 9 之后，String 、`StringBuilder` 与 `StringBuffer` 的实现改用 byte 数组存储字符串 `private final byte[] value`

而 `StringBuilder` 与 `StringBuffer` 都继承自 `AbstractStringBuilder` 类，在 `AbstractStringBuilder` 中也是使用字符数组保存字符串`char[]value` 但是没有用 `final` 关键字修饰，所以这两种对象都是可变的。

`StringBuilder` 与 `StringBuffer` 的构造方法都是调用父类构造方法也就是`AbstractStringBuilder` 实现的，大家可以自行查阅源码。

```java
AbstractStringBuilder.java

abstract class AbstractStringBuilder implements Appendable, CharSequence {
    /**
     * The value is used for character storage.
     */
    char[] value;

    /**
     * The count is the number of characters used.
     */
    int count;

    AbstractStringBuilder(int capacity) {
        value = new char[capacity];
    }}

```

#### 线程安全

线程安全：String、StringBuffer

线程不安全：StringBuilder

`String` 中的对象是不可变的，也就可以理解为常量，线程安全。`AbstractStringBuilder` 是 `StringBuilder` 与 `StringBuffer` 的公共父类，定义了一些字符串的基本操作，如 `expandCapacity`、`append`、`insert`、`indexOf` 等公共方法。`StringBuffer` 对方法加了同步锁或者对调用的方法加了同步锁，所以是线程安全的。`StringBuilder` 并没有对方法进行加同步锁，所以是非线程安全的。

#### 性能

每次对 `String` 类型进行改变的时候，都会生成一个新的 `String` 对象，然后将指针指向新的 `String` 对象。`StringBuffer` 每次都会对 `StringBuffer` 对象本身进行操作，而不是生成新的对象并改变对象引用。相同情况下使用 `StringBuilder` 相比使用 `StringBuffer` 仅能获得 10%~15% 左右的性能提升，但却要冒多线程不安全的风险。

**对于三者使用的总结：**

1. 操作少量的数据: 适用 `String`
2. 单线程操作字符串缓冲区下操作大量数据: 适用 `StringBuilder`
3. 多线程操作字符串缓冲区下操作大量数据: 适用 `StringBuffer`



### 3.2 Object类常见的方法

Object 类是一个特殊的类，是所有类的父类。它主要提供了以下 11 个方法：

```java
public final native Class<?> getClass()//native方法，用于返回当前运行时对象的Class对象，使用了final关键字修饰，故不允许子类重写。

public native int hashCode() //native方法，用于返回对象的哈希码，主要使用在哈希表中，比如JDK中的HashMap。
public boolean equals(Object obj)//用于比较2个对象的内存地址是否相等，String类对该方法进行了重写用户比较字符串的值是否相等。

protected native Object clone() throws CloneNotSupportedException//naitive方法，用于创建并返回当前对象的一份拷贝。一般情况下，对于任何对象 x，表达式 x.clone() != x 为true，x.clone().getClass() == x.getClass() 为true。Object本身没有实现Cloneable接口，所以不重写clone方法并且进行调用的话会发生CloneNotSupportedException异常。

public String toString()//返回类的名字@实例的哈希码的16进制的字符串。建议Object所有的子类都重写这个方法。

public final native void notify()//native方法，并且不能重写。唤醒一个在此对象监视器上等待的线程(监视器相当于就是锁的概念)。如果有多个线程在等待只会任意唤醒一个。

public final native void notifyAll()//native方法，并且不能重写。跟notify一样，唯一的区别就是会唤醒在此对象监视器上等待的所有线程，而不是一个线程。

public final native void wait(long timeout) throws InterruptedException//native方法，并且不能重写。暂停线程的执行。注意：sleep方法没有释放锁，而wait方法释放了锁 。timeout是等待时间。

public final void wait(long timeout, int nanos) throws InterruptedException//多了nanos参数，这个参数表示额外时间（以毫微秒为单位，范围是 0-999999）。 所以超时的时间还需要加上nanos毫秒。

public final void wait() throws InterruptedException//跟之前的2个wait方法一样，只不过该方法一直等待，没有超时时间这个概念

protected void finalize() throws Throwable { }//实例被垃圾回收器回收的时候触发的操作
```

### 3.3有些字段不想被序列化怎么办？

对于不想进行序列化的变量，使用 transient 关键字修饰。

transient 关键字的作用是：阻止实例中那些用此关键字修饰的的变量序列化；当对象被反序列化时，被 transient 修饰的变量值不会被持久化和恢复。transient 只能修饰变量，不能修饰类和方法。



# 三、Java核心技术

## 1.反射【待补充】

https://snailclimb.gitee.io/javaguide/#/docs/java/basis/%E5%8F%8D%E5%B0%84%E6%9C%BA%E5%88%B6

### 1.1 介绍反射

​		JAVA 反射机制是在**运行时状态中**，对于任意一个类，都能够知道这个类的**所有属性和方法**；对于任意一个对象，都能够调用它的任意一个方法和属性；这种动态获取的信息以及动态调用对象的方法的功能称为 java 语言的反射机制。

### 1.2获取Class对象的四种方式

如果我们动态获取到这些信息，我们需要依靠 Class 对象。Class 类对象将一个类的方法、变量等信息告诉运行的程序。Java 提供了四种方式获取 Class 对象:

#### 1.2.1 XXX.class

知道具体类的情况下可以使用：

```java
Class alunbarClass = TargetObject.class;
```

但是我们一般是不知道具体类的，基本都是通过遍历包下面的类来获取 Class 对象，通过此方式获取Class对象不会进行初始化

#### 1.2.2 Class.forName()

通过 `Class.forName()`传入类的路径获取：

```java
Class alunbarClass1 = Class.forName("cn.javaguide.TargetObject");
```

Class.forName(className)方法，内部实际调用的是一个native方法 forName0(className, true, ClassLoader.getClassLoader(caller), caller);

第2个boolean参数表示类是否需要初始化，Class.forName(className)默认是需要初始化。

一旦初始化，就会触发目标对象的 static块代码执行，static参数也会被再次初始化。

#### 1.2.3 instance.getClass()

通过对象实例`instance.getClass()`获取：

```java
Employee e = new Employee();
Class alunbarClass2 = e.getClass();
```

#### 1.2.4 类加载器`xxxClassLoader.loadClass()`

通过类加载器`xxxClassLoader.loadClass()`传入类路径获取

```java
class clazz = ClassLoader.LoadClass("cn.javaguide.TargetObject");
```

通过类加载器获取Class对象不会进行初始化，意味着不进行包括初始化等一些列步骤，静态块和静态对象不会得到执行



### 1.3 代码实例

1.创建一个我们要使用反射操作的类 `TargetObject`：

```java
package cn.javaguide;

public class TargetObject {
    private String value;
 		// 构造方法
    public TargetObject() {
        value = "JavaGuide";
    }
  	//自定义的公共方法
    public void publicMethod(String s) {
        System.out.println("I love " + s);
    }
  	//自定义的私有方法
    private void privateMethod() {
        System.out.println("value is " + value);
    }
}
```



### 1.4静态编译和动态编译

- **静态编译：** 在编译时确定类型，绑定对象
- **动态编译：** 运行时确定类型，绑定对象

### 1.5 反射机制优缺点

- **优点：** 运行期类型的判断，动态加载类，提高代码灵活度。
- **缺点：** 
  - ​    1、性能瓶颈：反射相当于一系列解释操作，通知 JVM 要做的事情，性能比直接的 java 代码要慢很多。
  - ​	2、安全问题，让我们可以动态操作改变类的属性同时也增加了类的安全隐患

### 1.6反射的应用场景

**反射是框架设计的灵魂。**

在我们平时的项目开发过程中，基本上很少会直接使用到反射机制，但这不能说明反射机制没有用，实际上有很多设计、开发都与反射机制有关，例如模块化的开发，通过反射去调用对应的字节码；动态代理设计模式也采用了反射机制，还有我们日常使用的 Spring／Hibernate 等框架也大量使用到了反射机制。

举例：

1. 我们在使用 JDBC 连接数据库时使用 `Class.forName()`通过反射加载数据库的驱动程序；
2. Spring 框架的 IOC（动态加载管理 Bean）创建对象以及 AOP（动态代理）功能都和反射有联系；
3. 动态配置实例的属性；

## 2. 异常

### 2.1异常类层次图

<img src="../../../Library/Application%20Support/typora-user-images/image-20210308232541303.png" alt="image-20210308232541303" style="zoom:50%;" />



![image-20210308232607046](../../../Library/Application%20Support/typora-user-images/image-20210308232607046.png)

​														RunTimeException属于非受检异常，IOException属于受检异常

在 Java 中，所有的异常都有一个共同的祖先 `java.lang` 包中的 `Throwable` 类。`Throwable` 类有两个重要的子类 `Exception`（异常）和 `Error`（错误）。`Exception` 能被程序本身处理(`try-catch`)， `Error` 是无法处理的(只能尽量避免)。

`Exception` 和 `Error` 二者都是 Java 异常处理的重要子类，各自都包含大量子类。

- **`Exception`** :程序本身可以处理的异常，可以通过 `catch` 来进行捕获。`Exception` 又可以分为 受检查异常(必须处理) 和 不受检查异常(可以不处理)。
- **`Error`** ：`Error` 属于程序无法处理的错误 ，我们没办法通过 `catch` 来进行捕获 。例如，Java 虚拟机运行错误（`Virtual MachineError`）、虚拟机内存不够错误(`OutOfMemoryError`)、类定义错误（`NoClassDefFoundError`）等 。这些异常发生时，Java 虚拟机（JVM）一般会选择线程终止。

#### 受检异常

Java 代码在编译过程中，如果受检查异常没有被 `catch`/`throw` 处理的话，就没办法通过编译 。比如下面这段 IO 操作的代码。

<img src="../../../Library/Application%20Support/typora-user-images/image-20210308232924586.png" alt="image-20210308232924586" style="zoom:100%;" />

除了`RuntimeException`及其子类以外，其他的`Exception`类及其子类都属于受检查异常 。

常见的受检查异常有： IO 相关的异常、`ClassNotFoundException` 、`SQLException`...。

#### 不受检异常

Java 代码在编译过程中 ，我们即使不处理不受检查异常也可以正常通过编译。

`RuntimeException` 及其子类都统称为非受检查异常，例如：`NullPointerException`、`NumberFormatException`（字符串转换为数字）、`ArrayIndexOutOfBoundsException`（数组越界）、`ClassCastException`（类型转换错误）、`ArithmeticException`（算术错误）等。

### 2.2 Throwble类常用方法

- **`public string getMessage()`**:返回异常发生时的简要描述
- **`public string toString()`**:返回异常发生时的详细信息
- **`public string getLocalizedMessage()`**:返回异常对象的本地化信息。使用 `Throwable` 的子类覆盖这个方法，可以生成本地化信息。如果子类没有覆盖该方法，则该方法返回的信息与 `getMessage（）`返回的结果相同
- **`public void printStackTrace()`**:在控制台上打印 `Throwable` 对象封装的异常信息

### 2.3 try-catch-finally

- **`try`块：** 用于捕获异常。其后可接零个或多个 `catch` 块，如果没有 `catch` 块，则必须跟一个 `finally` 块。
- **`catch`块：** 用于处理 try 捕获到的异常。
- **`finally` 块：** 无论是否捕获或处理异常，`finally` 块里的语句都会被执行。当在 `try` 块或 `catch` 块中遇到 `return` 语句时，`finally` 语句块将在方法返回之前被执行。

**在以下 3 种特殊情况下，`finally` 块不会被执行：**

1. 在 `try` 或 `finally `块中用了 `System.exit(int)`退出程序。但是，如果 `System.exit(int)` 在异常语句之后，`finally` 还是会被执行
2. 程序所在的线程死亡。
3. 关闭 CPU。

下面这部分内容来自 issue:https://github.com/Snailclimb/JavaGuide/issues/190。

**注意：** 当 try 语句和 finally 语句中都有 return 语句时，在方法返回之前，finally 语句的内容将被执行，并且 finally 语句的返回值将会覆盖原始的返回值。如下：

```java
public class Test {
    public static int f(int value) {
        try {
            return value * value;
        } finally {
            if (value == 2) {
                return 0;
            }
        }
    }
}
```

如果调用 `f(2)`，返回值将是 0，因为 finally 语句的返回值覆盖了 try 语句块的返回值。

### 2.4 try-with-resources

1. **适用范围（资源的定义）：** 任何实现 `java.lang.AutoCloseable`或者 `java.io.Closeable` 的对象
2. **关闭资源和 final 的执行顺序：** 在 `try-with-resources` 语句中，任何 catch 或 finally 块在声明的资源关闭后运行

《Effecitve Java》中明确指出：

> 面对必须要关闭的资源，我们总是应该优先使用 `try-with-resources` 而不是`try-finally`。随之产生的代码更简短，更清晰，产生的异常对我们也更有用。`try-with-resources`语句让我们更容易编写必须要关闭的资源的代码，若采用`try-finally`则几乎做不到这点。

Java 中类似于`InputStream`、`OutputStream` 、`Scanner` 、`PrintWriter`等的资源都需要我们调用`close()`方法来手动关闭，一般情况下我们都是通过`try-catch-finally`语句来实现这个需求，如下：

```java
        //读取文本文件的内容
        Scanner scanner = null;
        try {
            scanner = new Scanner(new File("D://read.txt"));
            while (scanner.hasNext()) {
                System.out.println(scanner.nextLine());
            }
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } finally {
            if (scanner != null) {
                scanner.close();
            }
        }

```

使用 Java 7 之后的 `try-with-resources` 语句改造上面的代码:

```java
try (Scanner scanner = new Scanner(new File("test.txt"))) {
    while (scanner.hasNext()) {
        System.out.println(scanner.nextLine());
    }
} catch (FileNotFoundException fnfe) {
    fnfe.printStackTrace();
}

```

当然多个资源需要关闭的时候，使用 `try-with-resources` 实现起来也非常简单，如果你还是用`try-catch-finally`可能会带来很多问题。

通过使用分号分隔，可以在`try-with-resources`块中声明多个资源。

```java
try (BufferedInputStream bin = new BufferedInputStream(new FileInputStream(new File("test.txt")));
             BufferedOutputStream bout = new BufferedOutputStream(new FileOutputStream(new File("out.txt")))) {
            int b;
            while ((b = bin.read()) != -1) {
                bout.write(b);
            }
        }
        catch (IOException e) {
            e.printStackTrace();
        }
```

## 3.文件与IO流

### 3.1 IO流分为几种？

- 按照流的流向分，可以分为输入流和输出流；
- 按照操作单元划分，可以划分为字节流和字符流；
- 按照流的角色划分为节点流和处理流。

Java Io 流共涉及 40 多个类，这些类看上去很杂乱，但实际上很有规则，而且彼此之间存在非常紧密的联系， Java I0 流的 40 多个类都是从如下 4 个抽象类基类中派生出来的。

- InputStream/Reader: 所有的输入流的基类，前者是字节输入流，后者是字符输入流。
- OutputStream/Writer: 所有输出流的基类，前者是字节输出流，后者是字符输出流。

按操作方式分类结构图：

![image-20210308233504412](../../../Library/Application%20Support/typora-user-images/image-20210308233504412.png)



按操作对象分类结构图：

![image-20210308233557782](../../../Library/Application%20Support/typora-user-images/image-20210308233557782.png)

### 3.2 既然有了字节流,为什么还要有字符流?

​		问题本质想问：**不管是文件读写还是网络发送接收，信息的最小存储单元都是字节，那为什么 I/O 流操作要分为字节流操作和字符流操作呢？**

​		回答：字符流是由JVM 将字节转换得到的，问题就出在这个过程还算是非常耗时，并且，如果我们不知道编码类型就很容易出现乱码问题。所以， I/O 流就干脆提供了一个直接操作字符的接口，方便我们平时对字符进行流操作。如果音频文件、图片等媒体文件用字节流比较好，如果涉及到字符的话使用字符流比较好。

### 3.3 BIO,NIO,AIO 有什么区别?

> 京东数科二面：https://www.cnblogs.com/javaguide/p/io.html

> 图解NIO：https://www.pianshen.com/article/3674739166/

> **需要参考以上两个链接，补充该模块知识**

#### IO

I/O（**I**nput/**O**utpu） 即**输入／输出** 。

**我们先从计算机结构的角度来解读一下 I/O。**

根据冯.诺依曼结构，计算机结构分为 5 大部分：运算器、控制器、存储器、输入设备、输出设备。

<img src="../../../Library/Application%20Support/typora-user-images/image-20210309112248658.png" alt="image-20210309112248658" style="zoom: 50%;" />

输入设备（比如键盘）和输出设备（比如鼠标）都属于外部设备。网卡、硬盘这种既可以属于输入设备，也可以属于输出设备。

输入设备向计算机输入数据，输出设备接收计算机输出的数据。

**从计算机结构的视角来看的话， I/O 描述了计算机系统与外部设备之间通信的过程。**

**我们再先从应用程序的角度来解读一下 I/O。**

根据大学里学到的操作系统相关的知识：为了保证操作系统的稳定性和安全性，一个进程的地址空间划分为 **用户空间（User space）** 和 **内核空间（Kernel space ）** 。

像我们平常运行的应用程序都是运行在用户空间，只有内核空间才能进行系统态级别的资源有关的操作，比如如文件管理、进程通信、内存管理等等。也就是说，我们想要进行 IO 操作，一定是要依赖内核空间的能力。

并且，用户空间的程序不能直接访问内核空间。

当想要执行 IO 操作时，由于没有执行这些操作的权限，只能发起系统调用请求操作系统帮忙完成。

因此，用户进程想要执行 IO 操作的话，必须通过 **系统调用** 来间接访问内核空间

我们在平常开发过程中接触最多的就是 **磁盘 IO（读写文件）** 和 **网络 IO（网络请求和相应）**。

**从应用程序的视角来看的话，我们的应用程序对操作系统的内核发起 IO 调用（系统调用），操作系统负责的内核执行具体的 IO 操作。也就是说，我们的应用程序实际上只是发起了 IO 操作的调用而已，具体 IO 的执行是由操作系统的内核来完成的。**

当应用程序发起 I/O 调用后，会经历两个步骤：

1. 内核等待 I/O 设备准备好数据
2. 内核将数据从内核空间拷贝到用户空间。

##### 有哪些常见的 IO 模型?

UNIX 系统下， IO 模型一共有 5 种： **同步阻塞 I/O**、**同步非阻塞 I/O**、**I/O 多路复用**、**信号驱动 I/O** 和**异步 I/O**。

这也是我们经常提到的 5 种 IO 模型。



#### 3.3.1 Java 中 3 种常见 IO 模型一：BIO

##### 简介

**BIO (Blocking I/O):** 同步阻塞 I/O 模式，数据的读取写入必须阻塞在一个线程内等待其完成。在活动连接数不是特别高（小于单机 1000）的情况下，这种模型是比较不错的，可以让每一个连接专注于自己的 I/O 并且编程模型简单，也不用过多考虑系统的过载、限流等问题。线程池本身就是一个天然的漏斗，可以缓冲一些系统处理不了的连接或请求。但是，当面对十万甚至百万级连接的时候，传统的 BIO 模型是无能为力的。因此，我们需要一种更高效的 I/O 处理模型来应对更高的并发量。

##### 原理

同步阻塞 IO 模型中，应用程序发起 read 调用后，会一直阻塞，直到在内核把数据拷贝到用户空间。

<img src="../../../Library/Application%20Support/typora-user-images/image-20210309113009380.png" alt="image-20210309113009380" style="zoom:50%;" />

​		在客户端连接数量不高的情况下，是没问题的。但是，当面对十万甚至百万级连接的时候，传统的 BIO 模型是无能为力的。因此，我们需要一种更高效的 I/O 处理模型来应对更高的并发量。

#### 3.3.2 Java 中 3 种常见 IO 模型二：NIO

##### 简介

**NIO (Non-blocking/New I/O):** NIO 是一种同步非阻塞的 I/O 模型，在 Java 1.4 中引入了 NIO 框架，对应 java.nio 包，提供了 Channel , Selector，Buffer 等抽象。NIO 中的 N 可以理解为 Non-blocking，不单纯是 New。它支持面向缓冲的，基于通道的 I/O 操作方法。 NIO 提供了与传统 BIO 模型中的 `Socket` 和 `ServerSocket` 相对应的 `SocketChannel` 和 `ServerSocketChannel` 两种不同的套接字通道实现,两种通道都支持阻塞和非阻塞两种模式。阻塞模式使用就像传统中的支持一样，比较简单，但是性能和可靠性都不好；非阻塞模式正好与之相反。对于低负载、低并发的应用程序，可以使用同步阻塞 I/O 来提升开发速率和更好的维护性；对于高负载、高并发的（网络）应用，应使用 NIO 的非阻塞模式来开发

##### NIO结构

Channel：通道，连接客户端和服务端的一个管道，管道内可以双向传输数据。

Selector：选择器，可以想象成一个环状传送带，上面可以接入很多管道，selector还可以对每个管道设置感兴趣的颜色（连接（红色），读（黄色），写（蓝色），接收数据）。当Selector开始轮询的时候Selector这个传送带就一直转动，当某个管道被传送到感兴趣事件检查点的时候，selector会检查改管道当前颜色（即事件）之前是否被注册成了感兴趣颜色（事件），如果感兴趣，那么Selector就可以对这个管道做处理了，比如把管道传给别的线程，让别的线程完成读写操作。

ByteBuffer：字节缓冲区，本质上是一个连续的字节数组，Selector感兴趣的事件发生后对管道的读操作所读到的数据都存储在ByteBuffer中，而对管道的写操作也是以ByteBuffer为源头，值得注意的是ByteBuffer可以有多个。想想看，我们使用的所有基本类型都可以转换成byte字节，比如Integer类型占4字节，那么传递数字 1 ByteBuffer底层数组被占用了4个格子。

<img src="../../../Library/Application%20Support/typora-user-images/image-20210309114900874.png" alt="image-20210309114900874" style="zoom:50%;" />

##### 与传统IO比较

1：传统IO一般是一个线程等待连接，连接过来之后分配给processor线程，processor线程与通道连接后如果通道没有数据过来就会阻塞（线程被动挂起）不能做别的事情。NIO则不同，首先：在Selector线程轮询的过程中就已经过滤掉了不感兴趣的事件，其次：在processor处理感兴趣事件的read和write都是非阻塞操作即直接返回的，线程没有被挂起。

2：传统IO的管道是单向的，NIO的管道是双向的

3：两者都是同步的，也就是Java程序亲力亲为的去读写数据，不管传统IO还是NIo都需要read和write方法，这些都是Java程序调用的而不是系统帮我们调用的。NIO2.0里这点得到了改观，即使用异步非阻塞AsynchronousXXX四个类来处理。

##### 原理

<img src="../../../Library/Application%20Support/typora-user-images/image-20210309113048739.png" alt="image-20210309113048739" style="zoom:50%;" />

同步非阻塞 IO 模型中，应用程序会一直发起 read 调用，等待数据从内核空间拷贝到用户空间的这段时间里，线程依然是阻塞的，直到在内核把数据拷贝到用户空间。

相比于同步阻塞 IO 模型，同步非阻塞 IO 模型确实有了很大改进。通过轮询操作，避免了一直阻塞。

但是，这种 IO 模型同样存在问题：**应用程序不断进行 I/O 系统调用轮询数据是否已经准备好的过程是十分消耗 CPU 资源的。**

这个时候，**I/O 多路复用模型** 就上场了。

<img src="../../../Library/Application%20Support/typora-user-images/image-20210309113126365.png" alt="image-20210309113126365" style="zoom:50%;" />



IO 多路复用模型中，线程首先发起 select 调用，询问内核数据是否准备就绪，等内核把数据准备好了，用户线程再发起 read 调用。read 调用的过程（数据从内核空间->用户空间）还是阻塞的。

> 目前支持 IO 多路复用的系统调用，有 select，epoll 等等。select 系统调用，是目前几乎在所有的操作系统上都有支持
>
> - **select 调用** ：内核提供的系统调用，它支持一次查询多个系统调用的可用状态。几乎所有的操作系统都支持。
> - **epoll 调用** ：linux 2.6 内核，属于 select 调用的增强版本，优化了 IO 的执行效率。

**IO 多路复用模型，通过减少无效的系统调用，减少了对 CPU 资源的消耗。**

Java 中的 NIO ，有一个非常重要的**选择器 ( Selector )** 的概念，也可以被称为 **多路复用器**。通过它，只需要一个线程便可以管理多个客户端连接。当客户端数据到了之后，才会为其服务。

<img src="../../../Library/Application%20Support/typora-user-images/image-20210309113204212.png" alt="image-20210309113204212" style="zoom:100%;" />

#### 3.3.3 Java 中 3 种常见 IO 模型三：AIO

##### 简介

**AIO (Asynchronous I/O):** AIO 也就是 NIO 2。在 Java 7 中引入了 NIO 的改进版 NIO 2,它是异步非阻塞的 IO 模型。异步 IO 是基于事件和回调机制实现的，也就是应用操作之后会直接返回，不会堵塞在那里，当后台处理完成，操作系统会通知相应的线程进行后续的操作。AIO 是异步 IO 的缩写，虽然 NIO 在网络操作中，提供了非阻塞的方法，但是 NIO 的 IO 行为还是同步的。对于 NIO 来说，我们的业务线程是在 IO 操作准备好时，得到通知，接着就由这个线程自行进行 IO 操作，IO 操作本身是同步的。查阅网上相关资料，我发现就目前来说 AIO 的应用还不是很广泛，Netty 之前也尝试使用过 AIO，不过又放弃了。

##### 原理

AIO 也就是 NIO 2。Java 7 中引入了 NIO 的改进版 NIO 2,它是异步 IO 模型。

异步 IO 是基于事件和回调机制实现的，也就是应用操作之后会直接返回，不会堵塞在那里，当后台处理完成，操作系统会通知相应的线程进行后续的操作。

<img src="../../../Library/Application%20Support/typora-user-images/image-20210309113239354.png" alt="image-20210309113239354" style="zoom:50%;" />

​		目前来说 AIO 的应用还不是很广泛。Netty 之前也尝试使用过 AIO，不过又放弃了。这是因为，Netty 使用了 AIO 之后，在 Linux 系统上的性能并没有多少提升。

#### 总结

![image-20210309113313249](../../../Library/Application%20Support/typora-user-images/image-20210309113313249.png)



## 4.枚举【待补充】

https://snailclimb.gitee.io/javaguide/#/docs/java/basis/%E7%94%A8%E5%A5%BDJava%E4%B8%AD%E7%9A%84%E6%9E%9A%E4%B8%BE%E7%9C%9F%E7%9A%84%E6%B2%A1%E6%9C%89%E9%82%A3%E4%B9%88%E7%AE%80%E5%8D%95

### 4.1概览

enum关键字在 java5 中引入，表示一种特殊类型的类，其总是继承java.lang.Enum类，更多内容可以自行查看其[官方文档](https://docs.oracle.com/javase/6/docs/api/java/lang/Enum.html)。

枚举在很多时候会和常量拿来对比，可能因为本身我们大量实际使用枚举的地方就是为了替代常量。那么这种方式由什么优势呢？

**以这种方式定义的常量使代码更具可读性，允许进行编译时检查，预先记录可接受值的列表，并避免由于传入无效值而引起的意外行为。**

下面示例定义一个简单的枚举类型 pizza 订单的状态，共有三种 ORDERED, READY, DELIVERED状态:

```java
package shuang.kou.enumdemo.enumtest;

public enum PizzaStatus {
    ORDERED,
    READY, 
    DELIVERED; 
}
```

**简单来说，我们通过上面的代码避免了定义常量，我们将所有和 pizza 订单的状态的常量都统一放到了一个枚举类型里面。**

```java
System.out.println(PizzaStatus.ORDERED.name());//ORDERED
System.out.println(PizzaStatus.ORDERED);//ORDERED
System.out.println(PizzaStatus.ORDERED.name().getClass());//class java.lang.String
System.out.println(PizzaStatus.ORDERED.getClass());//class shuang.kou.enumdemo.enumtest.PizzaStatus
```

### 4.2 比较枚举类型使用==

原因：

1. JVM确保枚举类型仅存在一个常量实例，因此可以安全的使用`==`

2. `==` 运算符可提供编译时和运行时的安全性

   2.1 : `==` 运算符可提供运行时的安全性

````java
Pizza.PizzaStatus pizza = null;
System.out.println(pizza.equals(Pizza.PizzaStatus.DELIVERED));//运行时会抛出 空指针异常
System.out.println(pizza == Pizza.PizzaStatus.DELIVERED);//运行时不会抛异常，而是正常返回false
````

​		2.2 : `==` 运算符可提供编译时的安全性

````java
if (Pizza.PizzaStatus.DELIVERED.equals(TestColor.GREEN)); // 编译正常
if (Pizza.PizzaStatus.DELIVERED == TestColor.GREEN);      // 编译阶段就会失败，类型不匹配
````

### 4.3switch中使用枚举类型

```java
public int getDeliveryTimeInDays() {
    switch (status) {
        case ORDERED:
            return 5;
        case READY:
            return 2;
        case DELIVERED:
            return 0;
    }
    return 0;
}
```

# 四、Java新特性

