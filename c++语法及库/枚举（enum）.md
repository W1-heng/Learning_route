
枚举（Enumeration）是一种**用户自定义数据类型**，用于表示一组**有限且有名字的==整数常量==**。

核心作用：**提高程序可读性**

# 传统枚举

## 语法
```cpp
enum Week { Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday };//定义week枚举类型
Week day = Monday;//构造week枚举类型实体
```

枚举成员默认从 **0开始递增**

也就是说上面的定义式等价于
```cpp
enum Week { Monday = 0, Tuesday = 1, Wednesday = 2... };
```
枚举成员数字可以自定义，**允许重复**

==输出枚举类型输出的是它背后的整数==
例如：
```cpp
cout<<day;
//输出0
```
## 坑
```cpp
Color c = 1; //错误!!!!!!!!
```

==整型无法自动变为枚举类型！！！！！==

## 经典用法

状态机：
```CPP
enum State
{
    Idle,
    Running,
    Jumping,
    Dead
};


void update(State state)
{
    switch(state)
    {
        case Idle:
            cout<<"站立";
            break;

        case Running:
            cout<<"跑步";
            break;

        case Jumping:
            cout<<"跳跃";
            break;

        case Dead:
            cout<<"死亡";
            break;
    }
	}//用有名字的整数替代整数，提高程序可读性。
```

## 作用域问题

传统枚举没有自己的作用域，而是进入**外部作用域**（**全局区**），所以经常引起==重命名问题==


```cpp
enum Color
{
    Red,
    Green
};


enum TrafficLight
{
    Red,
    Yellow,
    Green
};//Red和Green重定义错误！！！
```

原因：**实际上并不存在Color::Red**，==而是Red直接存在==

# C++11 新枚举 （enum class）

## 语法
```cpp
enum class 枚举名 { 

枚举值

 };
```

## 实例化(必须加作用域！！！！)
```cpp
Color c = Color::Red;//必须加定义域才能使用
```

## 好处
1. 解决命名污染：==每个变量有对应的枚举类型作用域，防止重定义==
2. 类型更加安全：
```cpp
//传统enum
int x =red;//允许
//enum class
int x =color::red//不允许
int x =static_cast<int> color::red//必须强转，不能直接枚举成员变整型
```
## enum class指定底层类型

enum class 的底层类型可以**自定义指定**，==节约内存空间==
```cpp
enum class Color : unsigned char //此时成员都是unsigned char类型，仅占一个字节
{ 
Red, 
Green,
Blue 
 };
```



# enum vs enum class 总结

| 特点     | enum  | enum class |
| ------ | ----- | ---------- |
| C++版本  | C++98 | C++11      |
| 作用域    | 全局污染  | 独立作用域      |
| 类型安全   | 弱     | 强          |
| 隐式转int | 可以    | 不可以        |
| 推荐程度   | 旧代码   | 现代C++推荐    |
| 工程使用   | 少     | 多          |