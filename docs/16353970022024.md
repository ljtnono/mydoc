# Python从入门到精通

## 5. 列表与元组

在数学里，序列也称为数列，是指按照一定顺序排列的一列数，而在程序设计中，序列是一种常用的数据存储方式，几乎每种程序设计语言都提供了类似的数据结构，如C语言或Java中的数组等。在Python中，序列是最基本的数据结构。它是一块用于存放多个值的连续内存空间。Python中内置了5个常用的序列结构，分别是`列表、元组、集合、字典和字符串`。

序列是一块用于存放多个值的连续内存空间，并且按一定顺序排列，每个值（称为元素）都分配一个数字，称为索引或位置。通过该索引可以取出相应的值。例如，我们可以把一家酒店看作一个序列，那么酒店里的每个房间都可以看作是这个序列的元素。而房间号就相当于索引，可以通过房间号找到对应的房间。**在Python中，序列结构主要有列表、元组、集合、字典和字符串。对于这些序列结构有以下几个通用的操作。其中，集合和字典不支持索引、切片、相加和相乘操作。**

### 5.1 序列概述

#### 5.1.1 索引

序列中的每个元素都有一个编号，也称为索引（indexing）。这个索引是从0开始递增的，即下表为0表示第一个元素，下标为1表示第二个元素，以此类推。python中索引可以为负数，为负数时代表从右往左计数，即最后一个元素的索引值为-1，倒数第二个元素的索引值为-2，以此类推。

通过索引可以访问序列中的任何元素。例如，定义一个包括4个元素的列表，要访问它的第三个元素和最后一个元素，可以使用下面的代码。

```python
verse = ["自古逢秋悲寂寥", "我言秋日胜春朝", "晴空一鹤排云上", "便引诗情到碧霄"]
print(verse[2]) # 输出第三个元素
print(verse[3]) # 输出最后一个元素
```

输出的结果为显示文字：

```python
晴空一鹤排云上
便引诗情到碧霄
```

#### 5.1.2 切片

切片（sliceing）操作是访问序列中元素的另外一种方法，它可以访问一定范围内的元素。通过切片操作可以生成一个新的序列。实现切片操作的语法如下：

```python
sname[start : end : step]
```

参数说明如下：

* sname：表示序列的名称
* start：表示切片的开始位置（包括该位置），如果不指定，则默认为0；
* end：表示切片的截止位置（不包括该位置），如果不指定，则默认为序列的长度；
* step：表示切片的步长，如果省略，则默认为1，当省略该步长时，最后一个冒号也就可以省略。

> 进行切片操作时，如果指定了步长，那么在对该切片进行遍历时，会按照步长进行遍历
>
> 如果想复制整个序列，可以将start和end参数都省略，但是中间的冒号需要保留

```python
verse = ["青青园中葵", "朝露待日晞", "阳春布德泽", "万物生光辉", "常恐秋节至", "焜黄华叶衰", 
"百川东到海", "何时复西归", "少壮不努力", "老大徒伤悲"]

print(verse[1:6])
print(verse[1:6:2])

for data in verse[1:6:2]:
    print(data)
print(verse[:])
##
# ['朝露待日晞', '阳春布德泽', '万物生光辉', '常恐秋节至', '焜黄华叶衰']
# ['朝露待日晞', '万物生光辉', '焜黄华叶衰']
# 朝露待日晞
# 万物生光辉
# 焜黄华叶衰
# ["青青园中葵", "朝露待日晞", "阳春布德泽", "万物生光辉", "常恐秋节至", "焜黄华叶衰", "百川东到海", "何时复西归", "少壮不努力", "老大徒伤悲"]
```

**特别需要强调的是，在python中切片是深拷贝，即被复制的对象作为一个一个新的个体独立存在，任意改变其一不会对另一个对象造成影响。**

#### 5.1.3 序列相加

在Python中，支持两种相同类型的序列相加（adding）操作。即将两个序列进行连接，**不会去除重复的元素**，使用（+）运算符实现。例如，将两个列表相加，可以使用下面的代码。

