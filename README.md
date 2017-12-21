# Java 面试题整理

C:\Users\sqsga\OneDrive\MeProjectsEx\Java-server-interview-questions\README.md
/Users/sqsgalaxys/Downloads/OneDrive/MeProjectsEx/Java-server-interview-questions/README.md

## Java基础

Arrays.sort实现原理和Collection实现原理

foreach和while的区别(编译之后)

线程池的种类，区别和使用场景

分析线程池的实现原理和线程的调度过程

线程池如何调优

线程池的最大线程数目根据什么确定

动态代理的几种方式

HashMap的并发问题

了解LinkedHashMap的应用吗

反射的原理，反射创建类实例的三种方式是什么？

cloneable接口实现原理，浅拷贝or深拷贝

Java NIO使用

hashtable和hashmap的区别及实现原理，hashmap会问到数组索引，hash碰撞怎么解决

arraylist和linkedlist区别及实现原理

反射中，Class.forName和ClassLoader区别

String，Stringbuffer，StringBuilder的区别？

有没有可能2个不相等的对象有相同的hashcode

简述NIO的最佳实践，比如netty，mina

TreeMap的实现原理

## JVM 相关

### 01 类的实例化顺序，比如父类静态数据，构造函数，字段，子类静态数据，构造函数，字段，他们的执行顺序

----

父类--静态变量

父类--静态初始化块

子类--静态变量

子类--静态初始化块

父类--变量(字段)

父类--初始化块

父类--构造器

子类--变量(变量)

子类--初始化块

子类--构造器

ps: 静态变量,和静态初始化块若无依赖关系,加载(执行)顺序为声明顺序 

静态初始化块中如果使用静态变量,而静态变量声明在静态初始化块后面则编译器会报错. 

常量 (static final 修饰的) 会存入调用类的常量池(这里说的是main函数所在的类的常量池)

#### 参考:

----

