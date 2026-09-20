cmake是一个构建器，它负责生成编译规则，告诉真正的编译器，比如g++如何编译这个项目

![[photo/Pasted image 20260824104922.png]]



核心文件CMakeLists.txt：它是编译说明书，cmake启动第一时间就是找这个文件

基本语句含义：
```cpp
cmake_minimum_required(VERSION 3.20) //表示项目要求最低的cmake版本
```

```cpp
project(TaskScheduler)//给项目起名字，未来运行项目会生成TaskScheduler.exe

```

```cpp
set(CMAKE_CXX_STANDARD 17)//设置c++标准，比如这里设置c++17为标准
```
指定头文件
```cpp
include_directories(${PROJECT_SOURCE_DIR}/include)//告诉cmake头文件在哪找
```

```cpp
add_executable( //添加可执行源文件
TaskScheduler//表示想生成一个TaskScheduler程序，想当于起名

main.cpp //这个程序由这些可执行cpp文件组成
User.cpp 
UserService.cpp
 )
```

 需要在源文件目录下创建一个==build==文件夹，**实现源码和编译产物分离**，也就是简单来说，生成的exe文件不能单独在源码文件夹下
```
TaskScheduler

├── src
├── include
├── CMakeLists.txt

└── build
    ├── exe
    ├── 临时文件
    └── 编译缓存
```

# 编译流程：

1. 先在项目build文件下，通过终端执行（先找到编译规则）
	 ```cpp
	cmake .. //cmake：启动cmake     .. ：表示当前目录的上一级目录，也就是TaskScheduler，里面有CMakeLists.txt
	 ```
==执行后会生成编译规则，但还没有开始编译==
2. 然后执行（开始编译）
```cpp
windows：cmake --build .
Linux：make//生成makefile文件
```
==开始编译，编译完成后生成exe文件和一些编译缓存



# 库制作（将源代码变为二进制机器码给别人用）

当项目大起来之后，如果全部cpp文件都塞到add_executable 里会非常乱

可以将源代码按功能，模块等拆成若干个**库**然后add_executable只放主程序，也就是程序入口main,然后再通过链接功能，把库和主程序链接起来，==表示主程序依赖于什么库==
## 静态库
例如：

1. **用户库** 
```cpp
add_library( 
user //相当于起名
STATIC//指定为静态库

User.cpp
UserRepository.cpp 
UserService.cpp )//含义：生成一个名叫user的代码库，库中有这些可执行cpp文件
```
2. **任务库**
```cpp
add_library( 
task 
STATIC
Task.cpp 
TaskRepository.cpp )
```
...
3. **主程序**
```cpp
add_executable( 
TaskScheduler 
STATIC
main.cpp )
```
4. **进行库与主程序的链接**
```cpp
target_link_libraries( 
TaskScheduler//这个是target
user//这两个是库
task 
 )
```

## 动态库
动态库的物理内存有且仅有一份，被共享
```cpp
add_library( 
user //相当于起名
SHARED//指定生成为动态库加SHARED宏

User.cpp
UserRepository.cpp 
UserService.cpp )//含义：生成一个名叫user的代码库，库中有这些可执行cpp文件
```
**==库文件发布时要同时包含头文件和库==**



## 链接为静态库的方法（全局）
**==可以是库与可执行程序链接，也可以是库与库之间链接等等==**

用这个方法在它后面的可执行程序都会带上这些库
不推荐使用！！一般只用于全局的静态库
```cpp
link_libraries(库名称 ....)//如果该库不在环境变量里（自定义库），还需要把库的地址链接起来，链接在可执行程序生成前！！！！！！！！！！！
link_directories(库的地址)//让cmake找到库的地址
add_executable(....)
```
==静态库链接后会打包一份到可执行程序里面，而动态库不会

## 链接的方法（局部）

==既可以链接静态库也能链接动态库，且实际开发更推荐用！！！==

```cpp

link_directories(动态库地址) //同样需要把库所在地址链接起来
add_executable(...)
target_link_directories(//链接在可执行程序（target）之后
	target
	
	 权限 库名...
	 权限 库名...
	 ...

)
```

target可以是
1. 源文件
2. 别的动态库文件
3. 可执行文件
4. 等待可以链接库的东西
权限：==PRIVATE==（==没有传递性==，只有当前target可使用，别的target链接现在这个target就不能用了），==PUBLIC==(默认）（保留传递性），==INTERFACE==（target仅仅知道库函数接口，但不知道有哪些库，也不知道具体哪个函数是哪个库提供的，只导出符号，==所以也不具有传递性==）
# 文件搜索

痛点：在加载可执行文件是，需要自己手动一个个罗列cpp文件，当文件很多时会十分繁琐

**aux_source_directory（文件路径，变量）：**所有该路径下的源文件会变成字符串形式存在变量中


```cpp
aux_source_directory(${PROJECT_SOURCE_DIR} SRC)
```

==PROJECT_SOURCE_DIR这是一个宏，是当我们执行**cmake 文件名** 时提取到的文件名的路径

SRC是变量


**file(GLOB或GLOB_RECURSE(递归)  变量名  文件路径/文件类型（比如*.cpp)**
```cpp
filr(GLOB SRC 路径/*.cpp) //找当前路径的所有.cpp文件
```


# set

**作用1**：初始化变量（所有变量默认为字符串类型）

**set(变量名  字符串（源文件名称)）

**==如何取变量值==**：${  变量名   }   **这样才能取到正确的变量值，或者是宏的值

**作用2：指定使用的c++标准**

**set(CMAKE_CXX_STANDARD   标准（98,11,14,17...))

