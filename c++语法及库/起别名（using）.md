# 语法
```cpp
using 新名字 = 原类型;
//或
typedef 原类型 新名字;
```

# 作用
1. 简化类型名，比如一些库里名字很长的类型名
2. 如果原来用的库有问题，或者想换库，只需要改using，源码不需要改（**解耦，对变化进行隔离**）
3. 不暴露底层思想（别人不知道用了什么库）


# using 定义模版别名

```cpp
template<typename T>
using mymap =map<int,T>
mymap<string> a;//定义map的value为string
mymap<int> b;//定义map的value为int
```
