---
title: 【提高组】第三课：STL 学习笔记
date: 2024-01-26 16:38:13
tags:
- 提高组
- 算法
- STL
categories: 学习笔记
---

# STL

[第三课：STL](https://www.517coding.com/contests/1142)

## A：水果店

[水果店](https://www.517coding.com/contests/1142/problem/A)

考察 map 的使用

map 可以将一个数据类型映射到另一个数据类型

### map 的定义

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    map<int, int> mp;
    mp[10] = 5;
    mp[20] = 1;
    mp[15] = 2;
    mp[1000000000] = 4;
    cout << mp.size() << endl;
    return 0;
}
```

以上代码会输出 $3$

### map 的遍历

#### 迭代器

因为 map 是一种容器，所以只能用迭代器进行访问

```cpp
for (map<int, int>::iterator it = mp.begin(); it != mp.end(); it++) {
    cout << it->first << " " << it->second << endl;
}
```

这样可以对 map 进行排序

如果你利用这份代码进行输出的话，你会发现：输出的结果会自动排序，先按第一维关键字（`it->first`）进行从小到大，再按第二维关键字（`it->second`）从小到大进行排序

#### auto 进行遍历

我们发现刚刚的代码太长了，有没有什么办法可以让代码变短一点呢？

答案是有的（建议充分理解上面的方法后再使用以下方法）

使用 C++11 的 auto

```cpp
for (auto it : mp) {
    cout << it.first << " " << it.second;
}
```

值得注意的是，如果使用迭代器，输出就是 `it->first` 和 `it->second`

如果使用 auto，输出就是 `it.first` 和 `it.second`



### map 的用法

`map<int,int> mp` 定义

`mp[10]=100` 映射

`mp.find(10)` 查找（第一关键字） ,找到返回得迭代器，找不到返回 `mp.end`



map 可以很方便的进行计数，统计，因为它是一个容器，第一关键字和第二关键字是不需要管大小的

但是它的效率不是 $O(1)$ 的，而是 $O(logn)$ 



本题的朴素版写法（迭代器）

```cpp
#include <bits/stdc++.h>
using namespace std;

//map 是可以叠加定义的，但是要注意中间的空格，低版本的编译器可能会报错
map<string,map<string,int> >mp;
int main() {
    int n;
    scanf("%d", &n);
    string shuiguo, city;
    int xiaoliang;
    for(int i = 0; i < n; i++) {
        cin >> shuiguo >> city >> xiaoliang;
        mp[city][shuiguo] += xiaoliang;
    }
    map<string,map<string,int> >::iterator it1;
    map<string,int>::iterator it2;
    //遍历整个容器
    //先遍历外面map<string, map<string, int> >
    for(it1 = mp.begin(); it1 != mp.end(); it1++) {
        cout << it1->first << endl;
        //再遍历里面的map<string, int>
        for(it2 = it1->second.begin(); it2 != it1->second.end(); it2++)
        {
            cout << "   |----" << it2->first << "(" << it2->second << ")" << endl;
        }
    }
    return 0;
}
```

因为这样写代码量太多了，所以就有了更简短的方法

本题的高端写法（auto）

```cpp
#include <bits/stdc++.h>
using namespace std;

map<string, map<string, int> > mp;
int main() {
    int n;
    scanf("%d", &n);
    string shuiguo, city;
    int xiaoliang;
    for(int i = 0; i < n; i++) {
        cin >> shuiguo >> city >> xiaoliang;
        mp[city][shuiguo] += xiaoliang;
    }
    //auto直接在for循环里面用 ":" 遍历
    for (auto it1 : mp) {
        cout << it1.first << endl;
        for (auto it2 : it1.second) {
            cout << "   |----" << it2.first << "(" << it2.second << ")" << endl;
        }
    }
    return 0;
}
```

## Set

[set](https://www.517coding.com/contests/1142/problem/B)

### set 的用法

**注意：set是集合，自动去重**

#### 基本用法

`set<int> st` 定义

`st.insert(x)` 插入 $x$ （默认从小到大排序）

遍历：

```cpp
for (set<int>::iterator it = st.begin(); it != st.end(); it++) {
    cout << *it << endl;
}
```

或者

```cpp
for (auto it : st) {
    cout << it << endl;
}
```

**最值、删除和查找**

最小值为 `*st.begin()` 

最大值为 `*(--st.end())` 或 `*(st.rbegin())` 

要注意的是：迭代器只能 ++ 或 - - ，如果换成 +1 或 -1 就会报错

删除有两种方式

1. `st.erase(10)`
2. `st.erase(it)`

也就是说 `st.erase()` 函数的参数既可以是一个数值，也可以是一个迭代器

但是如果删除一个并不存在的数值或者迭代器，就会出现一些奇奇怪怪的 bug，因此，在执行删除操作之前，一般要查找这个元素是否存在

`st.find(10)` 查找，找到返回迭代器，否则返回 `st.end()`

#### lower_bound 和 upper_bound

1. `st.lower_bound(10)` 返回大于等于 $10$ 的第一个迭代器位置，如果没有返回 `st.end()`
2. `st.upper_bound(10)` 返回第一个严格大于 $10$ 的迭代器位置，如果没有分会 `st.end()`

利用这两个函数，可以衍生出其他的技巧

1. 最后一个小于：找出第一个大于等于，再往前跳一个

```cpp
auto it = st.lower_bound(20);
if (it != st.begin()) {
    it--;
    cout << *it << endl;
}
```

2. 求最后一个不大于（超过）：求出第一个严格大于，再往前跳一个

```cpp
auto it = st.upper_bound(20);
if (it != st.begin()) {
    it--;
    cout << *it << endl;
}
```





**注意：凡是涉及到向前跳一个的，都要判断等不等于 begin**



### multiset 和 set 的区别

首先 multiset 是可重复集合，而 set 是不可重复集合

其次，multiset 如果是删除一个数值，那么会把所有等于这个数的全部删除，如果删除的是一个迭代器，那么只会删除这个位置

---

*巧记 begin 和 end*

**计算机左闭上右开，所以 `--end()` 才有数值**

**所有操作不成立全部返回 end ，唯独向前跳一个是例外**

**特殊记忆小于某个数的最大的和不大于（超过）某个数最大的**

```cpp
#include <bits/stdc++.h>
using namespace std;

multiset<int> st;
int main() {
    int n;
    scanf("%d", &n);
    while(n--) {
        string op;
        int x;
        cin >> op;
        scanf("%d", &x);
        if (op == "Push") st.insert(x);
        else {
            //这里也可以写成multiset<int>::iterator it = st.upper_bound(x);
            auto it = st.upper_bound(x);
            //注意这里是判断等不等于st.begin()
            if (it == st.begin()) printf("No Element!\n");
            else {
                //it在这里再--，在上面的话可能会出错
                it--;
                printf("%d\n", *it);
                st.erase(it);
            }
        }
    }
    return 0;
}
```



## biset

[bitset](https://www.517coding.com/contests/1142/problem/C)

### 暴力

> 所有题目都要首先要写出暴力，然后再去思考优化。

管他能不能过，我们可以先把暴力当作找思路

观察到 $C \le 10000， N \le 1000$ （分别小于等于 $1$ 万和 $1$ 千）

直接开一个数组 $a[10010][1010]$ 

$a_{ij}$ 表示 $i$  是否属于 $j$ 集合

$a_{ij}=1$ 表示 $i$ 属于 $j$ 集合

$a_{ij}=0$ 表示 $i$ 不属于 $j$ 集合

这样的话就要每次 $O(N)$ 查询

但是询问有 $200000$ 个，整体的复杂度为 $O(NQ)$

~~直接下播~~

### 压位

$a$ 数组很明显有很多的浪费，因为它只是用来表示 $0$ 或 $1$ 的

然而一个 int 本身就有 $32$ 个 $0$ 或 $1$

所以可以对 $a$ 数组进行一些改编

开一个数组 $a[10010][40]$

相当于把 $1$ 千压成了 $40$

一个 int 是 $32$ 个位，$40$ 个 int 就可以表示 $1$ 千多个集合了

### bitset 的使用方法

手写压位太麻烦了，各种位运算痛不欲生，有没有更简单的方法呢？

别忘了 STL 就是为了让代码量变少而生的

我们可以使用 STL 中的 bitset

1. 定义： `bitset<1010> b[10010]`（**注意：<> 里面必须是常数**），可以理解为长度位 $1010$ 的二进制字符串

2. `b[x].set(y)` 把 $x$ 的第 $y$  设为 $1$
3. `(b[x] & b[y])` 返回两个 bitset 与之后的新的 bitset
4. `b[x].count()` 返回 $b[x]$ 中 $1$ 的数量
5. `b[x].flip(pos)` 把 $b[x]$ 中的第 $pos$ 位 $0$ 变 $1$，$1$ 变 $0$ 
6. `b[x].reset()` 清空（把所有位设为 $0$ ）
7. `b[x].any()` 如果 $b[x]$ 至少有一位为 $1$，返回 true，否则返回 false

详见 <https://cplusplus.com/>

```cpp
#include <bits/stdc++.h>
using namespace std;


bitset<1010> a[10010];//用值当做下标
int main() {
    int n;
    scanf("%d", &n);
    for(int i = 1; i <= n; i++) {
        int k, x;
        scanf("%d", &k);
        while(k--) {
            scanf("%d", &x);
            a[x].set(i);//x和i在同一集合
        }
    }
    int Q;
    scanf("%d", &Q);
    while(Q--) {
        int x, y;
        scanf("%d%d", &x, &y);
        //如果两个与了之后不为0，就说明这两个有相同的1，即在同一集合
        if((a[x] & a[y]).any()) printf("Yes\n");
        else printf("No\n");
    }
    return 0;
}
```



## 矩阵切割

[矩形切割](https://www.517coding.com/contests/1142/problem/D)

首先，我们发现，切痕可以用 set 维护

然后，我们发现，每切一刀，只改变这一维中某一块的大小，其他的都不变

而最大面积本质上讲就是横向的最大间距乘纵向的最大间距

当切一刀的时候其实只会改变一个间距，其他的间距都不会变，那么就可以把这个间距找出来，删掉，再插入两个新的间距

又因为间距会重复，所以需要两个 multiset 来维护间距 

```cpp
#include <bits/stdc++.h>
using namespace std;

set<long long> s1,s2;
multiset<long long> ss1,ss2;

int main() {
    long long w, h, n;
    scanf("%lld%lld%lld", &w, &h, &n);
    //插入边界
    s1.insert(w), s2.insert(h);
    s1.insert(0), s2.insert(0);
    ss1.insert(w), ss2.insert(h);
    for (int i = 1; i <= n; i++) {
        char c;
        int x;
        scanf(" %c %d", &c, &x);
        if(c == 'H') {
            //找到这个切痕的切面一个切痕和后面一个切痕
            auto it=s2.upper_bound(x),it2=s2.upper_bound(x);
            it2--;//注意这里要--
            long long k = (*it) - (*it2);
            ss2.erase(ss2.find(k));
            ss2.insert(x - (*it2)), ss2.insert((*it)-x);
            s2.insert(x);
        }
        else {
            auto it=s1.upper_bound(x),it2=s1.upper_bound(x);
            it2--;
            long long k=(*it)-(*it2);
            ss1.erase(ss1.find(k));
            ss1.insert(x - (*it2)), ss1.insert((*it)-x);
            s1.insert(x);
        }
        //最值相乘
        printf("%lld\n", *(--ss1.end()) * *(--ss2.end()));
    }
    return 0;
}
```


