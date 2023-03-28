# JVM类加载流程

一个简单的HelloWorld.java

```java
package cn.ljtnono.demo;

public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("hello world");
    }
}
```

执行javac -parameters -d . HelloWorld.java

上述命令 -parameters 是保留参数的名称信息，-d . 表示生成的文件在当前文件夹下

编译为HelloWorld.class后是这个样子的：(通过vscode安装hexdump for vscode 插件查看)

```
  Offset: 00 01 02 03 04 05 06 07 08 09 0A 0B 0C 0D 0E 0F 	
00000000: CA FE BA BE 00 00 00 34 00 1F 0A 00 06 00 11 09    J~:>...4........
00000010: 00 12 00 13 08 00 14 0A 00 15 00 16 07 00 17 07    ................
00000020: 00 18 01 00 06 3C 69 6E 69 74 3E 01 00 03 28 29    .....<init>...()
00000030: 56 01 00 04 43 6F 64 65 01 00 0F 4C 69 6E 65 4E    V...Code...LineN
00000040: 75 6D 62 65 72 54 61 62 6C 65 01 00 04 6D 61 69    umberTable...mai
00000050: 6E 01 00 16 28 5B 4C 6A 61 76 61 2F 6C 61 6E 67    n...([Ljava/lang
00000060: 2F 53 74 72 69 6E 67 3B 29 56 01 00 10 4D 65 74    /String;)V...Met
00000070: 68 6F 64 50 61 72 61 6D 65 74 65 72 73 01 00 04    hodParameters...
00000080: 61 72 67 73 01 00 0A 53 6F 75 72 63 65 46 69 6C    args...SourceFil
00000090: 65 01 00 0F 48 65 6C 6C 6F 57 6F 72 6C 64 2E 6A    e...HelloWorld.j
000000a0: 61 76 61 0C 00 07 00 08 07 00 19 0C 00 1A 00 1B    ava.............
000000b0: 01 00 0B 68 65 6C 6C 6F 20 77 6F 72 6C 64 07 00    ...hello.world..
000000c0: 1C 0C 00 1D 00 1E 01 00 1A 63 6E 2F 6C 6A 74 6E    .........cn/ljtn
000000d0: 6F 6E 6F 2F 64 65 6D 6F 2F 48 65 6C 6C 6F 57 6F    ono/demo/HelloWo
000000e0: 72 6C 64 01 00 10 6A 61 76 61 2F 6C 61 6E 67 2F    rld...java/lang/
000000f0: 4F 62 6A 65 63 74 01 00 10 6A 61 76 61 2F 6C 61    Object...java/la
00000100: 6E 67 2F 53 79 73 74 65 6D 01 00 03 6F 75 74 01    ng/System...out.
00000110: 00 15 4C 6A 61 76 61 2F 69 6F 2F 50 72 69 6E 74    ..Ljava/io/Print
00000120: 53 74 72 65 61 6D 3B 01 00 13 6A 61 76 61 2F 69    Stream;...java/i
00000130: 6F 2F 50 72 69 6E 74 53 74 72 65 61 6D 01 00 07    o/PrintStream...
00000140: 70 72 69 6E 74 6C 6E 01 00 15 28 4C 6A 61 76 61    println...(Ljava
00000150: 2F 6C 61 6E 67 2F 53 74 72 69 6E 67 3B 29 56 00    /lang/String;)V.
00000160: 21 00 05 00 06 00 00 00 00 00 02 00 01 00 07 00    !...............
00000170: 08 00 01 00 09 00 00 00 1D 00 01 00 01 00 00 00    ................
00000180: 05 2A B7 00 01 B1 00 00 00 01 00 0A 00 00 00 06    .*7..1..........
00000190: 00 01 00 00 00 03 00 09 00 0B 00 0C 00 02 00 09    ................
000001a0: 00 00 00 25 00 02 00 01 00 00 00 09 B2 00 02 12    ...%........2...
000001b0: 03 B6 00 04 B1 00 00 00 01 00 0A 00 00 00 0A 00    .6..1...........
000001c0: 02 00 00 00 05 00 08 00 06 00 0D 00 00 00 05 01    ................
000001d0: 00 0E 00 00 00 01 00 0F 00 00 00 02 00 10          ..............
```

