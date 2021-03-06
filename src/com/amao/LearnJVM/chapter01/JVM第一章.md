# 1.首先来看看计算机系统当中JVM所处的位置

![1846149-20200401104340482-1757970137](/Users/mao/Desktop/java/JVM/Chapter01/images/1846149-20200401104340482-1757970137.png)

​			重点:JVM是运行在操作系统之上的，并没有和硬件有直接的交互



# 2.Java一次编译,到处运行

![1846149-20200403091940968-1617936617](/Users/mao/Desktop/java/JVM/Chapter01/images/1846149-20200403091940968-1617936617.png)

每个字节码文件对于一个类

JAVA编译器（前端编译器）

编译器环节任何一个失败了，都不能生成字节码文件

# ![image-20200429194908948](/Users/mao/Desktop/java/JVM/Chapter01/images/image-20200429194908948.png)

操作系统并不识别字节码指令，所以需要后端编译器，JIT解释为机器指令。由执行引擎完成。

**第一次编译**：把源文件编译成字节码文件

**第二次编译**：将字节码文件编译为机器指令。同时因为机器指令他是需要反复执行的，将热点代码缓存起来了（JIT），提高性能。所以由翻译字节码、JIT编译器两部分组成

![image-20200429195145051](/Users/mao/Desktop/java/JVM/Chapter01/images/image-20200429195145051.png)

Java编译器输入的指令流基本上是一种基于**栈的指令集架构**，另外一种指令集架构则
是基于寄存器的指令集架构。
具体来说:这两种架构之间的区别:
●**基于栈式架构的特点**
➢设计和实现更简单，适用于资源受限的系统; .
➢避开了寄存器的分配难题:使用零地址指令方式分配。
即是一个栈的操作，我们只需要关心栈顶
➢指令流中的指令大部分是零地址指令，其执行过程依赖于操作栈。指令集更小, [但相比于寄存器操作更多，下图有解释].
编译器容易实现。
➢不需要硬件支持，可移植性更好，更好实现跨平台。
栈是一个内存层面，不跟硬件打交道
**基于寄存器架构的特点**
➢典型的应用是x86的二进制指令集:比如传统的PC以及Android的Davlik虛
拟机。
➢指令集架构则完全依赖硬件，可移植性差
➢性能优秀和执行更高效: 
因为基于cpu，比较快，对硬件耦合度较高
➢花费更少的指令去完成一项操作。
➢在大部分情况下，基于寄存器架构的指令集往往都以一地址指令、二地址指令
和三地址指令为主，而基于栈式架构的指令集却是以零地址指令为主。

# 3.JVM体系结构概览

![1846149-20200401103726898-763616456](/Users/mao/Desktop/java/JVM/Chapter01/images/1846149-20200401103726898-763616456.png)

1.方法区和堆区是所有线程共享的内存区域；而java栈、本地方法栈和程序员计数器是运行是线程私有的内存区域。

2.Java栈又叫做jvm虚拟机栈

3.方法区（永久代）在jdk8中又叫做元空间Metaspace

- 方法区用于存储已被虚拟机加载的类信息、常量、静态变量、即时编译器（JIT编译器，英文写作Just-In-Time Compiler）编译后的代码等数据。虽然Java虚拟机规范把方法区描述为堆的一个逻辑部分，但是它却有一个别名叫做 Non-Heap（非堆），目的应该是与 Java 堆区分开来。
- 在JDK1.7之前运行时常量池逻辑包含字符串常量池存放在方法区, 此时hotspot虚拟机对方法区的实现为永久代
- 在JDK1.7 字符串常量池被从方法区拿到了堆中, 这里没有提到运行时常量池,也就是说字符串常量池被单独拿到堆,运行时常量池剩下的东西还在方法区, 也就是hotspot中的永久代
- 在JDK1.8之后JVM 已经将运行时常量池从方法区中移了出来，在 Java 堆（Heap）中开辟了一块区域存放运行时常量池。同时在 jdk 1.8中移除整个永久代，取而代之的是一个叫元空间（Metaspace）的区域

4.java代码执行流程：

　　java程序--（编译javac）-->字节码文件.class-->类装载子系统化身为反射类Class--->运行时数据区--->（解释执行）-->操作系统（Win，Linux，Mac JVM）

# 4.栈的指令集架构和寄存器的指令集架构

```
由于跨平台的设计，java的指令都是根据栈来设计的，不同平台CPU架构不同，所以不能设计为基于寄存器的
```

二者区别：

```txt
栈：跨平台性、指令集小、指令多；执行性比寄存器差
寄存器：指令少
```

一些简单查看命令：

```java
//查看指令集命令代码
cd out/production/类根目录

//反编译
javap -v .class文件

//打印程序执行的进程
jps
```

# 5.jvm生命周期

## 1.启动

通过引导类加载器（bootstrap class loader）创建一个初始类（initial class）来完成的，这个类是由虚拟机的具体实现指定的.

## 2.执行

- 一个运行中的java虚拟机有着一个清晰的任务：执行Java程序；
- 程序开始执行的时候他才运行，程序结束时他就停止；
- 执行一个所谓的Java程序的时候，真真正正在执行的是一个叫做Java虚拟机的进程。

## 3.退出

- 程序正常执行结束
- 程序异常或错误而异常终止
- 操作系统错误导致终止
- 某线程调用Runtime类或System类的exit方法，或Runtime类的halt方法，并且java安全管理器也允许这次exit或halt操作
- 除此之外，JNI规范描述了用JNI Invocation API来加载或卸载Java虚拟机时，Java虚拟机的退出情况



# 6.JVM发展历史

![image-20200430001351315](/Users/mao/Desktop/java/JVM/Chapter01/images/image-20200430001351315.png)

