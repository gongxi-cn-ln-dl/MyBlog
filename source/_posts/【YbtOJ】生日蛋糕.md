---
title: 【YbtOJ】生日蛋糕
date: 2022-02-21 10:30:00
tags: [题解]
mathjax: true
excerpt: false
---



~~好家伙这在洛谷是道蓝题~~

### 题面

> **题目描述**
>
> 7 月 17 日是 Mr.W 的生日，ACM-THU 为此要制作一个体积为 $N\times\pi$ 的 $M$ 层生日蛋糕，每层都是一个圆柱体。
>
> 设从下往上数第 $i(1 \leq i \leq M)$层蛋糕是半径为 $R_i$，高度为 $H_i$的圆柱。当 $i \lt M$时，要求 $R_i \gt R_{i+1}$ 且 $H_i \gt H_{i+1}$。
>
> 由于要在蛋糕上抹奶油，为尽可能节约经费，我们希望蛋糕外表面（最下一层的下底面除外）的面积 $Q$ 最小。
>
> 请编程对给出的 $N$ 和 $M$，找出蛋糕的制作方案（适当的 $R_i$和 $H_i$ 的值），使 $S=\dfrac{Q}{\pi}$最小。
>
> （除 $Q$ 外，以上所有数据皆为正整数）
>
> **输入格式**
>
> 第一行为一个整数 $N(N \leq 2 \times 10^4)$，表示待制作的蛋糕的体积为 $N\times \pi$。
>
> 第二行为 $M(M \leq 15)$，表示蛋糕的层数为 $M$ 。
>
> **输出格式**
>
> 输出一个整数 $S$，若无解，输出 $0$。
>
> **样例输入**
>
> ```plain
> 100
> 2
> ```
>
> **样例输出**
>
> ```plain
> 68
> ```

### 分析

- 蛋糕的表面积为各层侧面积之和加上底层的上表面面积

- 基本的思路其实很简单

  从下向上搜索每一层的高度 $h$ 与半径 $r$ 并记录目前的总表面积 $s$ 、还需要的体积$v$以及当前层数

  1. 如果已经到达了最顶层，检查 $v$ 是否为 $0$ ，即体积是否等于 $m$
     1. 若体积恰巧等于 $m$ 则找到一种解，比较 $s$ 与目前最小表面积 $minS$ 
     2. 若体积不相等，则这种方法不符合规则应舍弃
  2. 枚举 $h$ 和 $r$ ，判断侧面积 $s'$ 、 目前体积 $v'$ 是否合法，不合法就跳过

- 所有式子里面都有 $\pi$ 可以将其提出来，在运算过程中忽略掉

### 剪枝

- 先给出我最开始写出来的、显而易见的剪枝

  1. 若目前的总表面积已经超过了 $minS$ ，很显然最后的结果一定会超过目前的最小值
  2. 如果加上该层蛋糕的体积后总体积超过所求体积，最后答案一定非法

- 然后是书中给出的剪枝方法（

  记当前表面积为 $S$ ，当前体积 $V$ ，当前处于 $dep$ 层（最上层为第 $1$ 层）

  1. **上下界剪枝**

     根据 $\pi R^2H=\pi(N-V)$ 可得

     1. $R = \sqrt{\frac{N-V}{H}}$ 

        由于 $H$ 最小为 $1$ ，则 $R$ 的最大值为 $\sqrt{N-V}$

        但考虑到 $R_{dep}$ 不能超过上一层的 $R_{dep+1}$ 

        因此 $R_{dep}$ 的最大值为 $\min(R_{dep+1},\sqrt{N-V})$ 

        由于每一层的 $R_{dep}$ 都需要小于上一层的 $R_{dep+1}$ ，可知 $R_{dep}$ 的最小值就是 $dep$

        因此，$R$ 的取值范围是 $[dep,\min(R_{dep+1},\sqrt{N-V})]$

     2. $H=\frac{N-V}{R^2}$，由于是先枚举 $R$ 再枚举 $H$ ，因此这个 $R^2$ 可以留下来

        其余部分同 $R$ 

        因此 $H$ 的取值范围为 $[dep,\min(R_{dep+1},[\frac{N-V}{R^2}]]$

  2. 优化搜索顺序

     在上面确定的范围中，采用倒序枚举

     因为该层的 $R$ 和 $H$ 越大，留给接下来的蛋糕的 $R$ 和 $H$ 的范围就越大

  3. 可行性剪枝

     可以预处理出从上往下前 $i$ 层的最小体积和侧面积，显然，当第 $1$ ~ $i$ 层的半径分别取 $1,2,3,\cdots,i$ ，高度也分别取 $1,2,3,\cdots,i$ 时，有最小体积和侧面积

     如果当前体积 $V$ 加上 $1\sim dep-1$ 层的最小体积大于 $N$ ，可以剪枝

  4. 最优性剪枝一

     如果当前表面积 $S$ 加上 $1\sim dep-1$ 层的最小侧面积大于已经搜到的答案，剪枝

  5. 最优性剪枝二

     （下列描述忽略 $\pi$）

     利用 $h$ 与 $r$ 数组， $1\sim dep-1$ 层的体积可表示为 $N-V=\sum^{dep-1}_{k=1}(h_k\times r_k^2)$

     $1\sim dep-1$ 层的侧面积可表示为 $2\sum^{dep-1}_{k=1}(h_k\times r_k)$

     因为 
     $$
     \begin{align}
     2\sum^{dep-1}_{k=1}(h_k\times r_k)&=\dfrac{2}{r_{dep}}\times\sum^{dep-1}_{k=1}(h_k\times r_k \times r_{dep})\\
     &\geq\dfrac{2}{r_{dep}}\times \sum^{dep-1}_{k=1}(h_k\times r_k^2)\\
     &\geq\dfrac{2(N-V)}{r_{dep}}\\
     
     \end{align}
     $$
     所以当 $\dfrac{2(N-V)}{r_{dep}}+S > minS$ （即目前的 $S$ 加上上面的所有层侧面积最小值仍然大于已经搜到的答案）时，可以剪枝

### 代码

```cpp
#include <bits/stdc++.h>
using namespace std;

int n, m;
int minS = 1e9;

inline int CS(int r, int h) { return 2 * r * h; }
inline int CV(int r, int h) { return r * r * h; }
void dfs(int N, int R, int H, int cnt, int sc) {
    if (sc > minS)
        return;
    if (cnt == m + 1) {
        if (N != 0)
            return;
        minS = min(minS, sc);
        return;
    }
    int k = m - cnt + 1;
    if (k * R * R * H < N)
        return;
    if (k * 2 + sc > minS)
        return;
    if (cnt == 1) {
        for (int r = R; r >= m; r--) {
            for (int h = m; h <= H; h++) {
                int cs = CS(r, h);
                int cv = CV(r, h);
                if (N - cv < 0)
                    continue;
                if (r * r * h > n)
                    continue;
                dfs(N - cv, r, h, cnt + 1, sc + cs + r * r);
            }
        }
    } else
        for (int r = R - 1; r >= m - cnt + 1; r--) {
            for (int h = m - cnt + 1; h < H; h++) {
                int cs = CS(r, h);
                int cv = CV(r, h);
                if (N - cv < 0)
                    continue;
                if (r * r * h > n)
                    continue;
                dfs(N - cv, r, h, cnt + 1, sc + cs);
            }
        }
}
int main() {
    scanf("%d%d", &n, &m);
    dfs(n, 50, 50, 1, 0);
    printf("%d", minS);
    return 0;
}

```
