# STL 容器总览

| 容器                   | 特点      | 有序    | 重复      | `[]` | 竞赛重要程度 |
| -------------------- | ------- | ----- | ------- | ---- | ------ |
| `vector`             | 动态数组    | ❌     | ✅       | ✅    | ⭐⭐⭐⭐⭐  |
| `string`             | 字符串     | ❌     | ✅       | ✅    | ⭐⭐⭐⭐⭐  |
| `deque`              | 双端队列    | ❌     | ✅       | ✅    | ⭐⭐⭐⭐   |
| `list`               | 双向链表    | ❌     | ✅       | ❌    | ⭐⭐     |
| `forward_list`       | 单向链表    | ❌     | ✅       | ❌    | ⭐      |
| `stack`              | 栈       | ❌     | ✅       | ❌    | ⭐⭐⭐⭐⭐  |
| `queue`              | 队列      | ❌     | ✅       | ❌    | ⭐⭐⭐⭐⭐  |
| `priority_queue`     | 堆       | 堆序    | ✅       | ❌    | ⭐⭐⭐⭐⭐  |
| `set`                | 有序集合    | ✅     | ❌       | ❌    | ⭐⭐⭐⭐⭐  |
| `multiset`           | 有序可重复集合 | ✅     | ✅       | ❌    | ⭐⭐⭐    |
| `map`                | 有序键值对   | 按 key | key 不重复 | ✅    | ⭐⭐⭐⭐⭐  |
| `multimap`           | 有序多键值对  | 按 key | key 可重复 | ❌    | ⭐⭐⭐    |
| `unordered_set`      | 哈希集合    | ❌     | ❌       | ❌    | ⭐⭐⭐⭐   |
| `unordered_map`      | 哈希映射    | ❌     | key 不重复 | ✅    | ⭐⭐⭐⭐⭐  |
| `unordered_multiset` | 哈希可重复集合 | ❌     | ✅       | ❌    | ⭐⭐     |
| `unordered_multimap` | 哈希多键映射  | ❌     | key 可重复 | ❌    | ⭐⭐     |

---

# 三、vector 动态数组

```
vector<int> v;
```

## 1. 创建

|语法|参数作用|作用|
|---|---|---|
|`vector<T> v`|`T`：元素类型|创建空 vector|
|`vector<T> v(n)`|`n`：元素数量|创建 n 个默认初始化元素|
|`vector<T> v(n,x)`|`n`：数量，`x`：初始值|创建 n 个值为 x 的元素|
|`vector<T> v={...}`|初始化列表|初始化|

例如：

```
vector<int> a(5,10);
```

得到：

```
10 10 10 10 10
```

---

## 2. vector 常用函数

|函数|参数|返回值|作用|
|---|---|---|---|
|`push_back(x)`|`x`：元素|`void`|尾部添加|
|`pop_back()`|无|`void`|删除尾元素|
|`front()`|无|引用|第一个元素|
|`back()`|无|引用|最后一个元素|
|`size()`|无|`size_type`|元素数量|
|`empty()`|无|`bool`|是否为空|
|`clear()`|无|`void`|清空|
|`begin()`|无|迭代器|首元素|
|`end()`|无|迭代器|尾后|
|`rbegin()`|无|反向迭代器|最后元素|
|`rend()`|无|反向迭代器|反向尾后|
|`erase(pos)`|`pos`：迭代器|迭代器|删除一个元素|
|`erase(first,last)`|`[first,last)`|迭代器|删除区间|
|`insert(pos,x)`|`pos`：位置，`x`：元素|迭代器|插入|
|`resize(n)`|`n`：新大小|`void`|修改大小|
|`resize(n,x)`|`n`：大小，`x`：新增元素值|`void`|修改大小|
|`capacity()`|无|容量|当前容量|
|`reserve(n)`|`n`：容量|`void`|预留容量|

访问：

```
v[i];
v.at(i);
```

---

## vector 常见坑

### 坑 1：`size()` 是无符号类型

容易写出：

```
for(int i=v.size()-1;i>=0;i--)
```

当 `v` 为空时会出问题。

推荐：

