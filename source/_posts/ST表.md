---
title: ST表
date: 2022-02-15 14:18:00
tags: [算法]
mathjax: true
excerpt: false
---



- 用于解决**可重复贡献问题**

  > 可重复贡献问题，是指对于运算 $\operatorname{opt}$ ，满足 $x \operatorname{opt} x=x$，则对应的区间询问就是一个可重复贡献问题。例如，最大值有 $\max(x,x)=x$， $\gcd$ 有 $\gcd(x,x)=x$，所以 $\operatorname{RMQ}$ 和区间 $\gcd$ 就是一个可重复贡献问题。像区间和就不具有这个性质，如果求区间和时采用的预处理区间重叠了，则会导致重复部分计算两次，这是我们所不愿意看到的。另外，$\operatorname{opt}$ 还必须满足结合律才能使用ST表求解。
  >
  > > RMQ，RMQ 是英文 Range Maximum/Minimum Query 的缩写，表示区间最大（最小）值。

- 我们能使用至多两个预处理过的区间来覆盖询问区间，也就是说询问时的时间复杂度可以被降至 $O(1)$

### 预处理

- 使用 $f(i,j)$ 代表 $i$ 到 $i+2^j-1$ 的最值，显然 $f(i,0)=a_i$
- $f(i,j)=\operatorname{opt}(f(i,j-1),f(i+2^{j-1},j-1))$
- 以上就是预处理部分，复杂度为 $O(n \log n)$

### 查询

- 对于每个询问，可以把它分成两部分：$f(l,l+2^s-1)$ 与 $f(r-2^s+1,r)$ ，其中 $s=\lfloor \log_2(r-l+1)\rfloor$

- 求 $\log$ 可以建一个 $\log_2x$ 的表，这样就不需要STL的log函数了

  $\log_2x+1=\log_2x+\log_22=\log_2(2x)$

- 两部分的结果的 $\operatorname{opt}$ 就是最终结果

### 模板

```cpp
int Log[maxN];
int f[maxN][22];

//这里多定义一个opt函数是为了便于修改去解决其他的可重复贡献问题
inline int opt(int x,int y){
    return max(x,y);
}

void pre(){
    //预处理log值
    Log[1] = 0; Log[2] = 1;
    for(int i = 3;i<maxN;i++){
        Log[i] = Log[i/2]+1;
    }
    //预处理f值
    for(int j = 1;j<=21;j++){
        for(int i = 1;i + (1 << j) - 1 <= n;i++){
            f[i][j] = opt(f[i][j-1],f[i+(1<<j-1)][j-1]);
        }
    }
}

int ask(int l,int r){
    int s = Log[r - l + 1];
    return opt(f[x][s],f[y - (1 << s) + 1][s]);
}
```