CMAKE_CXX_STANDARD这是指定的宏，稍微记一下

**作用3：指定输出路径

**set(EXECUTABLE_OUTPUT_PATH 路径)//指定可执行文件的输出路径
set(LIBRARY_OUTPUT_PATH 路径)//指定库的输出路径

路径不存在，无需手动创建


# 日志

**默认重要程度：重要信息**



![[photo/Pasted image 20260820162220.png]]


# 变量操作（Cmake一切皆字符串）

## 追加

1. **set拼接** 

```cpp
set(变量名 字符串1 字符串2 ...)
set(变量名 ${变量名1} ${变量名2})
```

2. list拼接
```cpp
list(APPEND 变量名 ${变量名1}/字符串 ${变量名2}/字符串...)
```
list底层管理是用；来分割各个子串的


## 移除

1. list移除
```cpp
list(REMOVE_ITEM 变量 子字符串 ...)//必须是完整的一个子字符串


比如：ab；cd；ok
那么只能删除ab或者cd或者ok，不能删d，因为不是完整的一个子字符串
```

## 获取长度

```cpp
list(LENTH 变量名 新创建的变量（用于存储当前列表的长度)
```
注意：==长度依旧是字符串类型，不是整型

## 读取列表中指数索引的元素

```cpp
list(GET 列表变量名 索引 输出变量名（返回指定索引元素的输出)
```

## 将列表元素用连接符连接起来

```cpp
list(JOIN 列表变量 指定连接符 输出变量)
```

## 查找列表是否存在指定元素
```cpp
list(FIND 列表变量名 指定要找的元素 输出变量)
```

## 指定位置插入元素

```cpp
list(INSERT 列表变量名 插入位置 要插入的元素...)
```

 
 
 ## 头插法

```cpp
list(PREPEND 列表变量名 要插入的字符串)
```

## 移除最后元素
```cpp
list(POP_BACK 列表变量名 存储被弹出元素的变量（可不写）)
```

## 移除第一个元素

```cpp
list(POP_FRONT 列表变量名 存储被弹出元素的变量（可不写）)
```

## 指定索引元素从列表移除

```cpp
list(REMOVE_AT 列表变量名 索引 )
```

## 移除列表中的重复元素
```cpp
list(REMOVE_DUPLICATES 列表变量名)
```

## 列表翻转
```cpp
list(REVERSE 列表变量名)
```

## 列表排序
```cpp
list(SORT 列表变量名 排序方法 大小写是否敏感 升序还是降序关键字)
```
![[photo/Pasted image 20260823112147.png]]

# 宏定义
==主要作用：通过定义宏控制调试代码是否生效，并且不在源代码中定义宏，而是仅在测试阶段在Cmake上把宏定义出来触发调试代码，更方便日后发布不用频繁删减头文件==
```cpp
add_difinitions(-D 宏的名称)
```


# 嵌套的Cmake

一个完整目录中可以存在多个CmakeLists.txt形成树状模块结构
（因为Linux的目录是树状结构的）

**==核心作用：使每个CmakeLists.txt功能的更单一，编写起来更简单==**

**CmakeLists.txt的变量作用域**：
1. 根节点CmakeLists.txt中定义变量是全局有效的
2. 父节点的CmakeLists.txt变量可以在子节点中使用
3. 子节点的CmakeLists.txt只能在当前节点中使用
**结构样例**

![[photo/Pasted image 20260823114534.png]]

**根节点可以定义好，库路径，头文件路径，指定库生成路径等等的变量供其他节点使用**（但可读性差）

**==在根目录产生最终的可执行程序**==

**在根目录CMakeLists.txt 统一引入头文件**

## 添加子目录

![[photo/Pasted image 20260823114109.png|700]]
后面两个参数一般不用

### **样例
根节点：
![[photo/Pasted image 20260823115315.png]]
calc节点：（把calc变为库）
![[photo/Pasted image 20260823120016.png]]
text1节点：（利用calc库生成可执行程序）

![[photo/Pasted image 20260823120354.png]]


# 引入第三方库的方法

```cpp
find_package(第三方库名字  REQUIRED)//这个函数可以找到第三方库的源文件，头文件，和一些cmake配置（比如帮我们定义一些变量，例如：OpenCV_LIBS等，第三方库的源文件和头文件等等，都是通过这些变量返回的）REQUIRED表示必须，如果构建过程中找不到第三方库就直接报错停止，否则会真正编译报错时才停止

add_executable(
....

)


target_include_directories(Vision PRIVATE ${OpenCV_INCLUDE_DIRS} ) 


target_link_libraries(Vision PRIVATE ${OpenCV_LIBS} )



```





