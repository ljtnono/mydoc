# Class装载系统

## 虚拟机加载的流程图





## 类初始化的条件

1、当创建一个类的实例时，会对类进行初始化。例如：使用new关键字或者反射、克隆、反序列化。
2、当调用类的静态方法时，即使用了字节码invokestatic指令。
3、当使用类或接口的静态字段时（final常量除外），比如使用getstatic或者putstatic指令。
4、当使用java.lang.reflect包中的方法反射类的方法时。
5、当初始化子类时，要求先初始化父类。
6、作为启动虚拟机，含有main()方法的类会被初始化。

以上六种情况下属于主动使用类或者接口，会对类进行初始化，其他情况不会对类进行初始化。

例题1:

```java

public class Parent {

    static {
        System.out.println("Parent init");
    }

}


public class Child extends Parent{

    static {
        System.out.println("Child init");
    }

}

public class InitMain {

    public static void main(String[] args) {
        Child child = new Child();
        // 使用new创建子类对象，先初始化父类
    }

}


```

运行结果:

```bash

Parent init
Child init

```


例题2:

```java

public class Parent {

    static {
        System.out.println("Parent init");
    }

    public static int v = 100;

}

public class Child extends Parent {

    static {
        System.out.println("Child init");
    }

}


public class UseParent {

    public static void main(String[] args) {
        System.out.println(Child.v);
    }

}


```

运行结果:

```bash

Parent init
100

```

可以看到，虽然在UseParent中直接访问了子类对象，但是Child子类并未被初始化，只有Parent父类被初始化。可见，在使用一个字段时，只有直接定义该字段的类才会被初始化。


例题3:

```java

public class FinalFieldClass {

    public static final String constString = "CONST";

    static {
        System.out.println("FinalFieldClass init");
    }

}

public class UseFinalField {

    public static void main(String[] args) {
        System.out.println(FinalFieldClass.constString);
    }

}


```

运行结果：

```bash

CONST

```

FinalField类并没有因为常量字段constString被引用而进行初始化。这是因为在Class文件生成时，final常量由于不变性，做了适当的优化。FinalField字段在编译后直接存放在常量池中，因此FinalFieldClass类自然不会被加载。

注意：并不是在代码中出现的类就一定会被加载或者初始化。如果不符合主动使用的条件，类就不会被初始化。


## 加载类

加载类处于类装载的第一个阶段。在加载类时，Java虚拟机必须完成以下工作：

1、通过类的全名获取类的二进制数据流。
2、解析类的二进制数据流为方法区内的数据结构。
3、创建java.lang.Class类的实例，表示该类型。


对于类的二进制数据流，虚拟机可以通过多种途径产生或获得。一般虚拟机可能通过文件系统读入一个class后缀的文件，或者也可能读入JAR、ZIP等归档数据包，提取类文件。除了这些形式，任何形式都是可以的。比如，事先将类的二进制数据存放在数据库中，或者通过类似于HTTP之类的协议通过网络进行加载，甚至在运行时生成一段Class的二进制信息。

## 验证类

当类加载到系统后，就开始连接操作，验证是连接操作的第一步。它的目的是保证加载的字节码是合法、合理并符合规范的。验证的步骤比较复杂，实际要验证的项目也很繁多，大体上Java虚拟机需要做的检查如图所示。


## 准备

当一个类验证通过时，虚拟机就会进入准备阶段。在这个阶段，虚拟机会为这个类分配相应的内存空间，并设置初始值。Java虚拟机各类型变量默认的初始值如表所示。

        
| 类型      | 默认初始值 |
|-----------|:-----------|
| int       | 0          |
| long      | 0L         |
| short     | (short)0   |
| char      | \u0000     |
| boolean   | false      |
| reference | null       |
| float     | 0f         |
| double    | 0f         |


## 解析类





## 初始化

类的初始化是类装载的最后一个阶段。如果前面的步骤都没有问题，表示类可以顺利装载到系统中。此时，类才会开始执行Java字节码。初始化阶段的重要工作是执行类的初始化方法<clinit>。方法<clinit>是由编译器自动生成的，它是由类静态成员的赋值语句及static语句块共同产生的。



## 类加载器和双亲委派

ClassLoader在Java中有着非常重要的作用，它主要工作在Class装载的加载阶段，主要作用是从系统外部获得Class二进制数据流。
