# Java 基础

## 1. 分析线程池的实现原理和线程的调度过程

### 1.1 什么时候使用线程池？

- 单个任务处理时间比较短
- 需要处理的任务数量很大

### 1.2 使用线程池的好处

引用自 [http://ifeve.com/java-threadpool/](http://ifeve.com/java-threadpool/) 的说明：

- **降低资源消耗** 。通过重复利用已创建的线程降低线程创建和销毁造成的消耗。
- **提高响应速度** 。当任务到达时，任务可以不需要的等到线程创建就能立即执行。
- **提高线程的可管理性** 。线程是稀缺资源，如果无限制的创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一的分配，调优和监控。

### 1.3 ThreadPoolExecutor

![image](http://static.lovedata.net/jpg/2018/5/28/27abf026c12c631217100176ef0d7528.jpg)

![image](http://static.lovedata.net/jpg/2018/5/28/ec7340130d0fb9cf76247972c89e73eb.jpg)

参考

1. [探秘线程池 ThreadPoolExecutor 的任务调度过程 | 指间生活](http://www.zhenchao.org/2017/08/31/java-thread-pool-executor/)
2. [深入理解 Java 线程池：ThreadPoolExecutor - 后端 - 掘金](https://juejin.im/entry/58fada5d570c350058d3aaad)
3. [聊聊并发（三）Java线程池的分析和使用 | 并发编程网 – ifeve.com](http://ifeve.com/java-threadpool/)

## 2. 动态代理的几种方式

## 3. Spring AOP与IOC的实现

## 4. Dubbo的底层实现原理和机制，

## 5. 接口的幂等性的概念

1. **错误定义** ：幂等性是指重复使用 **同样的参数** 调用同一方法时总能获得 **同样的结果** 。比如对同一资源的GET请求访问结果都是一样的。
2. GET方法是向服务器查询，不会对系统产生副作用，具有幂等性（不代表每次请求都是相同的结果)
3. **HTTP的幂等性** 指的是一次和多次请求某一个资源应该具有相同的副作用。如通过PUT接口将数据的Status置为1，无论是第一次执行还是多次执行，获取到的结果应该是相同的，即执行完成之后Status =1。
4. 随着分布式系统及微服务的普及，因为网络原因而导致调用系统未能获取到确切的结果从而导致重试，这就需要被调用系统具有幂等性。
5. 定义： **幂等性是系统的接口对外一种承诺(而不是实现),** 承诺只要调用接口成功, 外部多次调用对系统的影响是一致的. 声明为幂等的接口会认为外部调用失败是常态, **并且失败之后必然会有重试.**
6. 解决办法
    1. 美团GTIS，通过Tair生成全局的业务id，在执行前和执行后进行判断
        1. ![image](http://static.lovedata.net/jpg/2018/5/28/4fbdeeef48fed51ed59db8109cbf494e.jpg)
7. 参考
    1. [分布式系统接口幂等性 - BruceFeng](https://blog.brucefeng.info/post/api-idempotent)
    2. [接口的幂等性 - 简书](https://www.jianshu.com/p/b09a2e9bcd29)
    3. [分布式系统互斥性与幂等性问题的分析与解决 -](https://tech.meituan.com/distributed-system-mutually-exclusive-idempotence-cerberus-gtis.html)

## 6. Synchronized和Lock的区别

[死磕Java并发：深入分析synchronized的实现原理](https://mp.weixin.qq.com/s/wHz0uL_LEe4OgLsSFGEZEg)

## 7. HashMap的原理和并发问题

### 7.1 原理

[HashMap实现原理分析 - CSDN博客](http://blog.csdn.net/vking_wang/article/details/14166593)

### 7.2 并发问题

## 8. 了解LinkedHashMap的应用吗

## 9. 线程池实现原理，Lock机制的实现

## 10. ConcurrentHashMap深入分析

### 10.1 concurrenthashmap怎么实习同步？各个版本的实现方案？

## 11. callable runnable 区别；

## 12. 进程线程区别；

## 13. hashMap和treeMap的区别，以及实现；

## 14. ByteBuffer的原理和使用

## 15. java的重载、重写、覆盖

![image](http://static.lovedata.net/jpg/2018/6/22/b1ac6f6922a609e910e0ed608f45a3dd.jpg)
![image](http://static.lovedata.net/jpg/2018/6/22/8484c892ff673e69d15a29646a020916.jpg)

方法的重写(Overriding)和重载(Overloading)是java多态性的不同表现，重写是父类与子类之间多态性的一种表现，重载可以理解成多态的具体表现形式。

### 15.1 重载

[Java 实例 – 方法重载 | 菜鸟教程](http://www.runoob.com/java/method-overloading.html)

重载(overloading) 是在一个类里面，方法名字相同，而参数不同。返回类型可以相同也可以不同。

每个重载的方法（或者构造函数）都必须有一个独一无二的参数类型列表。
最常用的地方就是构造器的重载。

如果有两个方法的方法名相同，但参数不一致，哪么可以说一个方法是另一个方法的重载。 具体说明如下：

- 方法名相同
- 方法的参数类型，参数个不一样
- 方法的返回类型可以不相同
- 方法的修饰符可以不相同
- main 方法也可以被重载

重载规则:

- 被重载的方法必须改变参数列表(参数个数或类型不一样)；
- 被重载的方法可以改变返回类型；
- 被重载的方法可以改变访问修饰符；
- 被重载的方法可以声明新的或更广的检查异常；
- 方法能够在同一个类中或者在一个子类中被重载。
- 无法以返回值类型作为重载函数的区分标准。

### 15.2 覆盖（重写）

重写是子类对父类的允许访问的方法的实现过程进行重新编写, 返回值和形参都不能改变。即外壳不变，核心重写！

重写方法不能抛出新的检查异常或者比被重写方法申明更加宽泛的异常   父类的一个方法申明了一个检查异常 IOException，但是在重写这个方法的时候不能抛出 Exception 异常，因为 Exception 是 IOException 的父类，只能抛出 IOException 的子类异常。

方法的重写规则

- 参数列表必须完全与被重写方法的 **相同；**
- 返回类型必须完全与被重写方法的返回类型 **相同；**
- 访问权限不能比父类中被重写的方法的访问权限更低。例如：如果父类的一个方法被声明为public，那么在子类中重写该方法就不能声明为protected。
- 父类的成员方法只能被它的子类重写。
- **声明为final的方法不能被重写。**
- **声明为static的方法不能被重写，但是能够被再次声明。**
- 子类和父类在同一个包中，那么子类可以重写父类所有方法，除了声明为private和final的方法。
- 子类和父类不在同一个包中，那么子类只能够重写父类的声明为public和protected的非final方法。
- 重写的方法能够抛出任何非强制异常，无论被重写的方法是否抛出异常。但是，重写的方法不能抛出新的强制性异常，或者比被重写方法声明的更广泛的强制性异常，反之则可以。
- 构造方法不能被重写。
- 如果不能继承一个方法，则不能重写这个方法。

[Java 重写(Override)与重载(Overload) | 菜鸟教程](http://www.runoob.com/java/java-override-overload.html)

## 16. Exception和Error有什么区别？

[欢迎回来](https://app.yinxiang.com/shard/s19/nl/3013102/138ba717-51ee-4052-abe3-eac3f4390ad0)

Exception 和 Error 都是继承了 Throwable 类，在 Java 中只有 Throwable 类型的实例才可以被抛出（throw）或者捕获（catch），它是异常处理机制的基本组成类型。

Exception 和 Error 体现了 Java 平台设计者对不同异常情况的分类。

Exception 是程序正常运行中，可以预料的意外情况，可能并且应该被捕获，进行相应处理。
Error 是指在正常情况下，不大可能出现的情况，绝大部分的 Error 都会导致程序（比如 JVM 自身）处于非正常的、不可恢复状态。既然是非正常情况，所以不便于也不需要捕获，常见的比如 OutOfMemoryError 之类，都是 Error 的子类。

Exception 又分为可检查（checked）异常和不检查（unchecked）异常，可检查异常在源代码里必须显式地进行捕获处理，这是编译期检查的一部分。
前面我介绍的不可查的 Error，是 Throwable 不是 Exception。
不检查异常就是所谓的运行时异常，类似 **NullPointerException、ArrayIndexOutOfBoundsException** 之类，通常是可以编码避免的逻辑错误，具体根据需要来判断是否需要捕获，并不会在编译期强制要求。

Checked Exception，因为这种类型设计的初衷更是为了从异常情况恢复，作为异常设计者，我们往往有充足信息进行分类

从性能角度来审视一下 Java 的异常处理机制，这里有两个可能会相对昂贵的地方：

try-catch 代码段会产生额外的性能开销，或者换个角度说，它往往会影响 JVM 对代码进行优化，所以建议仅捕获有必要的代码段，尽量不要一个大的 try 包住整段的代码；与此同时，利用异常控制代码流程，也不是一个好主意，远比我们通常意义上的条件语句（if/else、switch）要低效。

Java 每实例化一个 Exception，都会对当时的栈进行快照，这是一个相对比较重的操作。如果发生的非常频繁，这个开销可就不能被忽略了



![image](http://static.lovedata.net/jpg/2018/7/3/0b7956b3f218e27a2f07ac5c8c95d6d3.jpg)

## 17. NoClassDefFoundError 和 ClassNotFoundException 有什么区别

[关于NoClassDefFoundError和ClassNotFoundException异常 - Think Different - ITeye博客](http://wxl24life.iteye.com/blog/1919359)

[ClassNotFoundException和NoClassDefFoundError的区别 - 三郎 - 开源中国](https://my.oschina.net/jasonultimate/blog/166932)


## 18 强引用、软引用、弱引用、幻象引用有什么区别？

强引用（"Strong" Reference），就是我们最常见的普通对象引用，只要还有强引用指向一个对象，就能表明对象还“活着”，垃圾收集器不会碰这种对象

软引用（SoftReference），是一种相对强引用弱化一些的引用，可以让对象豁免一些垃圾收集，只有当 JVM 认为内存不足时，才会去试图回收软引用指向的对象。JVM 会确保在抛出 OutOfMemoryError 之前，清理软引用指向的对象。 缓存

弱引用（WeakReference）并不能使对象豁免垃圾收集，仅仅是提供一种访问在弱引用状态下对象的途径。这就可以用来构建一种没有特定约束的关系，比如，维护一种非强制性的映射关系，如果试图获取时对象还在，就使用它，否则重现实例化。它同样是很多缓存实现的选择

![image](http://static.lovedata.net/jpg/2018/7/3/53fd388e134a22f71ed12e1daa60db36.jpg)

## 19.理解 Java 的字符串，String、StringBuffer、StringBuilder 有什么区别？

String 是 Java 语言非常基础和重要的类，提供了构造和管理字符串的各种基本逻辑。它是典型的 Immutable 类，被声明成为 final class，所有属性也都是 final 的。也由于它的不可变性，类似拼接、裁剪字符串等动作，都会产生新的 String 对象。由于字符串操作的普遍性，所以相关操作的效率往往对应用性能有明显影响。
StringBuffer 是为解决上面提到拼接产生太多中间对象的问题而提供的一个类，它是 Java 1.5 中新增的，我们可以用 append 或者 add 方法，把字符串添加到已有序列的末尾或者指定位置。StringBuffer 本质是一个线程安全的可修改字符序列，它保证了线程安全，也随之带来了额外的性能开销，所以除非有线程安全的需要，不然还是推荐使用它的后继者，也就是 StringBuilder。
StringBuilder 在能力上和 StringBuffer 没有本质区别，但是它去掉了线程安全的部分，有效减小了开销，是绝大部分情况下进行字符串拼接的首选。

为了实现修改字符序列的目的，StringBuffer 和 StringBuilder 底层都是利用可修改的（char，JDK 9 以后是 byte）数组，二者都继承了 AbstractStringBuilder，里面包含了基本操作，区别仅在于最终的方法是否加了 synchronized。

另外，这个内部数组应该创建成多大的呢？如果太小，拼接的时候可能要重新创建足够大的数组；如果太大，又会浪费空间。目前的实现是，构建时初始字符串长度加 16（这意味着，如果没有构建对象时输入最初的字符串，那么初始值就是 16）。我们如果确定拼接会发生非常多次，而且大概是可预计的，那么就可以指定合适的大小，避免很多次扩容的开销。扩容会产生多重开销，因为要抛弃原有数组，创建新的（可以简单认为是倍数）数组，还要进行 arraycopy。

你可以看到，在 JDK 8 中，字符串拼接操作会自动被 javac 转换为 StringBuilder 操作，而在 JDK 9 里面则是因为 Java 9 为了更加统一字符串操作优化，提供了 StringConcatFactory，作为一个统一的入口。javac 自动生成的代码，虽然未必是最优化的，但普通场景也足够了，你可以酌情选择。

## 20.  动态代理是基于什么原理？

那么，如何分类 Java 语言呢？通常认为，Java **是静态的强类型语言，但是因为提供了类似反射等机制** ，也具备了部分动态类型语言的能力。

反射机制是 Java 语言提供的一种基础功能，赋予程序在运行时自省（introspect，官方用语）的能力。通过反射我们可以直接操作类或者对象，比如获取某个对象的类定义，获取类声明的属性和方法，调用方法或者构造对象，甚至可以运行时修改类定义。
动态代理是一种方便运行时动态构建代理、动态处理代理方法调用的机制，很多场景都是利用类似机制做到的，比如用来包装 RPC 调用、面向切面的编程（AOP）。
实现动态代理的方式很多，比如 JDK 自身提供的动态代理，就是主要利用了上面提到的反射机制。还有其他的实现方式，比如利用传说中更高性能的字节码操作机制，类似 ASM、cglib（基于 ASM）、Javassist 等。

反射，它就像是一种魔法，引入运行时自省能力，赋予了 Java 语言令人意外的活力，通过运行时操作元数据或对象，Java 可以灵活地操作运行时才能确定的信息。而动态代理，则是延伸出来的一种广泛应用于产品开发中的技术，很多繁琐的重复编程，都可以被动态代理机制优雅地解决。

```java
public class MyDynamicProxy {
    public static void main (String[] args) {
        HelloImpl hello = new HelloImpl();
        MyInvocationHandler handler = new MyInvocationHandler(hello);
        // 构造代码实例
        Hello proxyHello = (Hello) Proxy.newProxyInstance(HelloImpl.class.getClassLoader(), HelloImpl.class.getInterfaces(), handler);
        // 调用代理方法
        proxyHello.sayHello();
    }
    }
    interface Hello {
        void sayHello();
    }
    class HelloImpl implements Hello {
    @Override
    public void sayHello() {
     System.out.println("Hello World");
    }
    }
    class MyInvocationHandler implements InvocationHandler {
        private Object target;
        public MyInvocationHandler(Object target) {
        this.target = target;
    }
        @Override
        public Object invoke(Object proxy, Method method, Object[] args)
        throws Throwable {
            System.out.println("Invoking sayHello");
            Object result = method.invoke(target, args);
            return result;
    }
}
```

cglib 动态代理采取的是创建目标类的子类的方式，因为是子类化，我们可以达到近似使用被调用者本身的效果。在 Spring 编程中，框架通常会处理这种情况，当然我们也可以显式指定。关于类似方案的实现细节，我就不再详细讨论了。

JDK Proxy 的优势：

- 最小化依赖关系，减少依赖意味着简化开发和维护，JDK 本身的支持，可能比 cglib 更加可靠。
- 平滑进行 JDK 版本升级，而字节码类库通常需要进行更新以保证在新版 Java 上能够使用。
- 代码实现简单。

基于类似 cglib 框架的优势：

- 有的时候调用目标可能不便实现额外接口，从某种角度看，限定调用者实现接口是有些侵入性的实践，类似 cglib 动态代理就没有这种限制。
- 只操作我们关心的类，而不必为其他相关类增加工作量。
- 高性能。

我们在选型中，性能未必是唯一考量，可靠性、可维护性、编程工作量等往往是更主要的考虑因素，毕竟标准类库和反射编程的门槛要低得多，代码量也是更加可控的，如果我们比较下不同开源项目在动态代理开发上的投入，也能看到这一点。

AOP

![image](http://static.lovedata.net/jpg/2018/7/3/b321fc7ee4289500327678791bb9f947.jpg)

AOP 通过（动态）代理机制可以让开发者从这些繁琐事项中抽身出来，大幅度提高了代码的抽象程度和复用度。从逻辑上来说，我们在软件设计和实现中的类似代理，如 Facade、Observer 等很多设计目的，都可以通过动态代理优雅地实现。

## 21. java 左移、右移、无符号位移的原理

参考
[Java中>>和>>>的区别 - belief01 - 博客园](https://www.cnblogs.com/565261641-fzh/p/7686757.html)

[Java的位运算符详解实例——与（&）、非（~）、或（|）、异或（^） - 菜的一笔的程序员 - 博客园](https://www.cnblogs.com/lichengze/p/5713409.html)

![image](http://static.lovedata.net/jpg/2018/7/9/27dab01f11e1862420683fa319384520.jpg)

在这6种操作符，只有~取反是单目操作符，其它5种都是双目操作符。
位操作只能用于整形数据，对float和double类型进行位操作会被编译器报错

右移 31位相当取符号位。 2 >> 31  -> 0 （证书为0，负数为1）
右移 32位相当于什么都不做。 2 >> 32  -> 2
右移 33位相当于右移1位。  2 >> 33  -> 1
右移 34位相当于右移2位。   2 >> 34  -> 0

```java
public class DisplacementDemo {
    public static void main(String[] args) {
        printBinary("4", 4);
        System.out.println("右移");
        printBinary("4 >> 1", 4 >> 1);
        printBinary("4 >> 2", 4 >> 2);
        printBinary("-4 >> 2", -4 >> 2);
        printBinary("4 >> 3", 4 >> 3);
        printBinary("4 >> 4", 4 >> 4);
        System.out.println("左移");
        printBinary("4 << 2", 4 << 1);
        printBinary("4 << 2", 4 << 2);
        printBinary("4 << 2", 4 << 3);
        //无符号位移
        System.out.println("无符号右移");
        //对于正数而言，>>和>>>没区别。
        printBinary("4 >>> 1", 4 >>> 1);
        System.out.println("无符号左移");
        // 对于负数而言,结果是2147483647（Integer.MAX_VALUE）
        printBinary("-4 >>> 1", -4 >>> 1);
    }

    private static void printBinary(String msg, int a) {
        System.out.println("["+msg + "] : " + a + " -> " + Integer.toBinaryString(a));
    }
}
/**Result:
[4] : 4 -> 100
右移
[4 >> 1] : 2 -> 10
[4 >> 2] : 1 -> 1
[-4 >> 2] : -1 -> 11111111111111111111111111111111
[4 >> 3] : 0 -> 0
[4 >> 4] : 0 -> 0
左移
[4 << 2] : 8 -> 1000
[4 << 2] : 16 -> 10000
[4 << 2] : 32 -> 100000
无符号右移
[4 >>> 1] : 2 -> 10
无符号左移
[-4 >>> 1] : 2147483646 -> 1111111111111111111111111111110
**/
```

左移没有<<<运算符！
0是正数，1是负数
要判断两个数符号是否相同时，可以这么干：
return ((a >> 31) ^ (b >> 31)) == 0;

```java
public class BitLogicDemo {

    public static void main(String[] args) {
        System.out.println(compareIntSymbol(3, -4));
    }

    private static boolean compareIntSymbol(int a, int b) {
        System.out.println(a >> 31); //0
        System.out.println(b >> 31);//-1
        // 要判断两个数符号是否相同时，可以这么干：
        return ((a >> 31) ^ (b >> 31)) == 0;
    }
}
/** result
0
-1
false
**/
```

## 22. 位移操作的应用

[位操作基础篇之位操作全面总结 - CSDN博客]
(https://blog.csdn.net/morewindows/article/details/7354571)

[Java位操作方法，位运算实际应用(简单总结) -  - ITeye博客](http://longshaojian.iteye.com/blog/1946865)

### 22.1 奇偶判断

```java
 /**
     * 奇偶判断
     */
    private static void oddEven() {
        printBinary("3", 3);
        printBinary("4", 4);
        printBinary("7", 7);
        printBinary("8", 8);

        printBinary("-4", -4);
        printBinary("1", 1);
        //因为奇数的最后一位是1，而1的最后一位是1，所以奇数 & 1 结果为1
        //因为偶数的最后一位是0，而1的最后一位是1，所以偶数 & 1 结果为0
        // 因此可以用if ((a & 1) == 0)代替if (a % 2 == 0)来判断a是不是偶数。
        printBinary("3 & 1", 3 & 1);
        printBinary("4 & 1", 4 & 1);
    }

    private static void printBinary(String msg, int a) {
        System.out.println("[" + msg + "] : " + a + " -> " + Integer.toBinaryString(a));
    }
/**
[3] : 3 -> 11
[4] : 4 -> 100
[7] : 7 -> 111
[8] : 8 -> 1000
[-4] : -4 -> 11111111111111111111111111111100
[1] : 1 -> 1
[3 & 1] : 1 -> 1
[4 & 1] : 0 -> 0
**
```

## 22.2 数字交换

```java
 public static void Swap(int a, int b) {
        if (a != b) {
            a ^= b;
            b ^= a;
            a ^= b;
        }
        //由于传递的只是一个副本，所以不会对他进行修改
        System.out.println(a + " " + b);
    }
```

## 22.3 变换正负数

```java
   public static void main(String[] args) {
        oddEven();
        Swap(12, 14);
        System.out.println(signReversal(12));
    }

    private static int signReversal(int i) {
        System.out.println("---signReversal---");
        printBinary("i", i);
        printBinary("i", ~i);
        printBinary("~i + 1", ~i + 1);
        return ~i + 1;
    }

/***
---signReversal---
[i] : 12 -> 1100
[i] : -13 -> 11111111111111111111111111110011
[~i + 1] : -12 -> 11111111111111111111111111110100
-12
**/
```

## 22.4 求绝对值

```java
 public static int abs(int a) {
        System.out.println("---abs---");
        int i = a >> 31;
        printBinary("i", i);
        //位操作也可以用来求绝对值，对于负数可以通过对其取反后加1来得到正数
        return i == 0 ? a : ~a + 1;
    }

    public static int abs2(int a) {
        System.out.println("---abs2---");
        int i = a >> 31;
        return ((a ^ i) - i);
    }
/**
---abs---
[i] : -1 -> 11111111111111111111111111111111
1
**/
```

## 23. BitSet 的原理？

[【JAVA】BitSet的源码研究 - CSDN博客](https://blog.csdn.net/wxwzy738/article/details/8879423)
[Java BitSet使用场景和示例 - 温布利往事 - 博客园](https://www.cnblogs.com/xujian2014/p/5491286.html)

```java
 private static void testSetGetClear() {
        System.out.println("----set----");
        long[] words = new long[3];
        words[0] |= (1L << 3);
        System.out.println(Arrays.toString(words));
        System.out.println("----get----");
        System.out.println(((words[0] & (1L << 3)) != 0));

        words[0] |= (1L << 4);
        System.out.println(Arrays.toString(words));

        printBinary("1L", 1L);
        printBinary("3", 3);
        printBinary("3", 4);
        printBinary("2", 2);
        printBinary("1L<<2", 1L << 2);

        printBinary("24", words[0]);
        printBinary("1L<<3", 1L << 3);

        System.out.println(((words[0] & (1L << 3)) != 0));
        System.out.println(((words[0] & (1L << 4)) != 0));
        System.out.println(((words[0] & (1L << 5)) != 0));

        // 核心在于 一个数在求或（两个数都为0的时候才为0）之后，拿结果在求与，结果肯定是不为0的，所以此时就能判断是否在这个bitset中
        printBinary("10 | (1<<3)", 10 | (1 << 3));
        printBinary("10 | (1<<4)", 10 | (1 << 4));
        printBinary("26 & (1<<4)", 26 & (1 << 4));

    }

    private static void printBinary(String msg, long a) {
        System.out.println("[" + msg + "] : " + a + " -> " + Long.toBinaryString(a));
    }
/**
true
true
----set----
[8, 0, 0]
----get----
true
[24, 0, 0]
[1L] : 1 -> 1
[3] : 3 -> 11
[3] : 4 -> 100
[2] : 2 -> 10
[1L<<2] : 4 -> 100
[24] : 24 -> 11000
[1L<<3] : 8 -> 1000
true
true
false
[10 | (1<<3)] : 10 -> 1010
[10 | (1<<4)] : 26 -> 11010
[26 & (1<<4)] : 16 -> 10000
**/

```

## 23. java 自动拆箱和自动装箱（int 和 integer 有什么区别）？

int 是我们常说的整形数字，是 Java 的 8 个原始数据类型（Primitive Types，boolean、byte 、short、char、int、float、double、long）之一。Java 语言虽然号称一切都是对象，但原始数据类型是例外。

Integer 是 int 对应的包装类，它有一个 int 类型的字段存储数据，并且提供了基本操作，比如数学运算、int 和字符串之间转换等。在 Java 5 中，引入了自动装箱和自动拆箱功能（boxing/unboxing），Java 可以根据上下文，自动进行转换，极大地简化了相关编程。

Java 平台为我们自动进行了一些转换，保证不同的写法在运行时等价，它们发生在编译阶段，也就是生成的字节码是一致的。

像前面提到的整数，javac 替我们自动把装箱转换为 Integer.valueOf()，把拆箱替换为 Integer.intValue()，这似乎这也顺道回答了另一个问题，既然调用的是 Integer.valueOf，自然能够得到缓存的好处啊。

![image](http://static.lovedata.net/jpg/2018/7/10/698703d8312e5f9955dd4ebcc5d5e749.jpg)

Boolean，缓存了 true/false 对应实例，确切说，只会返回两个常量实例 Boolean.TRUE/FALSE。 
Short，同样是缓存了 -128 到 127 之间的数值。
Byte，数值有限，所以全部都被缓存。
Character，缓存范围 '\u0000' 到 '\u007F'。

int 装箱的时候是在缓存中拿的，Byte是全部放入缓存
![image](http://static.lovedata.net/jpg/2018/7/10/6d1eb98e67efebd9786271275868a40a.jpg)

Integer 会根据int的值得大小来做判断，如果在-128到127（这个值可以配置）之间，就从缓存里面取，否则new一个出来
![image](http://static.lovedata.net/jpg/2018/7/10/9c58c021c8ca6d151a17ec6c1f01c438.jpg)

建议避免无意中的装箱、拆箱行为，尤其是在性能敏感的场合，创建 10 万个 Java 对象和 10 万个整数的开销可不是一个数量级的，不管是内存使用还是处理速度，光是对象头的空间占用就已经是数量级的差距了

使用原始数据类型、数组甚至本地代码实现等，在性能极度敏感的场景往往具有比较大的优势，用其替换掉包装类、动态数组（如 ArrayList）等可以作为性能优化的备选项

Integer 等包装类，定义了类似 SIZE 或者 BYTES 这样的常量 保证了跨平台性  这种移植对于 Java 来说相对要简单些，因为原始数据类型是不存在差异的，这些明确定义在Java 语言规范里面，不管是 32 位还是 64 位环境，开发者无需担心数据的位数差异。

如果有线程安全的计算需要，建议考虑使用类似 AtomicInteger、AtomicLong 这样的线程安全类。

部分比较宽的数据类型，比如 float、double，甚至不能保证更新操作的原子性，可能出现程序读取到只更新了一半数据位的数值

## 24. 对比Vector、ArrayList、LinkedList有何区别？

[Java集合框架（Set与Map，HashSet与HashMap，TreeSet与TreeMap） - CSDN博客](https://blog.csdn.net/u013344815/article/details/49128315)

都是List，也就是所谓的有序集合，因此具体功能也比较近似，比如都提供按照位置进行定位、添加或者删除的操作，都提供迭代器以遍历其内容等。但因为具体的设计区别，在行为、性能、线程安全等方面，表现又有很大不同

Verctor 是 Java 早期提供的线程安全的动态数组 Vector 内部是使用对象数组来保存数据，可以根据需要自动的增加容量，当数组已满时，会创建新的数组，并拷贝原有数组数据

ArrayList 是应用更加广泛的动态数组实现，它本身不是线程安全的，所以性能要好很多  Vector 在扩容时会提高 1 倍，而 ArrayList 则是增加 50%。

LinkedList 顾名思义是 Java 提供的双向链表，所以它不需要像上面两种那样调整容量，它也不是线程安全的

场景：

1. Vector 和 ArrayList 作为动态数组，其内部元素以数组形式顺序存储的，所以非常适合随机访问的场合。除了尾部插入和删除元素，往往性能会相对较差，比如我们在中间位置插入一个元素，需要移动后续所有元素。
2. 而 LinkedList 进行节点插入、删除却要高效得多，但是随机访问性能则要比动态数组慢。

![image](http://static.lovedata.net/jpg/2018/7/10/cc1411bb7805906cdb5c4f7b068061aa.jpg)

- List，也就是我们前面介绍最多的有序集合，它提供了方便的访问、插入、删除等操作。
- Set，Set 是不允许重复元素的，这是和 List 最明显的区别，也就是不存在两个对象 equals 返回 true。我们在日常开发中有很多需要保证元素唯一性的场合。
- Queue/Deque，则是 Java 提供的标准队列结构的实现，除了集合的基本功能，它还支持类似先入先出（FIFO， First-in-First-Out）或者后入先出（LIFO，Last-In-First-Out）等特定行为。这里不包括 BlockingQueue，因为通常是并发编程场合，所以被放置在并发包里。

TreeSet 代码里实际默认是利用 TreeMap 实现的 Java 类库创建了一个 Dummy 对象“PRESENT”作为 value，然后所有插入的元素其实是以键的形式放入了 TreeMap 里面；同理，HashSet 其实也是以 HashMap 为基础实现的，原来他们只是 Map 类的马甲

Set

- TreeSet 支持自然顺序访问，但是添加、删除、包含等操作要相对低效（log(n) 时间）。
- HashSet 则是利用哈希算法，理想情况下，如果哈希散列正常，可以提供常数时间的添加、删除、包含等操作，但是它不保证有序。
- LinkedHashSet，内部构建了一个记录插入顺序的双向链表，因此提供了按照插入顺序遍历的能力，与此同时，也保证了常数时间的添加、删除、包含等操作，这些操作性能略低于 HashSet，因为需要维护链表的开销。


## 25. LinkedHashSet 和 LinkedHashMap的区别

[Java集合框架源码剖析：LinkedHashSet 和 LinkedHashMap - CarpenterLee - 博客园](https://www.cnblogs.com/CarpenterLee/p/5541111.html)

至于下面为什么Hashmap输出的key是按照字母递增排序的，请看
[Java遍历HashSet为什么输出是有序的](https://www.douban.com/note/596873407/)

```java
 public static void main(String[] args) {
        //   LinkedHashMap的迭代输出的结果保持了插入顺序
        System.out.println("---------LinkedHashMap---------");
        LinkedHashMap<String, Integer> lmap = new LinkedHashMap();
        lmap.put("b", 3);
        lmap.put("a", 4);
        lmap.put("e", 6);
        for (Map.Entry<String, Integer> entry : lmap.entrySet()) {
            System.out.println(entry.getKey() + ": " + entry.getValue());
        }

        System.out.println("---------hashmap---------");
        HashMap<String, Integer> hmap = new HashMap();
        hmap.put("b", 3);
        hmap.put("a", 4);
        hmap.put("e", 6);
        for (Map.Entry<String, Integer> entry : hmap.entrySet()) {
            System.out.println(entry.getKey() + ": " + entry.getValue());
        }
    }

    /**
---------LinkedHashMap---------
b: 3
a: 4
e: 6
---------hashmap---------
a: 4
b: 3
e: 6
    **/
```

## 26. HashSet 和 LinkedhashSet

```java
public class LinkedHashSetDemo {
    public static void main(String[] args) {
        System.out.println("---------LinkedHashSet---------");
        LinkedHashSet<String> lhs = new LinkedHashSet<>();
        lhs.add("j");
        lhs.add("e");
        lhs.add("m");
        lhs.add("c");
        lhs.forEach(e-> System.out.println(e));

        System.out.println("---------HashSet---------");
        HashSet<String> hs = new HashSet<>();
        hs.add("j");
        hs.add("jc");
        hs.add("e");
        hs.add("m");
        hs.add("ac");
        hs.add("c");
        hs.add("d");
        hs.forEach(e-> System.out.println(e));
    }
}

/**
---------LinkedHashSet---------
j
e
m
c
---------HashSet---------
ac
c
d
e
jc
j
m
**
```

## 30 对比Hashtable、HashMap、TreeMap有什么不同？

以键值对的形式存储和操作数据的容器类型

Hashtable 是早期 Java 类库提供的一个哈希表实现，本身是同步的，不支持 null 键和值，由于同步导致的性能开销，所以已经很少被推荐使用。

HashMap 是应用更加广泛的哈希表实现，行为上大致上与 HashTable 一致，主要区别在于 HashMap 不是同步的，支持 null 键和值等

TreeMap 则是基于红黑树的一种提供顺序访问的 Map，和 HashMap 不同，它的 get、put、remove 之类操作都是 O（log(n)）的时间复杂度，具体顺序可以由指定的 Comparator 来决定，或者根据键的自然顺序来判断。

![image](http://static.lovedata.net/jpg/2018/7/12/bb1636ca4d7e08da765b30822f66f580.jpg)

## 31 HashMap 源码分析

[Java7/8 中的 HashMap 和 ConcurrentHashMap 全解析 - ImportNew](http://www.importnew.com/28263.html)

- HashMap 内部实现基本点分析。
- 容量（capcity）和负载系数（load factor）。
- 树化 。

它可以看作是数组（Node[] table）和链表结合组成的复合结构，数组被分为一个个桶（bucket），通过哈希值决定了键值对在这个数组的寻址；哈希值相同的键值对，则以链表形式存储，你可以参考下面的示意图。这里需要注意的是，如果链表大小超过阈值（TREEIFY_THRESHOLD, 8），图中的链表就会被改造为树形结构。

![image](http://static.lovedata.net/jpg/2018/7/12/8d86171c660f52d93358e9a8af8e5155.jpg)

它并不是 key 本身的 hashCode，而是来自于 HashMap 内部的另外一个 hash 方法。注意，为什么这里需要将高位数据移位到低位进行异或运算呢？这是因为有些数据计算出的哈希值差异主要在高位，而 HashMap 里的哈希寻址是忽略容量以上的高位的，那么这种处理就可以有效避免类似情况下的哈希碰撞。

### java7 HashMap

![image](http://static.lovedata.net/jpg/2018/7/12/ca135927af830b28e5010dea44d8cfdf.jpg)

capacity：当前数组容量，始终保持 2^n，可以扩容，扩容后数组大小为当前的 2 倍。
loadFactor：负载因子，默认为 0.75。
threshold：扩容的阈值，等于 capacity * loadFactor

### Java8 HashMap

Java8 对 HashMap 进行了一些修改，最大的不同就是利用了红黑树，所以其由 数组+链表+红黑树 组成。

根据 Java7 HashMap 的介绍，我们知道，查找的时候，根据 hash 值我们能够快速定位到数组的具体下标，但是之后的话，需要顺着链表一个个比较下去才能找到我们需要的，时间复杂度取决于链表的长度，为 O(n)。

为了降低这部分的开销，在 Java8 中，当链表中的元素超过了 8 个以后，会将链表转换为红黑树，在这些位置进行查找的时候可以降低时间复杂度为 O(logN)。

![image](http://static.lovedata.net/jpg/2018/7/12/3d41215e962ff43cf808fd88149317a8.jpg)

Java7 中使用 Entry 来代表每个 HashMap 中的数据节点，Java8 中使用 Node，基本没有区别，都是 key，value，hash 和 next 这四个属性，不过，Node 只能用于链表的情况，红黑树的情况需要使用 TreeNode。

## 32 ConcurrentHashMap  源码分析

![image](http://static.lovedata.net/jpg/2018/7/12/aba4f22eab5917cc79ed13a740687400.jpg)

concurrencyLevel：并行级别、并发数、Segment 数，怎么翻译不重要，理解它。默认是 16，也就是说 ConcurrentHashMap 有 16 个 Segments，所以理论上，这个时候，最多可以同时支持 16 个线程并发写，只要它们的操作分别分布在不同的 Segment 上。这个值可以在初始化的时候设置为其他值，但是一旦初始化以后，它是不可以扩容的。

其实每个 Segment 很像之前介绍的 HashMap，不过它要保证线程安全

initialCapacity：初始容量，这个值指的是整个 ConcurrentHashMap 的初始容量，实际操作的时候需要平均分给每个 Segment。
loadFactor：负载因子，之前我们说了，Segment 数组不可以扩容，所以这个负载因子是给每个 Segment 内部使用的

## 33 Java提供了哪些IO方式？ NIO如何实现多路复用？

Java IO 方式有很多种，基于不同的 IO 抽象模型和交互方式，可以进行简单区分。
首先，传统的 java.io 包，它基于流模型实现，提供了我们最熟知的一些 IO 功能，比如 File 抽象、输入输出流等。交互方式是同步、阻塞的方式，也就是说，在读取输入流或者写入输出流时，在读、写动作完成之前，线程会一直阻塞在那里，它们之间的调用是可靠的线性顺序。
java.io 包的好处是代码比较简单、直观，缺点则是 IO 效率和扩展性存在局限性，容易成为应用性能的瓶颈。
很多时候，人们也把 java.net 下面提供的部分网络 API，比如 Socket、ServerSocket、HttpURLConnection 也归类到同步阻塞 IO 类库，因为网络通信同样是 IO 行为。

第二，在 Java 1.4 中引入了 NIO 框架（java.nio 包），提供了 Channel、Selector、Buffer 等新的抽象，可以构建多路复用的、同步非阻塞 IO 程序，同时提供了更接近操作系统底层的高性能数据操作方式。

第三，在 Java 7 中，NIO 有了进一步的改进，也就是 NIO 2，引入了异步非阻塞 IO 方式，也有很多人叫它 AIO（Asynchronous IO）。异步 IO 操作基于事件和回调机制，可以简单理解为，应用操作直接返回，而不会阻塞在那里，当后台处理完成，操作系统会通知相应线程进行后续工作。

![image](http://static.lovedata.net/jpg/2018/7/12/0227b399a0f653c26f1c9f5bb7575ca3.jpg)

## 34. wait，notify，notifyAll方法是什么？

![image](http://static.lovedata.net/jpg/2018/7/13/527f0cb9c21af6c549f7b5a2cc371603.jpg)

## 35. 什么时候会发生死锁

![image](http://static.lovedata.net/jpg/2018/7/13/1e38ba0b83326905e5c011092cfd9e62.jpg)

![image](http://static.lovedata.net/jpg/2018/7/13/97fd0df8c04ecf7a313d44ea29476227.jpg)

## 36. long 和 double 的操作是原子性的吗？

不是。

![image](http://static.lovedata.net/jpg/2018/7/13/c3b077ccefcf71805bf8e9afba779242.jpg)

![image](http://static.lovedata.net/jpg/2018/7/13/e99e113e3fa491bc2c31435c86ce9610.jpg)

![image](http://static.lovedata.net/jpg/2018/7/13/d42a86d6b81a264e2877282d203d6610.jpg)

---
总结：

![image](http://static.lovedata.net/jpg/2018/7/13/d25de1a66ed9e468b5eb3fb749b25fc0.jpg)

![image](http://static.lovedata.net/jpg/2018/7/13/290e79020a46ee7576e1e9cba73d2d0a.jpg)

---

Semaphore 表示信号量 permits t通过构造函数指定。accquire 获取资源，release 释放资源。如果有accquire资源等待，则会被唤醒。
