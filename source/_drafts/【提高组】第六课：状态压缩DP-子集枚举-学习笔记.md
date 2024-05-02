---
abbrlink: ''
categories:
- 学习笔记
date: '2024-02-07T16:17:46+08:00'
tags:
- 提高组
- 算法
- 状压DP
- 子集枚举
title: 【提高组】第六课：状态压缩DP/子集枚举 学习笔记
updated: '2024-05-02T09:31:47.674+08:00'
---
# 第六课：状态压缩DP/子集枚举

[第六课：状态压缩DP/子集枚举](https://www.517coding.com/contests/1145)

## 前置知识

已知一个状态压缩的数 $i$，有如下操作：

求 $i$ 的第 $j$ 位是否为 $1$ ：`i&(1<<j)!=0` （**注意：是 !=0，而不是==1**）

把 $i$ 的第 $j$ 位修改为 $1$：`i|=1<<j`

## A：环游世界

[环游世界](https://www.517coding.com/contests/1145/problem/A)

首先，这很明显是一个无向图

这样，我们需要的信息就是：

1. 已走过的点集
2. 当前停留在哪里
3. 当前的最小花费

知道了这三个信息之后，**转移时只要枚举一个未走过的点，从当前的位置转移过去，再把花费加上即可**

在做最短路是通常不会主动去构造一个环，而本题要求从起点出发，再回到起点

我们可以先找到一个点 $T$ ，求出起点到 $T$ 的最短路，最后再单独加上 $T$到起点的距离

表示已走过的集合就可以用状态压缩

```cpp
#include <cstdio>
#include <cstring>
#define min(a, b) (a<b?a:b)

//mp[i][j]表示i到j的距离
int mp[12][12];

//dp[i][j]表示经过了i状态的点集，当前停留在j的最小代价
int dp[1 << 12][12];

int n;
void checkmin(int &x, int y) {x == -1 || y < x ? x = y: x = x;}
void input() {
  scanf("%d", &n);
  n++;
  for (int i = 0; i < n; i++)
    for (int j = 0; j < n; j++)
      scanf("%d", &mp[i][j]);
}
void floyd() {
  for (int k = 0; k < n; k++)
    for (int i = 0; i < n; i++)
      for (int j = 0; j < n; j++)
        mp[i][j] = min(mp[i][j], mp[i][k] + mp[k][j]);
}
void change() {
  memset(dp, 0x3f, sizeof(dp));
  dp[1][0] = 0;
  //枚举已有的状态去转移给未知的状态
  for (int mask = 1; mask < (1 << n); mask++) {
    //枚举了一个已经求好的状态dp[mask][end]
    for (int end = 0; end < n; end++) if (dp[mask][end] != 0x3f3f3f3f) {
      //枚举一个未走过的点
      for (int nxt = 0; nxt < n; nxt++) if (!(mask & (1 << nxt))) {
        checkmin(dp[mask | (1 << nxt)][nxt], dp[mask][end] + mp[end][nxt]);
      }
    }
  }
}
void print() {
  int ans = 0x3f3f3f3f;
  for (int end = 0; end < n; end++) {
    checkmin(ans, dp[(1 << n) - 1][end] + mp[end][0]);
  }
  printf("%d\n", ans);
}
int main() {
  input();//输入
  floyd();//floyd求最短路
  change();//状态转移
  print();//计算答案
  return 0;
}
```

## B：数字重排

[数字重排](https://www.517coding.com/contests/1145/problem/B)

观察到 $n$ 最大只有 $16$

那么就可以使用状态压缩

共有两个信息：数字、位置

### 暴力

$dp_{i_j}$ 中 $i$ 表示放了那哪些位置，$j$ 表示放了哪些数字

接着枚举一个空的位置，进行转移

但是这样的话就要开大小为 $2^{16}\times2^{16}$ 个数组，也就是 $2^{32}$ 个

### 状态压缩

$dp_{ij}$ 中 $i$ 表示放了数字的集合，$j$ 表示以谁结尾

这时候位置就不重要了，因为当我们可以直接从左往右依次放进去，只需要考虑结尾即可，不用管位置

```cpp
#include <cstdio>
#include <cstring>

const int N = 18;
const int INF = (int) 1e9;

int a[N], p[N], b[N];
int dp[1 << N][N];
void checkmax(int &x, int y) {if (y > x) x = y;}
int main() {
  int n; scanf("%d", &n);
  memset(b, -1, sizeof(b));
  for (int i = 0; i < n; i++) {scanf("%d%d", &a[i], &p[i]); if (p[i] != -1) b[p[i]] = 1;}
  for (int i = 0; i < (1 << n); i++) for (int j = 0; j < n; j++) dp[i][j] = -INF;
  //设置初始状态，放第一个数
  if (b[0]) for (int i = 0; i < n; i++) if (p[i] == 0) dp[1 << i][i] = 0;
  else for (int i = 0; i < n; i++) if (p[i] == -1) dp[1 << i][i] = 0;
  for (int mask = 0; mask < (1 << n); mask++) {
    int pos = 0;//记录mask状态有几个1
    for (int i = 0; i < n; i++) if (mask & (1 << i)) pos++;
    for (int end = 0; end < n; end++) if (dp[mask][end] != -INF)
      //下一个数放a[nxt]
      for (int nxt = 0; nxt < n; nxt++) if (!(mask & (1 << nxt)))
        //下面的逻辑：如果a[nxt]还没有放过，而且它可以放在pos位置
        //是否可以放在pos位置
        //1. a[nxt]呗钦点在pos位置
        //2. a[nxt]可以随便放
        if (p[nxt] == -1 || p[nxt] == pos) checkmax(dp[mask | (1 << nxt)][nxt], dp[mask][end] + a[end] * a[nxt]);
  }
  int ans = -INF;
  for (int end = 0; end < n; end++) checkmax(ans, dp[(1 << n) - 1][end]);
  printf("%d\n", ans);
  return 0;
}
```

## E：回文子序列删除

[回文子序列删除](https://www.517coding.com/contests/1145/problem/E)

$1$ 个状态可以由多个状态转移过来，那么可以可以转移过来的状态构成了一个子集

$dp_i$ 表示 $i$ 状态的字符集合最少需要删几次

枚举这个状态的回文子集，然后扔掉

假设枚举的回文子集为 $s$ ，那么 $dp_i$ 就可以由 $dp_{i-s}+1$ 在转移过来

```cpp
#include <cstdio>
#include <algorithm>
#include <vector>
#include <cstring>

const int N = 18;
const int INF = (int) 1e9;

char s[N];
int dp[1 << N], flag[1 << N], n;

std::vector<int> v;
//判断mask状态的字符是否是回文的
bool judge(int mask) {
  v.clear();
	for (int i = 0; i < n; i++) if (mask & (1 << i)) v.push_back(i);
	int l = 0, r = (int) v.size() - 1;
	while (l <= r) {
		if (s[v[l]] != s[v[r]]) return false;
		l++, r--;
	}
	return true;
}
int main() {
	scanf("%s", s);
	n = strlen(s);
	for (int i = 1; i < (1 << n); i++) {
		if(judge(i)) dp[i] = 1, flag[i] = 1;
		else dp[i] = INF;
	}
	for (int mask = 0; mask < (1 << n); mask++)
    //枚举子集
    for (int sub = mask; sub > 0; sub = mask & (sub - 1)) if (flag[sub]) dp[mask] = std::min(dp[mask], dp[mask - sub] + 1);
  printf("%d\n", dp[(1 << n) - 1]);
  return 0;
}
```