```python
verse1 = ["自古逢秋悲寂寥", "我言秋日胜春朝", "晴空一鹤排云上", "便引诗情到碧霄", "hello world"]
verse2 = ["青青园中葵", "朝露待日晞", "阳春布德泽", "万物生光辉", "常恐秋节至", "焜黄华叶衰", 
"百川东到海", "何时复西归", "少壮不努力", "老大徒伤悲", "hello world"]

print(verse1 + verse2)

# ['自古逢秋悲寂寥', '我言秋日胜春朝', '晴空一鹤排云上', '便引诗情到碧霄', 'hello world', '青青园中葵', '朝露待日晞', '阳春布德泽', '万物生光辉', '常恐秋节至', '焜黄华叶衰', '百川东到海', '何时复西归', '少壮不努力', '老大徒伤悲', 'hello world']
```

> 在进行序列相加时，相同类型的序列是指，同为列表、元组、字符串等，序列中的元素类型可以不同。

#### 5.1.4 乘法

在Python中，使用数字n乘以一个序列会生成新的序列。新序列的内容为原来序列被重复n次的结果。

```python
verse = ["自古逢秋悲寂寥", "我言秋日胜春朝", "晴空一鹤排云上", "便引诗情到碧霄", "hello world"]
print(verse * 2)
# ['自古逢秋悲寂寥', '我言秋日胜春朝', '晴空一鹤排云上', '便引诗情到碧霄', 'hello world', '自古逢秋悲寂寥', '我言秋日胜春朝', '晴空一鹤排云上', '便引诗情到碧霄', 'hello world']
```

#### 5.1.5 检查某个元素是否是序列的成员

在Python中，可以使用`in`关键字检查某个元素是否是序列的成员，即检查某个元素是否包含在该序列中。另外，在Python中也可以使用`not in`关键字检查某个元素不是序列的成员。

```python
# 语法 value in sequence

verse1 = ["自古逢秋悲寂寥", "我言秋日胜春朝", "晴空一鹤排云上", "便引诗情到碧霄", "hello world"]
print("自古逢秋悲寂寥" in verse1)
print("111" not in verse1)
# True
# False
```

#### 5.1.6 计算序列的长度、最大值和最小值

在Python中，提供了内置函数计算序列的长度、最大值和最小值。分别是：使用`len()`函数计算序列的长度，即返回序列包含多少个元素；使用`max()`函数返回序列中的最大元素；使用`min()`函数返回序列中的最小元素。

```python
num = [1, 2, 3, 4, 5]
print(min(num))
print(max(num))
print(len(num))

# 1
# 5
# 5
```

`max()`和`min()`函数可以传递多个参数

```python
print(max(1, 2))
print(min(1, 2))
# 2
# 1
```

### 5.2 列表

Python中，使用"[]"包裹的一组序列称为列表，两个元素之间用","分隔，在内容上，可以将整数、实数、字符串、列表、元组等任何类型的内容放入列表中，并且同一个列表中，元素的类型可以不同，因为他们之间没有任何关系。由此可见，Python中的列表是非常灵活的，这一点与其他语言是不同的。



## 10. 面向对象

### 10.2 类的定义和使用

#### 10.2.1 定义类

在Python中，类的定义使用class关键字来实现，语法如下：

```python
class ClassName:
  '''类的帮助信息'''
  statement
```

参数说明如下：

* ClanssName

  用于指定类名，一般使用大驼峰形式

* '''类的帮助信息'''

  用于指定类的文档字符

* statement

  类体，主要由类变量（或类成员）、方法和属性等定义语句组成。如果在定义类时，没想好类的具体功能，也可以在类体中直接使用pass语句代替

```python
class Geese:
  '''大雁类'''
  pass
```

#### 10.2.2 创建类的实例

```python
class Geese:
  '''大雁类'''
  pass

# 创建大雁的实例
wildGeese = Geese()
```

#### 10.2.3 创建\_\_init\_\_()方法

在Python中，类的构造函数用`__init__()`表示。每当创建一个类的实例时，Python都会自动执行它。该方法必须包含一个self参数，并且必须是第一个参数。self参数是一个指向实例本身的引用，用于访问类中的属性和方法。在方法调用时会自动传递实际参数self。因此，当`__init__()`方法只有一个参数时，在创建类的实例时，就不需要指定实际参数了。

```python
class Geese:
  '''大雁类'''
  
  def __init__(self):
    print("我是大雁类")

wildGoose = Geese()

# 我是大雁类
```

> 实际上，如果没有定义`__init__`方法，python会自动定义一个`__init__`方法，该方法是继承自Object对象

#### 10.2.4 创建类的成员并访问