```
for(int i=(int)v.size()-1;i>=0;i--)
```

---

### 坑 2：`erase` 可能导致迭代器失效

例如：

```
for(auto it=v.begin();it!=v.end();it++)
{
    if(*it==3)
        v.erase(it);
}
```

删除之后 `it` 可能已经失效。

推荐：

```
for(auto it=v.begin();it!=v.end();)
{
    if(*it==3)
        it=v.erase(it);
    else
        ++it;
}
```

因为 `erase` 会返回删除元素之后的迭代器。

---

### 坑 3：vector 扩容会导致迭代器/指针/引用失效

```
vector<int> v;

int &x=v[0];

v.push_back(100000);
```

如果发生扩容，之前的 `x` 可能失效。

---

### 坑 4：`reserve` 和 `resize` 不一样

```
v.reserve(100);
```

只是：

> 容量至少准备 100。

**不会让 `size()` 变成 100。**

而：

```
v.resize(100);
```

会真的创建 100 个元素。

---

# 四、string

```
string s;
```

## 常用函数

| 函数                     | 参数                  | 返回值       | 作用    |
| ---------------------- | ------------------- | --------- | ----- |
| `size()`               | 无                   | 长度        | 字符串长度 |
| `length()`             | 无                   | 长度        | 字符串长度 |
| `empty()`              | 无                   | `bool`    | 是否为空  |
| `clear()`              | 无                   | `void`    | 清空    |
| `push_back(c)`         | `c`：字符              | `void`    | 尾插字符  |
| `pop_back()`           | 无                   | `void`    | 删除尾字符 |
| `front()`              | 无                   | 引用        | 首字符   |
| `back()`               | 无                   | 引用        | 尾字符   |
| `find(str)`            | `str`：查找内容          | 位置        | 查找    |
| `substr(pos,len)`      | `pos`：起点，`len`：长度   | `string`  | 截取    |
| `erase(pos,len)`       | `pos`：起点，`len`：删除长度 | `string&` | 删除    |
| `insert(pos,str)`      | `pos`：位置，`str`：字符串  | `string&` | 插入    |
| `replace(pos,len,str)` | 起点、长度、新字符串          | `string&` | 替换    |

---

## `substr`

```
s.substr(pos,len);
```

例如：

```
string s="abcdef";

cout<<s.substr(2,3);
```

结果：

```
cde
```

注意：

> 第二个参数是**长度**，不是终点下标。

---

## `find`

```
s.find("abc");
```

返回第一次出现的位置。

如果找不到：

```
string::npos
```

因此：

```
if(s.find("abc")!=string::npos)
```

---

## string 常见坑

### 坑 1：不能直接这样判断 find

```
if(s.find("abc"))
```

如果 `"abc"` 出现在位置 0：

```
find() == 0
```

会被当成 `false`。

正确：

```
if(s.find("abc")!=string::npos)
```

---

# 五、deque 双端队列

```
deque<int> d;
```

|函数|参数|作用|
|---|---|---|
|`push_back(x)`|`x`：元素|尾部插入|
|`push_front(x)`|`x`：元素|头部插入|
|`pop_back()`|无|删除尾部|
|`pop_front()`|无|删除头部|
|`front()`|无|查看头部|
|`back()`|无|查看尾部|
|`size()`|无|大小|
|`empty()`|无|判空|
|`clear()`|无|清空|
|`begin()`|无|首迭代器|
|`end()`|无|尾后迭代器|
|`erase(pos)`|`pos`：迭代器|删除|
|`insert(pos,x)`|`pos`：位置，`x`：元素|插入|
|`resize(n)`|`n`：新大小|修改大小|

支持：

```
d[i];
```

### 典型用途

**单调队列：**

```
deque<int> q;
```

因为两端都可以操作：

```
q.push_back(x);
q.push_front(x);

q.pop_back();
q.pop_front();
```

---

# 六、list 双向链表

```
list<int> l;
```

