---
title: 【题解】CF1714G Path Prefixes
date: 2022-08-02 21:52:45
tags: [题解]
mathjax: true
excerpt: false
---

### 题意简述

给出一棵有 $n$ 个节点的有根树，顶点编号为从 $1$ 到 $n$。根节点编号为 $1$。

每一条边上有两个正整数，分别为 $a_j$ 和 $b_j$。

对于每一条从节点 $1$ 到节点 $i$ 的路径，令 $A_i$ 表示这条路径上所有 $a_j$ 的和。

$r_i$ 的值为这条路径的最大前缀长度，并满足该前缀路径的 $b_j$ 和不超过 $A_i$。

最终输出 $r_2,r_3,\dots,r_n$。

### 题目分析

显然这是一道图论题。

通过一遍 DFS ，我们可以获取到所有 $A_i$ 的值，然后就可以考虑如何获取 $r_i$ 的值。

根据 $r_i$ 的定义，我们可以通过一个数组 $B$ 来维护到达该点的的 $b_j$ 的前缀和。

经过某条边后，将该边的 $b_j$ 添加到 $B$ 中；从某条边返回时，将 $b_j$ 从数组中删除。

以题里的这颗有根树为例，到达节点 $3$ 时，$B$ 中有三个元素：$6,16,17$。

由于 $1\leq a_j,b_j \leq 10^9$， $b_j$ 为正整数，那么 $B$ 中的值一定是单调递增的。

下一步就是在数组中找到最大的不超过 $A_i$ 的  $B_j$，则 $r_i$ 的值就为 $j$。

由于 $B$ 中所有元素单调递增，可以用二分查找来寻找最后一个不大于 $A_i$ 的 $B_j$。

而查找答案也可以在计算 $A_i$ 的同时计算，因为到达点 $i$ 时一定已经经过了其所有的祖先节点，$B$ 数组一定有到达该点所有的前缀 $b_j$ 值。

有两点需要注意：

1. 存图用 `vector` 而不是链前（我反正链前被卡了，可能是因为每组测试用例前归零耗时太大了罢。
2. 需要 `long long` ，因为 $1\leq a_j,b_j \leq 10^9$ 且 $2\leq n\leq 2\cdot10^5$ ，前缀和很有可能爆 int。

### 参考代码

```cpp
#include <bits/stdc++.h>
using namespace std;

const int maxN = 2e5 + 10;
typedef long long ll;

vector<int> TO[maxN];
vector<int> A[maxN];
vector<int> B[maxN];

inline void add_edge(int u, int v, int a, int b) {
    TO[u].push_back(v);
    A[u].push_back(a);
    B[u].push_back(b);
}

int N;
int cnt;
ll pre[maxN];
int ans[maxN];

inline void read(int &x) {
    x=0;int f=1;char c=getchar();
    while(!isdigit(c)){if(c=='-')f=-1;c=getchar();}
    while(isdigit(c)){x=(x<<3)+(x<<1)+(c^48);c=getchar();}
    x*=f;
}

//prefixS 表示到达该点经过所有a的和
//prefixSb 表示到达该点经过所有b的和
void dfs(int k, int fa, ll prefixS, ll prefixSb) {
    
    pre[++cnt] = prefixSb;

    //使用 upper_bound 来找到第一个大于等于Ai的值的下标
    //由于pre数组的数据下标从1开始，所以实际的前缀长度需要 -1
    int p = upper_bound(pre + 1, pre + 1 + cnt, prefixS) - pre - 1;
    //由于需要的是第一个不大于Ai的值，找到的是第一个大于Ai的值，所以需要再多减 1
    ans[k] = p - 1;

    for (int i = 0; i < TO[k].size(); i++) {
        int v = TO[k][i];
        if (v == fa) continue;
        dfs(v, k, prefixS + A[k][i], prefixSb + B[k][i]);
    }
    //从前缀数组中将该边的影响移除
    cnt--;
}

int main() {
    int T;
    scanf("%d", &T);
    while (T--) {
        /*
        初始化，这里的N为上一个测试用例中N的值
        因为一共N个节点，所以上一次始点的最大值为N
        这里的始点表示 edge[u][v] 中的 u
        */
        for (int i = 0; i <= N; i++) {
            A[i].clear();
            B[i].clear();
            TO[i].clear();
        }
        read(N);
        cnt = 0;

        for (int i = 2; i <= N; i++) {
            int p, a, b;
            read(p);read(a);read(b);
            add_edge(i, p, a, b);
            add_edge(p, i, a, b);
        }

        dfs(1, -1, 0, 0);

        for (int i = 2; i <= N; i++) {
            printf("%d ", ans[i]);
        }
        putchar('\n');
    }
    return 0;
}
```