1、创建实例方法并访问

所谓实例方法，是指在类中定义的函数。该函数是一种在类的实例上操作的函数。同__init__()方法一样，实例方法的第一个参数必须是self，并且必须包含一个self参数。创建实例方法的语法格式如下：

```python
def functionName(self, parameterlist):
  # block
```

参数说明如下：

* functionName：用于指定方法名，一般使用小写字母开头
* self：必要参数，表示类的实例，其名称可以是self以外的单词，使用self只是一个习惯而已
* parameterlist：用于指定除self参数以外的参数，个参数间使用“,”进行分割
* block：方法体，实现的具体功能

```python
# 类的定义和使用

class Geese:
    '''大雁'''
    
    def __init__(self, beak, wing, claw):
        print("我是大雁类!我有以下特征：")
        print(beak)
        print(wing)
        print(claw)
    
    def functionName(self, parameterlist):
        pass

if __name__ == "__main__":
    beak_1 = "基部较高，长度和头部的长度几乎相等"
    wing_1 = "翅膀长而尖"
    claw_1 = "爪子是蹼状的"
    wildGoose = Geese(beak_1, wing_1, claw_1)
    
```

2、创建数据成员并访问

```python
# 类的定义和使用

class Geese:
    '''大雁'''
    neck = "脖子较长"
    wing = "振翅频率高"
    leg = "腿位于身体的中心支点，行走自如"
    def __init__(self):
        print("我是雁类!我有以下特征：")
        print(Geese.neck)
        print(Geese.wing)
        print(Geese.leg)
    
    def functionName(self, parameterlist):
        pass

if __name__ == "__main__":
    wildGoose = Geese()
    
```

数据成员是指在类中定义的变量，即属性，根据定义位置，又可以分为类属性和实例属性。

```python

class Geese:
    '''大雁'''
    neck = "脖子较长"  # 类属性
    wing = "振翅频率高"  # 类属性
    leg = "腿位于身体的中心支点，行走自如"  # 类属性

    def __init__(self, name):
        self.name = name  # 实例属性
        print("我是雁类!我有以下特征：")
        print(Geese.neck)
        print(Geese.wing)
        print(Geese.leg)
        print("我叫" + self.name)


if __name__ == "__main__":
    wildGoose = Geese("tom")
    
# 我是雁类!我有以下特征：
# 脖子较长
# 振翅频率高
# 腿位于身体的中心支点，行走自如
# 我叫tom
```

#### 10.2.5 访问控制

类似于java语言，python中的类属性和方法也具有相应的访问限制控制，具体如下

* 以单下划线开头（例如：`_name, def _getName(self)`）的属性或方法只能本类及其子类能够访问（protected）
* 以双下划线开头（例如：`__name, def __getName(self)`）的属性或方法只能本类能够访问（private）
* 首位双下划线表示定义特殊方法，一般是系统定义名字，如：`__init__()`







## 11. 模块

在Python中，一个扩展名为.py的文件就称为一个模块。

在通常情况下，我们把能够实现某一特定功能的代码放置在一个文件中作为一个模块，从而方便其他程序和脚本导入并使用。另外，使用模块也可以避免函数名和变量名冲突。

### 11.2 自定义模块

#### 11.2.4 模块搜索目录

当使用import语句导入模块时，默认情况下，会按照以下顺序进行查找。

**（1）在当前目录（即执行的Python脚本文件所在目录）下查找。**

**（2）到PYTHONPATH（环境变量）下的每个目录中查找。**

**（3）到Python的默认安装目录下查找。**

可以使用sys模块的path变量的值来查看以上的具体目录路径

```python
import sys
print(sys.path)

['/Users/lingjiatong/Desktop/oss-es/src/util', '/Users/lingjiatong/Desktop/oss-es', '/Applications/PyCharm.app/Contents/plugins/python/helpers/pycharm_display', '/Library/Frameworks/Python.framework/Versions/3.9/lib/python39.zip', '/Library/Frameworks/Python.framework/Versions/3.9/lib/python3.9', '/Library/Frameworks/Python.framework/Versions/3.9/lib/python3.9/lib-dynload', '/Users/lingjiatong/Desktop/oss-es/venv/lib/python3.9/site-packages', '/Applications/PyCharm.app/Contents/plugins/python/helpers/pycharm_matplotlib_backend']
```