## 类文件结构

根据JVM规范，类文件结构如下

![image-20191209232911588](http://f.lingjiatong.cn:30090/rootelement/articleQuote/image-20191209232911588.png)

* 魔数

  魔数占4个字节

  00000000: **CA FE BA BE** 00 00 00 34 00 1F 0A 00 06 00 11 09

  上面一排中的CAFEBABE是魔数，用于标示文件是什么类型

* 小版本号(minor_version)和大版本号（major_version)

  版本号占4个字节（00 00 00 34）代表十进制52,52对应的是jdk8, 51对应jdk7,53对应jdk9

  00000000: CA FE BA BE **00 00 00 34** 00 1F 0A 00 06 00 11 09

* 常量池

  ![image-20191222214348488](http://f.lingjiatong.cn:30090/rootelement/articleQuote/image-20191222214348488.png)

  常量池占据了class文件中比较大的一部分

  8-9字节，表示常量池长度 ，**00 1F**（31）表示常量池有#1~#31项，注意#0项不计入，也没有值

  00000000: CA FE BA BE 00 00 00 34 00 1F **0A 00 06 00 11** 09

  

  第#1项0A表示一个Method信息，00 06 和 00 11 表示它引用了常量池中#6和#17项来获得这个方法的【所属类】和【方法名】

  

  第#2项09表示一个Field信息，后面的00 12 和 00 13 表示它引用常量池中#18和#19项来获得这个成员变量的【所属类】和【成员变量名】

  00000000: CA FE BA BE 00 00 00 34 00 1F 0A 00 06 00 11 **09**

  00000010: **00 12 00 13** 08 00 14 0A 00 15 00 16 07 00 17 07

  

  第#3项 08表示一个字符串常量名称，00 14 表示它引用了常量池中#20项

  00000010: 00 12 00 13 **08 00 14** 0A 00 15 00 16 07 00 17 07

  

  第#4项 0A表示一个Method信息，00 15 和 00 16 表示这个项引用了#21和#22项来获取method的【所属类】和【方法名】

  00000010: 00 12 00 13 08 00 14 **0A 00 15 00 16** 07 00 17 07

  

  第#5项 07表示一个Class信息，00 17 表示引用常量池第#23项

  00000010: 00 12 00 13 08 00 14 0A 00 15 00 16 **07 00 17** 07

  

  第#6项 07表示Class信息，00 18 表示引用#24项

  00000010: 00 12 00 13 08 00 14 0A 00 15 00 16 07 00 17 **07**

  00000020: **00 18** 01 00 06 3C 69 6E 69 74 3E 01 00 03 28 29

  

  第#7项 01表示utf8串，00 06表示长度，3C 69 6E 69 74 3E是【<init>】

  00000020: 00 18 01 **00 06 3C 69 6E 69 74 3E** 01 00 03 28 29

  

  第#8项 01表示一个utf8串， 00 03表示长度， 28 29 56是【()V】其实就是表示无参、无返回值

  00000020: 00 18 01 00 06 3C 69 6E 69 74 3E **01 00 03 28 29**

  00000030: **56** 01 00 04 43 6F 64 65 01 00 0F 4C 69 6E 65 4E

  

  第#9项 01表示一个utf8串， 00 04表示长度，43 6F 64 65是【Code】

  00000030: 56 **01 00 04 43 6F 64 65** 01 00 0F 4C 69 6E 65 4E

  

  第#10项 01表示utf8串，00 0F表示长度，4C 69 6E 65 4E 75 6D 62 65 72 54 61 62 6C 65是【LineNumberTable】

  00000030: 56 01 00 04 43 6F 64 65 01 **00 0F 4C 69 6E 65 4E**

  00000040: **75 6D 62 65 72 54 61 62 6C 65** 01 00 04 6D 61 69 

  

  第#11项 01表示utf8串， 00 04表示长度， 6D 61 69 6E表示【main】函数

  00000040: 75 6D 62 65 72 54 61 62 6C 65 **01 00 04 6D 61 69** 

  00000050: **6E** 01 00 16 28 5B 4C 6A 61 76 61 2F 6C 61 6E 67 

  

  第#12项 01表示utf8串， 00 16表示长度， 28 5B 4C 6A 61 76 61 2F 6C 61 6E 67 2F 53 74 72 69 6E 67 3B 29 56表示【([Ljava/lang/String;)V】参数为字符串数组返回值为void

  00000050: 6E 01 00 16 **28 5B 4C 6A 61 76 61 2F 6C 61 6E 67** 

  00000060: **2F 53 74 72 69 6E 67 3B 29 56** 01 00 10 4D 65 74

  

  以此类推

  

* 访问标识和继承信息

  ![image-20191222223114646](http://f.lingjiatong.cn:30090/rootelement/articleQuote/image-20191222223114646.png)

  

* 成员变量信息

  ![image-20191222223530647](http://f.lingjiatong.cn:30090/rootelement/articleQuote/image-20191222223530647.png)

  * B表示字节类型
  * C表示char类型
  * D表示double类型
  * F表示float类型
  * I表示int类型
  * J表示long类型
  * L ClassName ;表示引用类型
  * S表示short类型
  * Z表示字符串类型
  * [表示数组类型

* Method信息

  method信息比较复杂，这里不做详细描述，通常包括方法名，方法返回值，方法修饰符，方法参数，方法code中的代码，这其中涉及到常量池的引用，还有**操作数栈的深度，局部变量表的长度**

* 附加属性

  格式类似于常量池，属性类型，长度，值

  参考文献：[https://docs.oracle.com/javase/specs/jvms/se8/html/index.html](https://docs.oracle.com/javase/specs/jvms/se8/html/index.html)

## 字节码指令

常见字节码指令可以在jvm规范中查看，这里不再详述

### javap工具

自己分析类文件结构太麻烦了，Oracle提供了javap工具来反编译class文件

```java
$ javap -v HelloWorld.class
Classfile /E:/learnjava/jvm/src/main/java/cn.ljtnono/demo/cn/ljtnono/demo/HelloWorld.class
  Last modified 2019-12-22; size 478 bytes
  MD5 checksum 4930285069f4aa869bbd022d3d884d7a
  Compiled from "HelloWorld.java"
public class cn.ljtnono.demo.HelloWorld
  minor version: 0
  major version: 52
  flags: ACC_PUBLIC, ACC_SUPER
Constant pool:
   #1 = Methodref          #6.#17         // java/lang/Object."<init>":()V
   #2 = Fieldref           #18.#19        // java/lang/System.out:Ljava/io/PrintStream;
   #3 = String             #20            // hello world
   #4 = Methodref          #21.#22        // java/io/PrintStream.println:(Ljava/lang/String;)V
   #5 = Class              #23            // cn/ljtnono/demo/HelloWorld
   #6 = Class              #24            // java/lang/Object
   #7 = Utf8               <init>
   #8 = Utf8               ()V
   #9 = Utf8               Code
  #10 = Utf8               LineNumberTable
  #11 = Utf8               main
  #12 = Utf8               ([Ljava/lang/String;)V
  #13 = Utf8               MethodParameters
  #14 = Utf8               args
  #15 = Utf8               SourceFile
  #16 = Utf8               HelloWorld.java
  #17 = NameAndType        #7:#8          // "<init>":()V
  #18 = Class              #25            // java/lang/System
  #19 = NameAndType        #26:#27        // out:Ljava/io/PrintStream;
  #20 = Utf8               hello world
  #21 = Class              #28            // java/io/PrintStream
  #22 = NameAndType        #29:#30        // println:(Ljava/lang/String;)V
  #23 = Utf8               cn/ljtnono/demo/HelloWorld
  #24 = Utf8               java/lang/Object
  #25 = Utf8               java/lang/System
  #26 = Utf8               out
  #27 = Utf8               Ljava/io/PrintStream;
  #28 = Utf8               java/io/PrintStream
  #29 = Utf8               println
  #30 = Utf8               (Ljava/lang/String;)V
{
  public cn.ljtnono.demo.HelloWorld();
    descriptor: ()V
    flags: ACC_PUBLIC
    Code:
      stack=1, locals=1, args_size=1
         0: aload_0
         1: invokespecial #1                  // Method java/lang/Object."<init>":()V
         4: return
      LineNumberTable:
        line 3: 0

  public static void main(java.lang.String[]);
    descriptor: ([Ljava/lang/String;)V
    flags: ACC_PUBLIC, ACC_STATIC
    Code:
      stack=2, locals=1, args_size=1
         0: getstatic     #2                  // Field java/lang/System.out:Ljava/io/PrintStream;
         3: ldc           #3                  // String hello world
         5: invokevirtual #4                  // Method java/io/PrintStream.println:(Ljava/lang/String;)V
         8: return
      LineNumberTable:
        line 5: 0
        line 6: 8
    MethodParameters:
      Name                           Flags
      args
}
SourceFile: "HelloWorld.java"
```



## 类加载的阶段

### 加载

* 将类的字节码载入方法区中，内部采用C++的instanceKlass描述java类，它的重要field有
  * _java_mirror即java的类镜像，例如对String来说，就是String.class,作用是吧klass暴露给java使用
  * _super即父类
  * _fields即成员变量
  * _methods即方法
  * _constants即常量池
  * _class_loader即类加载器
  * _vtable虚方法表
  * _itable接口方法表
* 如果这个类还有父类没有加载，先加载父类
* 加载和链接可能是交替运行的

> 注意
>
> * instanceKlass这样的【元数据】是存储在方法区（1.8后的元空间内）
> * 可以通过HSDB工具查看

![image-20191214130628817](http://f.lingjiatong.cn:30090/rootelement/articleQuote/image-20191214130628817.png)



### 链接阶段

* 验证：验证类是否符合JVM规范，安全性检查

  用UE等支持二进制的编辑器修改Hello.class的魔数，在控制台运行

  ![image-20191214131017396](http://f.lingjiatong.cn:30090/rootelement/articleQuote/image-20191214131017396.png)

* 准备：为static变量分配空间，设置默认值
  * static变量在JDK7之前存储于instanceKlass末尾，从JDK7开始，存储于_java_mirror末尾
  * static变量分配空间和赋值是两个步骤,分配空间是准备阶段完成，赋值在初始化阶段完成
  * 如果static变量是final的基本类型，以及字符串常量，那么编译期阶段值就确定了，赋值在准备阶段完成
  * 如果static变量是final的，但属于引用类型，那么赋值也会在初始化阶段完成
* 解析：将常量池中的符号引用解析为直接引用



### 初始化

<cinit>()v方法

初始化即调用<cinit>()v，虚拟机会保证这个类的【构造方法】的线程安全

#### 发生的时机

概括的说，类的初始化是【懒惰的】

* main方法所在的类，总是会被首先初始化
* 首次访问这个类的静态变量或静态方法时
* 子类初始化，如果父类还没初始化，会引发
* 子类访问父类的静态变量，只会触发父类的初始化
* Class.forName
* new会导致初始化

不会导致类初始化的情况

* 访问类的static final静态常量（基本类型和字符串）不会触发初始化
* 类对象.class不会触发初始化
* 创建该类的数组不会触发初始化
* 类加载器的loadClass方法
* Class.forName的参数2为false时

