# CMake基本语法

## 语法特性介绍

* **基本语法格式：指令（参数1 参数2...）**
    * 参数使用**括弧**括起
    * 参数之间使用**空格**或**分号**分开
* **指令是大小写无关的，参数和变量是大小写相关的**

```bash

set(HELLO hello.cpp)
add_executable(hello main.cpp hello.cpp)
ADD_EXECUTABLE(hello main.cpp ${HELLO})

```

## 重要指令和CMake常用变量

### 重要指令

* **cmake_minimum_required** - 指定CMake的最小版本要求
    * 语法：**cmake_minimun_required(VERSION versionNumber [FATAL_ERROR])**

```bash
# CMake最小版本要求为2.8.3

cmake_minimum_required(VERSION 2.8.3)

```

* **project** - 定义工程名称，并可指定工程支持的语言
    * 语法：**project(projectname[CXX][C][Java])**

```bash
# 指定工程名为HELLOWORLD
project(HELLOWORLD)

```

* **set** - 显示定义变量
    * 语法：**set(VAR [VALUE][CACHE TYPE DOCSTRING [FORCE]])**

```bash
# 定义SRC变量，其值为main.cpp hello.cpp
set(SRC sayhello.cpp hello.cpp)

```

* **include_directories** - 向工程添加多个特定的头文件搜索路径 --> 相当于g++编译器的-I参数
    * 语法： **include_directories([AFTER|BEFORE][SYSTEM] dir1 dir2...)**

```bash

# 将/usr/include/myincludefolder 和 ./include 添加到头文件搜索路径
include_directories(/usr/include/myincludefolder ./lib)

```

* **link_directories** - 向工程添加多个特定的库文件搜索路径 --> 相当于g++编译器的-L参数
    * 语法：**link_directories(dir1 dir2...)**

```bash

# 将/usr/lib/mylibfoder 和 ./lib 添加到库文件搜索路径
link_directories(/usr/lib/mylibfolder ./lib)

```

* **add_library** - 生成库文件
    * 语法：add_library(libname[SHARED|STATIC|MODULE][EXCLUDE_FROM_ALL] source1 source2 ... sourceN)

```bash

# 通过变量 SRC 生成libhello.so 共享库
add_library(hello SHARED ${SRC})

```

* **add_compile_options** - 添加编译参数
    * 语法：**add_compile_options(<option>...)**

```bash

# 添加编译参数 -wall -std=c++11
add_compile_options(-wall -std=c++11 -o2)

```

* **add_executable** - 生成可执行文件
    * 语法：**add_executable(exename source1 source2 ... sourceN)**

```bash

# 编译main.cpp生成可执行文件main
add_executable(main main.cpp)

```

* **target_link_libraries** - 为target添加需要链接的共享库 --> 相当于指定g++编译器 -l参数
    * 语法：**target_link_libraries(target library1<debug | optimized> library2...)**

```bash

# 将hello动态库文件链接到可执行文件main
target_link_libraries(main hello)

```

* **add_subdirectory** - 向当前工程添加存放源文件的子目录，并可以指定中间二进制和目标二进制存放的位置
    * 语法：**add_subdirectory(source_dir [binary_dir][EXCLUDE_FROM_ALL])**

```bash

# 添加src子目录，src中需有一个CMakeLists.txt
add_subdirectory(src)

```

* **aux_source_directory** - 发现一个目录下所有的源代码文件并将列表存储在一个变量中，这个指令临时被用来自动构建源文件列表
    * 语法：**aux_source_directory(dir VARIABLE)**

```bash

# 定义SRC变量，其值为当前目录下所有的源代码文件
aux_source_directory(. SRC)
# 编译SRC变量所代表的源代码文件，生成main可执行文件
add_executable(main ${SRC})

```

### CMake常用变量

* **CMAKE_C_FLAGS** gcc编译选项
* **CMAKE_CXX_FLAGS** g++编译选项

```bash

# 在CMAKE_CXX_FLAGS编译选项后追加-std=c++11
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")

```

* **CMAKE_BUILD_TYPE**编译类型（Debug，Release）

```bash

# 设定编译类型为debug，调试时需要选择debug
set(CMAKE_BUILD_TYPE Debug)
# 设定编译类型为release，发布时需要选择release
set(CMAKE_BUILD_TYPE Release)

```

* **CMAKE_BINARY_DIR**
* **PROJECT_BINARY_DIR**
* **<projectname>_BINARY_DIR**

> 1、这三个变量指代的内容是一致的。
> 2、如果in source build，指的就是工程顶层目录。
> 3、如果是out-of-source编译，指的是工程编译发生的目录。
> 4、PROJECT_BINARY_DIR 跟其他指令稍有区别，不过现在，你可以理解为他们是一致的。

* **CMAKE_SOURCE_DIR**
* **PROJECT_SOURCE_DIR**
* **<projectname>_SOURCE_DIR**

> 1、这三个变量指代的内容是一致的，不论采用何种编译方式，都是工程顶层目录。
> 2、也就是在in source build时，他跟CMAKE_BINARY_DIR等变量一致。
> 3、PROJECT_SOURCE_DIR 跟其他指令稍有区别，现在，你可以理解为他们是一致的。

* **CMAKE_C_COMPILER**：指定C编译器
* **CMAKE_CXX_COMPILER**：指定C++编译器
* **EXECUTABLE_OUTPUT_PATH**：可执行文件输出的存放路径
* **LIBRARY_OUTPUT_PATH**：库文件输出的存放路径

## CMake编译工程

CMake目录结构：项目主目录存在一个CMakeLists.txt文件

**两种方式设置编译规则**：

1、包含源文件的子文件夹**包含**CMakeLists.txt文件，主目录的CMakeLists.txt通过add_subdirectory添加子目录即可；
2、包含源文件的子文件夹**未包含**CMakeLists.txt文件，子目录编译规则体现在主目录的CMakeLists.txt中;

## 编译流程

### 在linux平台下使用CMake构建C/C++工程的流程如下：

* 手动编写CMakeLists.txt
* 执行命令**cmake PATH生成Makefile（PATH是顶层CMakeList.txt所在的目录）**
* 执行命令**make**进行编译



## 两种构建方式

* 内部构建（in-source build）：不推荐使用

内部构建会在同级目录下产生一大堆中间文件，这些中间文件并不是我们最终所需要的，和工程源问津啊放在一起会显得杂乱无章。

```bash

## 内部构建

# 在当前目录下，编译本目录的CMakeLists.txt，生成Makefile和其他文件
cmake .

# 执行make命令，生成target
make

```

* 外部构建（out-of-source build）：推荐使用

将编译输出文件与源文件放到不同目录中

```bash

## 外部构建

# 1、在当前目录下，创建build文件夹

mkdir build

# 2、进入到build文件夹

cd build

# 3、编译上级目录的CMakeLists.txt，生成makefile和其他文件

cmake ..

# 4、执行make命令，生成target

make

```