[Java类初始化顺序 - xixicat - SegmentFault](https://segmentfault.com/a/1190000004527951 '0.0')

[Java类的初始化顺序_chuansir_新浪博客](http://blog.sina.com.cn/s/blog_4cc16fc50100bjjp.html '0.0')

[Java类加载的时机 - CSDN博客](http://blog.csdn.net/imzoer/article/details/8038249 '0.0')
[(转)Java类初始化顺序 - jackyrong的世界 - 博客园](http://www.cnblogs.com/jackyrong/archive/2008/08/12/1266161.html '0.0')



### 02 JVM内存分代

----
**程序计数器**
这是一块较小的内存空间,它的作用可以看做是当前线程所执行的字节码的行号指示器,线程私有
**Java 虚拟机栈**
它是 Java方法执行的内存模型,每一个方法被调用到执行完成的过程,就对应着一个栈帧在虚拟机栈中从入栈到出栈的过程,线程私有
**本地方法栈**
跟虚拟机栈类似,不过本地方法栈用于执行本地方法,线程私有
**Java 堆**
该区域存在的唯一目的就是存放对象,几乎应用中所有的对象实例都在这里分配内存,所有线程共享
**方法区**
它用于存储已被虚拟机加载的类信息,常量,静态变量,即时编译器编译后的代码等数据,所有线程共享

#### 参考:
[漫谈 JVM 内存分代、垃圾回收 - 咕咚的个人站点](http://gudong.name/2017/04/24/JVM_oom_gc.html '0.0')

[JVM对象分代内存划分与垃圾回收_JVM教程_田守枝Java技术博客](http://www.tianshouzhi.com/api/tutorials/JVM/97 '0.0')

[Java虚拟机：JVM内存分代策略 - CSDN博客](http://blog.csdn.net/xmtblog/article/details/75480912 '0.0')



### 03 Java 8 的内存分代改进

----

> 在JDK8之前的HotSpot虚拟机中，类的这些“永久的”数据存放在一个叫做永久代的区域。永久代一段连续的内存空间，我们在JVM启动之前可以通过设置-XX:MaxPermSize的值来控制永久代的大小，32位机器默认的永久代的大小为64M，64位的机器则为85M。永久代的垃圾回收和老年代的垃圾回收是绑定的，一旦其中一个区域被占满，这两个区都要进行垃圾回收。但是有一个明显的问题，由于我们可以通过‑XX:MaxPermSize 设置永久代的大小，一旦类的元数据超过了设定的大小，程序就会耗尽内存，并出现内存溢出错误(OOM)。

[JEP 122: Remove the Permanent Generation](http://openjdk.Java.net/jeps/122 '0.0')



### 04 JVM 垃圾回收机制，何时触发 MinorGC 等操作

----

垃圾回收时会执行 Minor GC、Major GC和Full GC等操作

#### Minor GC :

----

> 从年轻代空间（包括 Eden 和 Survivor 区域）回收内存被称为 Minor GC
>
> 当 JVM 无法为一个新的对象分配空间时会触发 Minor GC，比如当 Eden 区满了。所以分配率越高，越频繁执行 Minor GC。
>
> Minor GC、Major GC 和 Full GC之间的区别(TODO):

#### Major GC :

----

> Major GC 是清理老年代。
> 很不幸，实际上它还有点复杂且令人困惑。首先，许多 Major GC 是由 Minor GC 触发的，所以很多情况下将这两种 GC 分离是不太可能的。另一方面，许多现代垃圾收集机制会清理部分永久代空间，所以使用“cleaning”一词只是部分正确。



#### Full GC

----

> Full GC 是清理整个堆空间—包括年轻代和老年代。
>
> 这使得我们不用去关心到底是叫 Major GC 还是 Full GC，大家应该关注当前的 GC 是否停止了所有应用程序的线程，还是能够并发的处理而不用停掉应用程序的线程。



### 05 JVM中一次完整的GC流程（从ygc到fgc）是怎样的，重点讲讲对象如何晋升到老年代，几种主要的JVM参数等

----

![](http://img.blog.csdn.net/20170428141420806?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjI1Nzk1NQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)



对象晋升老生代一共有三个可能：

1.当对象达到成年，经历过15次GC（默认15次，可配置），对象就晋升为老生代

2.大的对象会直接在老生代创建

3.新生代跟幸存区内存不足时，对象可能晋升到老生代

#### 参考:

[Java面试题总览--JVM知识 - u012257955的博客 - CSDN博客](http://blog.csdn.net/u012257955/article/details/70890702 '0.0')



### 06 你知道哪几种垃圾收集器，各自的优缺点，重点讲下cms，g1

----

串行垃圾收集器：收集时间长，停顿时间久
并发垃圾收集器：碎片空间多
CMS:并发标记清除。他的主要步骤有：初始收集，并发标记，重新标记，并发清除（删除），重置
G1：主要步骤：初始标记，并发标记，重新标记，复制清除（整理）

CMS的缺点是对cpu的要求比较高。G1是将内存化成了多块，所有对内段的大小有很大的要求
CMS是清除，所以会存在很多的内存碎片。G1是整理，所以碎片空间较小



吞吐量优先：G1

响应优先：CMS

### 07 新生代和老生代的内存回收策略

----

>   堆大小 = 年轻代 + 老年代
>   年轻代 = eden space (新生代) + from survivor + to survivor
>   Minor GC 是发生在新生代中的垃圾收集动作，所采用的是复制算法
>   Full GC 是发生在老年代的垃圾收集动作，所采用的是标记-清除算法


### 08 Eden和Survivor的比例分配等

----

>   初级回收将年轻代分为三个区域, 一个新生代 , 2个大小相同的复活代
>   年轻代用来存放新近创建的对象
>   尺寸随堆大小的增大和减小而相应的变化
>   默认值是保持为堆大小的1/15
>   可以通过 -Xmn 参数设置年轻代为固定大小
>   也可以通过 -XX:NewRatio 来设置年轻代与老年代的大小比例

#### 参考:
[Java 新生代、老年代、持久代、元空间](http://gblog.sherlocky.com/Java-xin-sheng-dai-lao-nian-dai/ '0.0')

### 09 深入分析了 Classloader, 双亲委派机制

----
类的加载过程指通过一个类的**全限定名**来获取描述此类的二进制字节流，
并将其转化为**方法区**的数据结构，进而生成一个Java.lang.Class对象作为方法区这个类各种数据访问的入口。
这个过程通过Java中的类加载器(ClassLoader)来完成。

类与类加载器
类加载器非常重要，
因为每个类加载器都有一个独立的类名称空间。
比如我们要加载两个类，
如果要比较两个类是否相等（包括equals()方法、isAssignableFrom()方法、isInstance()方法），
只有在这两个类被同一个类加载器加载的前提下，比较才有意义。
否则，即使两个类来自同一个class文件，被同一个JVM加载，但是加载它们的类加载器不同，则这两个类就不相等。
这就相当于两个命名空间中的等价类LoaderA::C和LoaderB::C。

类加载器的种类
从一般角度来分的话，ClassLoader分为根加载器（Bootstrap ClassLoader）和其它的加载器。
其中Bootstrap ClassLoader负责加载Java的核心类，由JVM实现(C++)，而其它类加载器都由Java层实现并继承Java.lang.ClassLoader。

更细分的话，ClassLoader分为：

Bootstrap ClassLoader（启动类加载器）负责将%Java_HOME%/lib目录中或-Xbootclasspath中参数指定的路径中的，
并且是虚拟机识别的（按名称）类库加载到JVM中
Extension ClassLoader（扩展类加载器）负责加载%Java_HOME%/lib/ext中的所有类库
System ClassLoader（加载%CLASSPATH%路径的类库）以及其它自定义的ClassLoader

双亲委派模型
JVM中类加载的机制——双亲委派模型。
这个模型要求除了Bootstrap ClassLoader外，其余的类加载器都要有自己的父加载器。
子加载器通过组合来复用父加载器的代码，而不是使用继承。
在某个类加载器加载class文件时，它首先委托父加载器去加载这个类，依次传递到顶层类加载器(Bootstrap)。
如果顶层加载不了（它的搜索范围中找不到此类），子加载器才会尝试加载这个类。

双亲委派模型最大的好处就是让Java类同其类加载器一起具备了一种带优先级的层次关系。
这句话可能不好理解，我们举个例子。
比如我们要加载顶层的Java类——Java.lang.Object类，无论我们用哪个类加载器去加载Object类，这个加载请求最终都会委托给Bootstrap ClassLoader，这样就保证了所有加载器加载的Object类都是同一个类。
如果没有双亲委派模型，那就乱了套了，完全可以搞出Root::Object和L1::Object这样两个不同的Object类。

双亲委派模型的实现比较简单，在Java.lang.ClassLoader的loadClass方法中：

#### 参考:
[深入探究JVM | 类加载器与双亲委派模型 | 「浮生若梦」 - sczyh30's blog](http://www.sczyh30.com/posts/Java/JVM-classloader-parent-delegation-model/ '0.0')


### 10 JVM的编译优化

----
HotSpot中内置了两个即时编译器，分别称为 Client Compiler和 Server Compiler ，或者简称为 C1编译器和 C2编译器。

#### 参考:
[JVM编译优化 - CSDN博客](http://blog.csdn.net/qq_16681169/article/details/72945113 '0.0')


### 11 对Java内存模型的理解，以及其在并发中的应用

----

为了定义多线程中共享变量的可见性和保证程序能够对外（多个CPU）提供统一的视图。
JSL中提到过JVM中“存在”一个主内存，所有的线程共享主内存，而每个线程有自己独立的工作内存，工作内存相互不可见，
因此Java中线程之间的通信实际上使用的是共享内存，另外一种常用的通信方式是消息，

#### 参考:
[Java内存模型和并发](https://zhuanlan.zhihu.com/p/27361117 '0.0')

### 12 指令重排序，内存栅栏等

> 如下图所示。值得注意的是，a=1和x=b这两个语句的赋值操作的顺序被颠倒了，
> 
> 或者说，发生了指令“重排序”(reordering)。（事实上，输出了这一结果，并不代表一定发生了指令重排序，内存可见性问题也会导致这样的输出，详见后文）
>
> 内存屏障（Memory Barrier，或有时叫做内存栅栏，Memory Fence）是一种CPU指令
>
> 用于控制特定条件下的重排序和内存可见性问题
>
> Java编译器也会根据内存屏障的规则禁止重排序

![](https://tech.meituan.com/img/java-memory-reordering/code-result-sample.png)

#### 参考:
[Java内存访问重排序的研究 -](https://tech.meituan.com/java-memory-reordering.html '0.0')

----

### 13 OOM错误，stackoverflow错误，permgen space错误

#### OOM错误
1.  java.lang.OutOfMemoryError: Java heap space
2.  java.lang.OutOfMemoryError: GC overhead limit exceeded
3.  java.lang.OutOfMemoryError: Permgen space
4.  java.lang.OutOfMemoryError: Metaspace
5.  java.lang.OutOfMemoryError: Unable to create new native thread
6.  java.lang.OutOfMemoryError: Out of swap space?
7.  java.lang.OutOfMemoryError: Requested array size exceeds VM limit
8.  Out of memory: Kill process or sacrifice child

#### stackoverflow 错误

> JVM里会有两种StackOverflowError, 一种是对应JVM stack, 一种是对应Native Method stack. 我们一般说的都是JVM stack.

> 每一个JVM线程维护自己的JVM stack.

> JVM stack里面存放 JVM栈帧.

> 栈帧中存放 数据和中间结果(本地变量数组, 操作符栈, 和对runtime 常量池的引用).

> 这些数据都比较小(对象都在堆中, 栈帧仅存放对象引用), 所以想单纯通过 在栈帧中存放大数据的方法 去引入StackOverflowError, 基本是不现实的.

> 一般都是因为方法调用嵌套层数过大.

#### permgen space 错误

> PermGen space的全称是Permanent Generation space,

> 是指内存的永久保存区域,

> 这块内存主要是被JVM存放Class和Meta信息的,

> Class在被Loader时就会被放到PermGen space中,

> 它和存放类实例(Instance)的Heap区域不同,

> GC(Garbage Collection)不会在主程序运行期对PermGen space进行清理，

> 所以如果你的应用中有很多CLASS的话,

> 就很可能出现PermGen space错误,

> 这种错误常见在web服务器对JSP进行pre compile的时候。

> 如果你的WEB APP下都用了大量的第三方jar,

> 其大小超过了jvm默认的大小(4M)那么就会产生此错误信息了


#### 参考:
[Java内存溢出(OOM)异常完全指南 - 简书](http://www.jianshu.com/p/2fdee831ed03 '0.0')

[java.lang.OutOfMemoryError - The Ultimate Handbook by @JavaPlumbr](https://plumbr.io/outofmemoryerror '0.0')

[Java基础：OutOfMemoryError和StackOverflowError](http://arthur503.github.io/blog/2013/10/10/Java-OutOfMemoryError-and-StackOverflowError.html '0.0')

[PermGen space错误解决方法 - xwdreamer - 博客园](http://www.cnblogs.com/xwdreamer/archive/2011/11/21/2296930.html '0.0')


----

### 14 JVM常用参数

----

-XX:PermSize=128M

-XX:MaxPermSize=512m

-XX:PermSize=128M

-XX:MaxPermSize=512m

-Xms512m

-Xmx1024m

-XX:PermSize=640m

-XX:MaxPermSize=1280m

-XX:NewSize=64m

-XX:MaxNewSize=256m

-verbose:gc

-XX:+PrintGCDetails

-XX:+PrintGCTimeStamps

### 15 tomcat结构，类加载器流程

----

### 16 volatile的语义，它修饰的变量一定线程安全吗

----

### 17 g1和cms区别,吞吐量优先和响应优先的垃圾收集器选择

----

### 18 说一说你对环境变量classpath的理解？如果一个类不在classpath下，为什么会抛出ClassNotFoundException异常，如果在不改变这个类路径的前期下，怎样才能正确加载这个类？

----

### 19 说一下强引用、软引用、弱引用、虚引用以及他们之间和gc的关系

----



## JUC/并发相关

ThreadLocal用过么，原理是什么，用的时候要注意什么
Synchronized和Lock的区别
synchronized 的原理，什么是自旋锁，偏向锁，轻量级锁，什么叫可重入锁，什么叫公平锁和非公平锁
concurrenthashmap具体实现及其原理，jdk8下的改版
用过哪些原子类，他们的参数以及原理是什么
cas是什么，他会产生什么问题（ABA问题的解决，如加入修改次数、版本号）
如果让你实现一个并发安全的链表，你会怎么做
简述ConcurrentLinkedQueue和LinkedBlockingQueue的用处和不同之处
简述AQS的实现原理
countdowlatch和cyclicbarrier的用法，以及相互之间的差别?
concurrent包中使用过哪些类？分别说说使用在什么场景？为什么要使用？
LockSupport工具
Condition接口及其实现原理
Fork/Join框架的理解
jdk8的parallelStream的理解
分段锁的原理,锁力度减小的思考

## Spring

Spring AOP与IOC的实现原理
Spring的beanFactory和factoryBean的区别
为什么CGlib方式可以对接口实现代理？
RMI与代理模式
Spring的事务隔离级别，实现原理
对Spring的理解，非单例注入的原理？它的生命周期？循环注入的原理，aop的实现原理，说说aop中的几个术语，它们是怎么相互工作的？
Mybatis的底层实现原理
MVC框架原理，他们都是怎么做url路由的
spring boot特性，优势，适用场景等
quartz和timer对比
spring的controller是单例还是多例，怎么保证并发的安全

## 分布式相关

Dubbo的底层实现原理和机制
描述一个服务从发布到被消费的详细过程
分布式系统怎么做服务治理
接口的幂等性的概念
消息中间件如何解决消息丢失问题
Dubbo的服务请求失败怎么处理
重连机制会不会造成错误
对分布式事务的理解
如何实现负载均衡，有哪些算法可以实现？
Zookeeper的用途，选举的原理是什么？
数据的垂直拆分水平拆分。
zookeeper原理和适用场景
zookeeper watch机制
redis/zk节点宕机如何处理
分布式集群下如何做到唯一序列号
如何做一个分布式锁
用过哪些MQ，怎么用的，和其他mq比较有什么优缺点，MQ的连接是线程安全的吗
MQ系统的数据如何保证不丢失
列举出你能想到的数据库分库分表策略；分库分表后，如何解决全表查询的问题。

## 算法&数据结构&设计模式

海量url去重类问题（布隆过滤器）
数组和链表数据结构描述，各自的时间复杂度
二叉树遍历
快速排序
BTree相关的操作
在工作中遇到过哪些设计模式，是如何应用的
hash算法的有哪几种，优缺点，使用场景
什么是一致性hash
paxos算法
在装饰器模式和代理模式之间，你如何抉择，请结合自身实际情况聊聊
代码重构的步骤和原因，如果理解重构到模式？

## 数据库

MySQL InnoDB存储的文件结构
索引树是如何维护的？
数据库自增主键可能的问题
MySQL的几种优化
mysql索引为什么使用B+树
数据库锁表的相关处理
索引失效场景
高并发下如何做到安全的修改同一行数据，乐观锁和悲观锁是什么，INNODB的行级锁有哪2种，解释其含义
数据库会死锁吗，举一个死锁的例子，mysql怎么解决死锁

## Redis&缓存相关

Redis的并发竞争问题如何解决了解Redis事务的CAS操作吗
缓存机器增删如何对系统影响最小，一致性哈希的实现
Redis持久化的几种方式，优缺点是什么，怎么实现的
Redis的缓存失效策略
缓存穿透的解决办法
redis集群，高可用，原理
mySQL里有2000w数据，redis中只存20w的数据，如何保证redis中的数据都是热点数据
用Redis和任意语言实现一段恶意登录保护的代码，限制1小时内每用户Id最多只能登录5次
redis的数据淘汰策略

## 网络相关

http1.0和http1.1有什么区别
TCP/IP协议
TCP三次握手和四次挥手的流程，为什么断开连接要4次,如果握手只有两次，会出现什么
TIME_WAIT和CLOSE_WAIT的区别
说说你知道的几种HTTP响应码
当你用浏览器打开一个链接的时候，计算机做了哪些工作步骤
TCP/IP如何保证可靠性，数据包有哪些数据组成
长连接与短连接
Http请求get和post的区别以及数据包格式
简述tcp建立连接3次握手，和断开连接4次握手的过程；关闭连接时，出现TIMEWAIT过多是由什么原因引起，是出现在主动断开方还是被动断开方。

## 其他

maven解决依赖冲突,快照版和发行版的区别
Linux下IO模型有几种，各自的含义是什么
实际场景问题，海量登录日志如何排序和处理SQL操作，主要是索引和聚合函数的应用
实际场景问题解决，典型的TOP K问题
线上bug处理流程
如何从线上日志发现问题
linux利用哪些命令，查找哪里出了问题（例如io密集任务，cpu过度）
场景问题，有一个第三方接口，有很多个线程去调用获取数据，现在规定每秒钟最多有10个线程同时调用它，如何做到。
用三个线程按顺序循环打印abc三个字母，比如abcabcabc。
常见的缓存策略有哪些，你们项目中用到了什么缓存系统，如何设计的
设计一个秒杀系统，30分钟没付款就自动关闭交易（并发会很高）
请列出你所了解的性能测试工具
后台系统怎么防止请求重复提交？
有多个相同的接口，我想客户端同时请求，然后只需要在第一个请求返回结果的时候返回给客户端