|函数|参数|作用|
|---|---|---|
|`push_back(x)`|`x`|尾插|
|`push_front(x)`|`x`|头插|
|`pop_back()`|无|尾删|
|`pop_front()`|无|头删|
|`front()`|无|首元素|
|`back()`|无|尾元素|
|`insert(pos,x)`|`pos`：迭代器，`x`：元素|插入|
|`erase(pos)`|`pos`：迭代器|删除|
|`size()`|无|大小|
|`empty()`|无|判空|
|`clear()`|无|清空|
|`sort()`|无|排序|
|`reverse()`|无|反转|
|`unique()`|无|删除连续重复|

### 注意

没有：

```
l[i]; // ❌
```

因为链表不能随机访问。

`list` 自己提供：

```
l.sort();
```

而不是：

```
sort(l.begin(),l.end());
```

---

# 七、stack 栈

```
stack<int> st;
```

|函数|参数|作用|
|---|---|---|
|`push(x)`|`x`：元素|入栈|
|`pop()`|无|删除栈顶|
|`top()`|无|查看栈顶|
|`empty()`|无|判空|
|`size()`|无|大小|

### 常见坑

```
int x=st.pop(); // ❌
```

正确：

```
int x=st.top();
st.pop();
```

---

# 八、queue 队列

```
queue<int> q;
```

|函数|参数|作用|
|---|---|---|
|`push(x)`|`x`：元素|队尾加入|
|`pop()`|无|删除队头|
|`front()`|无|查看队头|
|`back()`|无|查看队尾|
|`empty()`|无|判空|
|`size()`|无|大小|

典型 BFS：

```
while(!q.empty())
{
    int u=q.front();
    q.pop();
}
```

---

# 九、priority_queue 优先队列

默认是：

> **大根堆**

```
priority_queue<int> q;
```

## 小根堆

```
priority_queue<int,vector<int>,greater<int>> q;
```

三个模板参数：

```
priority_queue<
    元素类型,
    底层容器,
    比较器
>
```

例如：

```
priority_queue<int,vector<int>,greater<int>>
```

分别是：

```
int
vector<int>
greater<int>
```

---

## 函数

|函数|参数|作用|
|---|---|---|
|`push(x)`|`x`：元素|插入|
|`pop()`|无|删除堆顶|
|`top()`|无|查看堆顶|
|`empty()`|无|判空|
|`size()`|无|大小|

### 常见坑

```
q.top();
```

只是查看。

```
q.pop();
```

才是删除。

---

# 十、set

特点：

> **有序 + 不重复**

没有push_back,pop_back等操作，因为它是自动有序的，无法做到精确的往后面加入，插入元素排序后可能在集合中的任何位置


```
set<int> s;
```

| 函数               | 参数        | 作用        |                                              |
| ---------------- | --------- | --------- | -------------------------------------------- |
| `insert(x)`      | `x`：元素    | 插入        |                                              |
| `erase(x)`       | `x`：值     | 删除值       |                                              |
| `erase(pos)`     | `pos`：迭代器 | 删除元素      |                                              |
| `find(x)`        | `x`：值     | 查找        | 返回迭代器，不是元素位置，它不像数组一样可以直接下标访问，找不到元素时返回s.end() |
| `count(x)`       | `x`：值     | 判断存在/个数   | 找到返回1，没找到返回0                                 |
| `lower_bound(x)` | `x`       | 第一个 `>=x` |                                              |
| `upper_bound(x)` | `x`       | 第一个 `>x`  |                                              |
| `size()`         | 无         | 大小        |                                              |
| `empty()`        | 无         | 判空        |                                              |
| `clear()`        | 无         | 清空        |                                              |
| `begin()`        | 无         | 最小元素      |                                              |
| `end()`          | 无         | 尾后        |                                              |

例如：

```
set<int> s={5,3,1,3,2};
```

结果：

```
1 2 3 5
```

### 坑

不能：

set底层不是数组实现的，是用红黑树实现的，节点之间靠指针连接 **==所以set的迭代器是双向迭代器，不支持随机访问==**

```
s[0]; // ❌
```

---

# 十一、multiset

和 `set` 基本一样：

> **有序 + 可以重复**

```
multiset<int> s;
```

### 特别重要的坑

```
s.erase(3);
```

