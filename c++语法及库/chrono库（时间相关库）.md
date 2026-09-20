头文件：
```cpp
#include <chrono>
```
==它把时间分为三个概念==：

## 时钟

**==用于提供时间来源

```cpp
std::chrono::system_clock
```
这个表示系统时间（从系统时间获取时间来源）
# 时间点

==**获取具体的时间点(这个才是时间)

类型：
```cpp
std::chrono::system_clock::time_point//这是一个类类型，需要实例化
```
使用：
```cpp
 std::chrono::system_clock::time_point createTime; //创建时间
std::chrono::system_clock::time_point executeTime;// 执行时间 
```

**获取当前系统时间**
```cpp
std::chrono::system_clock::now();
```

# 时间间隔

**类型**
```cpp
std::chrono::seconds
std::chrono::minutes
std::chrono::hours
```

# 时间加减
例
```cpp
auto executeTime = now + std::chrono::seconds(10);//表示now时间之后的10秒
```


