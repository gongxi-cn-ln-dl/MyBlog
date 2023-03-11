---
title: 【题解】CF1706B Making Towers
date: 2022-07-19 23:11:00
tags: [题解]
mathjax: true
excerpt: false
---



### 题意简述

- 你有 $n$ 块有颜色的积木。第 $i$ 块积木的颜色为 $c_i(1\leq c_i \leq n)$ 。

- 你可以按照下面的步骤在一个直角坐标系中搭积木（无视重力）：

  - 首先，将第 $1$ 块积木放在 $(0,0)$ 的位置上。
  - 对于第 $i(2\leq i \leq n)$ 块积木，它可以被放置在上一块积木的左侧、右侧或上面（只要该位置没有放过积木）。

- 一个塔是由 $s$ 个竖直摞起来的同色方块构成的，其高度为 $s$。

- 对于 $r \in [1,n]$ ，解决下面的互相独立的问题：

  找到颜色 $r$ 的方块能形成的最高的塔。

### 题目分析

既然要求颜色 $r$ 的方块能搭成的最高的塔，那就要这种颜色的积木尽可能地竖直摞在一起。

由于每种颜色的处理是互相独立的，所以就想到将这些积木按照颜色序号排序并拢后分别处理。

但在摞的过程中积木之间的相对位置编号也很重要，所以最终的排序方案就是：以颜色编号为第一关键字，以积木编号为第二关键字进行排序。

接下来考虑这些积木应该怎么摞能使高度最高。

容易想到，一个积木并不是一定有机会落在某个方块上的。

换言之，能竖直摞在一起的两个方块一定会满足一定的关系。

设上面的方块编号为 $a$，下面的方块的编号为 $b$，如果他们两个能摞在一起，那么一定有 $a=b+1+2k(k\in\mathbb{N})$。

从上面的式子进一步思考，我们能得到一个很关键的结论：奇数编号的积木一定与偶数编号的积木上下相邻。

更进一步，**放置奇数（偶数）编号的积木后，塔的高度变化一定是某个先前放过的偶数（奇数）编号的积木高度加一得到的**。

到了这里，最终算法应该已经出现了：

1. 排序积木。

2. 对每一种颜色的积木，分别统计奇数编号积木高度最大值 $m_1$ 和偶数编号积木高度最大值 $m_2$。

3. 如果该积木的编号为奇数，那么用 $m_2+1$ 尝试更新 $ans$ ，再用 $ans$ 尝试更新 $m_1$；

   如果该积木的编号为偶数，那么用 $m_1+1$ 尝试更新 $ans$ ，再用 $ans$ 尝试更新 $m_2$。

4. 统计结果并输出。



### 参考代码

```cpp
#include <bits/stdc++.h>
using namespace std;

const int maxN = 2e5;

struct Block{
    int col;
    int id;
    Block(){};
    Block(int col,int id):col(col),id(id){};
    friend bool operator < (Block A,Block B){
        if(A.col != B.col) return A.col < B.col;
        return A.id < B.id;
    }
}B[maxN];

int dp[maxN];
int ans[maxN];

int main(){
    int T;
    scanf("%d",&T);
    while(T--){
        int N;
        scanf("%d",&N);
        memset(ans,0,sizeof(ans));
        for(int i = 1;i<=N;i++){
            int c;
            scanf("%d",&c);
            B[i] = Block(c,i);
        }
        sort(B+1,B+1+N);
        B[0] = Block(0,0);
        int oddMax = 0;
        int evenMax = 0;
        for(int i = 1;i<=N;i++){
            if(B[i].col != B[i-1].col){
                oddMax = 0;
                evenMax = 0;
            }
            dp[i] = 1;
            int p = B[i].id;
            if(p & 1){
                dp[i] = max(dp[i],evenMax+1);
                oddMax = max(oddMax,dp[i]);
            } else{
                dp[i] = max(dp[i],oddMax+1);
                evenMax = max(evenMax,dp[i]);
            }
            ans[B[i].col] = max(ans[B[i].col],dp[i]);
        }
        for(int i = 1;i<=N;i++) printf("%d ",ans[i]);
        printf("\n");
    }
    return 0;
}
```