会删除：

> **所有值为 3 的元素。**

如果只删除一个：**要用具体迭代器才能只删除一个元素**

```
auto it=s.find(3);

if(it!=s.end())
    s.erase(it);
```

---

# 十二、map

特点：

> **key 有序 + key 不重复 + key-value**

```
map<string,int> mp;//第一个是键，第二个是值

```

|操作|语法|说明|
|---|---|---|
|访问|`mp[key]`|根据 key 访问 value|
|插入|`mp[key]=value`|插入/修改|
|插入|`mp.insert({key,value})`|插入键值对|
|删除|`erase(key)`|删除 key|
|查找|`find(key)`|查找|
|判断|`count(key)`|判断存在|
|二分|`lower_bound(key)`|第一个 `>=key`|
|二分|`upper_bound(key)`|第一个 `>key`|
|大小|`size()`|元素数量|
|判空|`empty()`|是否为空|
|清空|`clear()`|清空|

遍历：

```
for(auto p:mp)
{
    cout<<p.first<<" "<<p.second;
}
```
**p.first返回键，p.second 返回值

---

## map 最大的坑

```
map<int,int> mp;

cout<<mp[100];
```

==即使 `100` 不存在==：

```
mp[100]
```

也会==**创建这个 key**==。

所以：

```
mp[x]++;
```

特别适合统计次数。**假如现在有一个关于key的计数问题，那么就可以直接用map，因为，你只需要初始化创建一个map，然后用mp\[x]++,这样子不论是以前的key，还是新的key都能成功加1**

但如果只是想判断是否存在：

```
if(mp.find(x)!=mp.end())
```

或者：

```
if(mp.count(x))
```

不要随便：

```
mp[x];
```

---

# 十三、multimap

```
multimap<int,string> mp;
```

允许：

```
1 -> Tom
1 -> Bob
1 -> Alice
```

即：

> 一个 key 可以对应多个 value。

主要函数：

| 函数                    | 参数  | 作用                  |
| --------------------- | --- | ------------------- |
| `insert({key,value})` | 键值对 | 插入                  |
| `erase(key)`          | key | 删除该 key 的==所有==对应元素 |
| `find(key)`           | key | 查找                  |
| `count(key)`          | key | 统计 key 数量           |
| `lower_bound(key)`    | key | 第一个 `>=key`         |
| `upper_bound(key)`    | key | 第一个 `>key`          |

### 注意

没有：
**因为没有唯一对应关系**
```
mp[key]; // ❌
```

---

# 十四、unordered_set

哈希集合：

```
unordered_set<int> s;
```

|函数|参数|作用|
|---|---|---|
|`insert(x)`|x|插入|
|`erase(x)`|x|删除|
|`find(x)`|x|查找|
|`count(x)`|x|判断|
|`size()`|无|大小|
|`empty()`|无|判空|
|`clear()`|无|清空|

与 `set`：

```
set
    有序
    O(log n)

unordered_set
    无序
    平均 O(1)
```

### 注意

`unordered_set`：

```
for(auto x:s)
```

遍历顺序**不能依赖**。

---

# 十五、unordered_map

```
unordered_map<string,int> mp;
```

|函数|参数|作用|
|---|---|---|
|`mp[key]`|key|访问/修改|
|`insert({key,value})`|键值对|插入|
|`erase(key)`|key|删除|
|`find(key)`|key|查找|
|`count(key)`|key|判断|
|`size()`|无|大小|
|`empty()`|无|判空|
|`clear()`|无|清空|

最常见用途：

```
unordered_map<int,int> cnt;

for(int x:a)
    cnt[x]++;
```

---

# 十六、迭代器

STL 中大量函数都是：

```
[first,last)
```

形式。

例如：

```
sort(a.begin(),a.end());
```

这里：

```
a.begin()
↓
第一个元素

a.end()
↓
最后一个元素的下一个位置
```

---

## 常用迭代器

|语法|含义|
|---|---|
|`begin()`|首元素|
|`end()`|尾后|
|`rbegin()`|最后一个元素|
|`rend()`|反向尾后|
|`*it`|访问迭代器指向元素|
|`++it`|向后|
|`--it`|向前|

