---
title: 【题解】P2303 [SDOI2012] Longge 的问题
date: 2022-11-23 10:46:00
tags: [数学, 题解]
mathjax: true
excerpt: false
---

## 【题解】P2303 [SDOI2012] Longge 的问题

[题目链接](https://www.luogu.com.cn/problem/P2303)

> 求 $\displaystyle\sum_{i=1}^n \gcd(i,n)$ 

将这个柿子展开~~变复杂~~，得到 
$$
\displaystyle\sum_{i=1}^{n}\sum_{d \mid n,d \mid i}d[\gcd(i,n)=d]
$$
因为仅有 $d\mid n$ 且 $d \mid i$ 的时候可能会累加 $d$ ，则有效的 $i$ 一定是 $d$ 的倍数，即 $i = kd$

那么原式就可以化为
$$
\displaystyle\sum_{d \mid n}\sum_{k=1}^{kd\leq n} d [\gcd(kd,n)=d]
$$
在这个柿子中，$d$ 是常量，将其提出来，并将 $kd\leq n$ 移项得 $k \leq \dfrac{n}{d}$ 得到
$$
\displaystyle\sum_{d\mid n}d\sum_k^{\frac{n}{d}}[\gcd(kd,n)=d]
$$
艾弗森括号里面的也能同时除个 $d$ 得到 $\displaystyle\sum_{d\mid n}d\sum_k^{\frac{n}{d}}[\gcd(k,\dfrac{n}{d})=1]$

由于 $\displaystyle\varphi(n)=\sum_{i=1}^{n}[\gcd(i,n)]$

所以原式就化成了 $\displaystyle\sum_{d\mid n}d\times \varphi(\dfrac{n}{d})$



```cpp
#include<bits/stdc++.h>
using namespace std;

typedef long long ll;

ll getPhi(ll n){
    ll res = n;
    for(ll i = 2;i*i<=n;i++){
        if(n % i == 0) res = res / i * (i - 1);
        while(n % i == 0) n /= i;
    }
    if(n > 1) res = res / n * (n - 1);
    return res;
}

ll N;

int main(){
    scanf("%lld",&N);
    ll res = 0;
    ll i = 1;
    for(;i*i<N;i++){
        if(N % i == 0) res += i * getPhi(N / i) + (N / i) * getPhi(i);
    }
    if(i * i == N) res += i * getPhi(i);
    printf("%ll\n",res);
    return 0;
}
```
