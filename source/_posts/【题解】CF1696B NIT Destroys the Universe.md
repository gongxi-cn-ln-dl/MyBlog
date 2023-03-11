---
title: 【题解】CF1696B NIT Destroys the Universe
date: 2022-06-26 10:14:39
tags: [题解]
mathjax: true
excerpt: false
---

### 分析

记每一次操作后的序列为 $b$ .

序列 $b$ 有如下两种情况：

1.  $\forall i \in[l,r],a_i\not=0$ 时，序列 $b$ 在区间 $[l,r]$ 内每一项均为 $0$ .
2.  $\exist i\in[l,r],a_i=0$ 时，序列 $b$ 在区间 $[l,r]$ 中每一项的值均为 $\operatorname{mex}(\{a_l,a_{l+1},\dots,a_{r-1},a_r\})$ .

对于每一个区间 $[l,r]$ ，最多仅需要两次操作（情况 2 然后情况 1）即可使 $\forall i \in[l,r],a_i=0 $ .

一个显然的性质为：对于每个序列，最多只需要 $2$ 次操作即可使整个序列都为 $0$ .

因此，用 $0$ 将原序列分割成若干段，记段数为 $k$ ：

1. 当 $k=0$ 时，意味着原数组所有元素均为 $0$ .
2. 当 $k=1$ 时，意味着只存在一个区间 $[l,r]$ 满足情况 1.
3. 当 $k \geq 2$ 时，意味着存在多于 2 个满足情况 1 的区间，如果对于每一段都进行操作 1 ，共需要 $k(k>1)$ 次操作。但显然我们可以对整个序列进行两次操作（情况 2 然后情况 1）即可使整个序列均为 $0$ .

### 参考代码

```cpp
#include <bits/stdc++.h>
using namespace std;

const int maxN = 3e5;

int N;
int A[maxN];

int main() {
	int T;
	scanf("%d",&T);
	
	while(T--) {
		scanf("%d",&N);
		
		memset(A,0,sizeof(A));
		int cnt = 0;
		
		for(int i = 1; i<=N; i++) scanf("%d",&A[i]);
		
		for(int i = 1; i<=N+1; i++) {
			if(A[i] == 0 && A[i-1] != 0) cnt++; //统计段数 
		}
		
		if(cnt <= 1) printf("%d\n",cnt);
		else printf("2\n");
	}
	return 0;
}

```