---

## 反向遍历

```
for(auto it=a.rbegin();it!=a.rend();++it)
{
    cout<<*it;
}
```

---

# 十七、algorithm 核心算法

头文件：

```
#include<algorithm>
```

---

## 1. sort

```
sort(first,last);
```

|参数|作用|
|---|---|
|`first`|排序起点|
|`last`|尾后位置|

升序：

```
sort(a.begin(),a.end());
```

降序：

```
sort(a.begin(),a.end(),greater<int>());
```

自定义：

```
sort(a.begin(),a.end(),cmp);
```

---

# 2. reverse

```
reverse(first,last);
```

|参数|作用|
|---|---|
|`first`|起点|
|`last`|尾后|

```
reverse(a.begin(),a.end());
```

---

# 3. find

```
find(first,last,x);
```

|参数|作用|
|---|---|
|`first`|起点|
|`last`|尾后|
|`x`|要查找的值|

返回：

> 指向找到元素的迭代器，找不到返回 `last`。

例如：

```
auto it=find(a.begin(),a.end(),5);

if(it!=a.end())
{
    cout<<"找到";
}
```

---

# 4. lower_bound

```
lower_bound(first,last,x);
```

|参数|作用|
|---|---|
|`first`|起点|
|`last`|尾后|
|`x`|目标值|

返回：

> 第一个 `>= x` 的位置。

**要求区间有序。**

---

# 5. upper_bound

```
upper_bound(first,last,x);
```

返回：

> 第一个 `> x` 的位置。

同样要求有序。

---

# 6. binary_search

```
binary_search(first,last,x);
```

参数：

- `first`：起点
- `last`：尾后
- `x`：目标

返回：

```
bool
```

判断：

> x 是否存在。

同样要求有序。

---

# 7. min / max

```
min(a,b);
max(a,b);
```

参数：

- `a`
- `b`

返回较小/较大的值。

---

# 8. min_element

```
min_element(first,last);
```

返回：

> 最小元素的迭代器。

所以：

```
cout<<*min_element(a.begin(),a.end());
```

---

# 9. max_element

```
max_element(first,last);
```

返回最大元素迭代器。

---

# 10. unique

```
unique(first,last);
```

作用：

> 将连续重复元素压缩到前面。

**不会真正删除 vector 的元素。**

正确去重：

```
sort(a.begin(),a.end());

a.erase(
    unique(a.begin(),a.end()),
    a.end()
);
```

---

# 11. fill

```
fill(first,last,x);
```

参数：

|参数|作用|
|---|---|
|`first`|起点|
|`last`|尾后|
|`x`|填入的值|

例如：

```
fill(a.begin(),a.end(),0);
```

---

# 12. swap

```
swap(a,b);
```

交换两个对象。

例如：

```
int a=10,b=20;

swap(a,b);
```

---

# 13. remove

```
remove(first,last,x);
```

把等于 `x` 的元素移到逻辑末尾。

**不会真正改变容器 size。**

如果真的要删除：

```
a.erase(
    remove(a.begin(),a.end(),x),
    a.end()
);
```

---

# 14. replace

```
replace(first,last,old_value,new_value);
```

参数：

- `first`：起点
- `last`：尾后
- `old_value`：旧值
- `new_value`：新值

例如：

```
replace(a.begin(),a.end(),3,100);
```

所有 `3` 替换成 `100`。

---

# 十八、numeric

```
#include<numeric>
```

---

## accumulate

```
accumulate(first,last,init);
```

参数：

|参数|作用|
|---|---|
|`first`|起点|
|`last`|尾后|
|`init`|初始值|

例如：

```
int sum=accumulate(
    a.begin(),
    a.end(),
    0
);
```

---

## 一个非常重要的坑

```
vector<int> a;
```

如果：

```
accumulate(a.begin(),a.end(),0)
```

计算过程中通常以 `int` 为累加类型。

如果数据可能很大：

```
long long sum=accumulate(
    a.begin(),
    a.end(),
    0LL
);
```