如果要导入的模块不在以上目录中，那么在导入模块的时候python会报`No Module named xxx`异常，这个时候需要通过以下3种方式添加指定的目录到sys.path中。

1、临时添加

临时添加即在导入模块的Python文件中添加

```python
import
sys.path.append("xxxx")
```

通过该方式添加的目录只在执行当前文件的窗口中有效，窗口关闭后即失效

2、添加.pth文件（推荐）

在Python安装目录下的Lib\site-packages子目录中，创建一个扩展名为.pth的文件，文件名任意。在该文件中添加要导入模块所在的目录

```python
/Users/lingjiatong/code/test
```

3、在PYTHONPATH环境变量中添加

在计算机上添加PYTHONPATH环境变量，值为需要添加的模块路径



### 11.3 Python中的包

使用模块可以避免函数名和变量名重名引发的冲突。那么，如果模块名重复应该怎么办呢？在Python中，提出了包（Package）的概念。包是一个分层次的目录结构，它将一组功能相近的模块组织在一个目录下。这样，既可以起到规范代码的作用，又能避免模块名重名引起的冲突。

> 说明
>
> 包简单理解就是`文件夹`，只不过在该文件下必须存在一个名称为`__init__.py`的文件

#### 11.3.2 创建和使用包

1、创建包

实际上就是创建一个文件夹，并且在该文件夹中创建一个名称为`__init__.py`的Python文件，在这个文件中可以不编写任何代码，也可以编写一些Python代码。在`__init__.py`文件中所编写的代码，在导入包时会自动执行。

> 说明
>
> `__init__.py`文件是一个模块文件，模块名为对应的包名。例如，在settings包中创建的`__init__.py`文件，对应的模块名为settings

2、使用包

* 使用 `import + 完整包名 + 模块名`形式加载指定模块

```python
import util.configutil
```

通过该方式导入模块后，在使用时需要使用完整的名称

```python
print(util.configutil.xxfun())
# 调用util包下cofigutil模块中的xxfun函数
```

* 通过`from + 完整包名 + import + 模块名`形式加载指定模块

```python
from util import configutil
```

通过该方式导入模块后，在使用时只需要使用模块名就可以调用模块中函数、变量、类等

```python
print(configutil.xxfun())
print(configutil.MYSQL_CONFIG)
```

* 通过`from + 完整包名 + 模块名 + import + 定义名`加载指定模块中的某个定义

```python
from util.configutil import MYSQL_CONFIG
from util.configutil import xxfun
```

通过该方式导入模块的函数、变量或类后，在使用时直接使用函数、变量或类名即可。

3、以主程序的方式执行

> 在每个模块的定义中都包括一个记录模块名称的变量`__name__`,程序可以检查该变量，以确定他们在哪个模块中执行。
>
> 如果一个模块不是被导入其他程序中执行，那么它可能在解释器的顶级模块中执行。顶级模块的`__name__`变量的值为
>
> `__main__`

```python
import util.configutil

if __name__ == "__main__":
  print(util.configutil.MYSQL_CONFIG)
```



## 12. 异常处理及程序调试







## 13. 文件及目录操作

### 13.1 基本文件操作

#### 13.1.1 创建和打开文件





## 18. 使用进程和线程

### 18.2 创建进程的常用方式

在Python中有多个模块可以创建进程，比较常用的有os.fork()函数、multiprocessing模块和Pool进程池。由于os.fork()只适合在UNIX/LINUX/MAC系统上运行，在WINDOWS操作系统中不可用，所以本章重点介绍后两个。

#### 18.2.1 使用multiprocessing模块创建进程

multiprocessing模块提供了一个Process类代表一个进程对象，语法如下：

```python
Process([group [, target [, name [, args [, kwargs]]]]])
```

Process类的参数说明如下：

* group: 参数未使用，值始终未None
* target：表示当前进程启动时执行的可调用对象
* name：为当前进程实例的别名
* args：表示传递给target函数的参数元组
* kwargs：表示传递给target函数的参数字典

例如，实例化Process类，执行子进程，代码如下：

```python
from multiprocessing import Process


# 执行子进程代码
def test(interval):
    print("我是子进程")

def main():
    print("主进程开始")
    p = Process(target=test, args=(1,))
    p.start()
    print("主进程结束")


if __name__ == "__main__":
    main()
    

# 主进程开始
# 主进程结束
# 我是子进程
```