**`0` 和 `0LL` 会影响累加类型。**

---

# 十九、比较器

这是 STL 后面非常重要的一块。

---

## less

```
less<int>()
```

通常表示：

```
小 → 大
```

---

## greater

```
greater<int>()
```

表示：

```
大 → 小
```

例如：

```
sort(a.begin(),a.end(),greater<int>());
```

---

# 二十、自定义比较器

例如：

```
sort(a.begin(),a.end(),
    [](int a,int b)
    {
        return a<b;
    }
);
```

比较器接收：

```
a
b
```

返回：

```
true
```

表示：

> `a` 应该排在 `b` 前面。

---

例如按照绝对值排序：

```
sort(a.begin(),a.end(),
    [](int a,int b)
    {
        return abs(a)<abs(b);
    }
);
```

---

# 二十一、竞赛 STL 高频坑总表

```
# STL 常见坑

## 1. vector 的 size() 是无符号类型

不要轻易写：

for(int i=v.size()-1;i>=0;i--)

推荐：

for(int i=(int)v.size()-1;i>=0;i--)


## 2. vector 扩容会导致迭代器、指针、引用失效

push_back() 之后如果发生扩容：

原来的迭代器/指针/引用可能全部失效。


## 3. erase 之后原迭代器可能失效

vector 中：

it = v.erase(it);

是常见正确写法。


## 4. reserve 和 resize 不一样

reserve(n)：
只增加容量，不增加 size。

resize(n)：
真正修改元素数量。


## 5. stack::pop() 没有返回值

错误：

int x=st.pop();

正确：

int x=st.top();
st.pop();


## 6. queue::pop() 没有返回值

错误：

int x=q.pop();

正确：

int x=q.front();
q.pop();


## 7. priority_queue 默认是大根堆

priority_queue<int>

最大值在 top()。


## 8. priority_queue 的 top() 只查看，不删除

q.top();
q.pop();

两者作用不同。


## 9. set 不能使用下标

s[0] // 错误


## 10. map 的 [] 访问不存在 key 会创建元素

mp[x]

如果 x 不存在，会创建：

x -> 默认值


## 11. multiset::erase(x) 会删除所有 x

如果只删除一个：

auto it=s.find(x);
if(it!=s.end())
    s.erase(it);


## 12. multimap 没有 []

因为一个 key 可以对应多个 value。


## 13. unordered_map / unordered_set 无序

不能依赖遍历顺序。


## 14. find 的返回值不是下标

find() 返回迭代器。

例如：

auto it=find(a.begin(),a.end(),x);

要访问：

*it


## 15. find 找不到时返回 end()

判断：

if(it!=a.end())


## 16. string::find 找不到返回 string::npos

正确：

if(s.find("abc")!=string::npos)


## 17. substr 第二个参数是长度，不是终点

s.substr(2,3)

表示：

从下标2开始取3个字符。


## 18. lower_bound / upper_bound 要求区间有序

lower_bound：
第一个 >= x

upper_bound：
第一个 > x


## 19. unique 不是真正删除元素

正确：

a.erase(unique(a.begin(),a.end()),a.end());


## 20. remove 不是真正删除元素

正确：

a.erase(remove(a.begin(),a.end(),x),a.end());


## 21. accumulate 注意初始值类型

0：
通常以 int 累加。

0LL：
使用 long long 累加。


## 22. sort 的区间是左闭右开

sort(a.begin(),a.end());

排序：

[first,last)

即包含 first，不包含 last。


## 23. list 没有随机访问

l[i] // 错误

list 应该通过迭代器访问。


## 24. list 使用自己的 sort()

l.sort();

而不是：

sort(l.begin(),l.end());


## 25. 容器为空时不要访问 front/back/top

例如：

q.front();

如果 q 为空，行为未定义。

应该：

if(!q.empty())
    q.front();


## 26. pop_front/pop_back/pop/top 等删除函数通常不返回元素

先获取，再删除。


## 27. 比较器不能随便写

sort 的比较器：

return true;

表示 a 应该排在 b 前面。

比较器必须满足严格弱序关系，不能出现互相矛盾的排序规则。
```