---
title: 【题解】CF1698C 3SUM Closure
date: 2022-07-02 12:37:00
tags: [题解]
mathjax: true
excerpt: false
---



[链接](https://codeforces.com/contest/1698/problem/C)

### 题意简述

有一个长度为 $n$ 的序列 $a$ 。如果对于 $\forall i , j , k\in[1,n] $ ，$\exist a_i+a_j+a_k \in a$ ，那么称这个序列为 "3SUM-closed"

给出序列 $a$ ，问这个序列是否为 "3SUM-closed"

### 题目分析

我是通过不断寻找反例来逐渐缩小判断的条件范围的。

首先考虑 $n$ 最少的情况 ：$n=4$ 时：

1. 我们令前三个数分别为 $1,2,3$ ，那么显而易见的是，为了达到 "3SUM-closed" ，不得不向序列不断添加当前序列的任意三项和，这个序列是无尽的（全负同理），所以可以知道， **序列元素全正或全负，序列不是 3SUM-closed ** 。
2. 那么如果序列减少一些正元素的数量呢？可以想到，当序列中存在三个及以上的正数（负数同理）时，就会因为它们可以不断累加而使序列无尽。所以，**序列最多同时存在 2 个正数（负数）**。
3. 那么如果在三项中有一个负数来制约序列，使序列被“拉住”而不会无尽拓展呢？可以尝试令前三个数分别为 $1,2,-1$ ，似乎这种情况是可行的，但是如果我们换一种情况，$1,2,-4$ ，那么序列就会因为正数和小于负数而使序列向负方向无限拓展。
4. 再回头去看 $1,2,-1$ 这种情况为什么可行：**当存在正数与负数可以互相抵消时，序列可以是 3SUM-closed**。
5. 那么只要正数与负数可以互相抵消，这个序列就是 3SUM-closed 吗？显然不是。比如 $1,1,2,-1,-1$ 就不行，因为 $1,1,2$ 违反了第 2 条规律。

在以上五条规律的基础上，我们稍微整合一下可以得到如下结论：

**序列中最多存在两个正数，两个负数和若干个0**。

而这若干个 0 ，实际上对序列的影响可以等效为 2 个 0。

所以我们实际上可以通过以上结论先过滤一遍，过滤后得到的序列可以直接暴力验证（因为最多可以等效为一个 $n=6$ 的序列）。

### 参考代码

```cpp
#include <bits/stdc++.h>
using namespace std;

int N;
bool flg;

int hasZero;

int zNum;
int fNum;

int A[20];

map <int, bool> M;

int main() {
	int T;
	scanf("%d", &T);
	while (T--) {
		flg = true;
		zNum = fNum = 0;
		int cnt = 0;
		hasZero = false;
		M.clear();
		memset(A, 0, sizeof(A));

		scanf("%d", &N);
		for (int i = 1; i <= N; i++) {
			int t;
			scanf("%d", &t);
			if (!flg) continue;
			if (t > 0) {
				++zNum;
				A[++cnt] = t;
			} else if (t < 0) {
				++fNum;
				A[++cnt] = t;
			} else hasZero++;
			if (zNum > 2 || fNum > 2) flg = false;
		}

		if (!flg) {
			printf("No\n");
			continue;
		}

		cnt += min(hasZero, 2);
		for (int i = 1; i <= cnt; i++) {
			M[A[i]] = true;
		}
		for (int i = 1; i <= cnt; i++) {
			for (int j = i + 1; j <= cnt; j++) {
				for (int r = j + 1; r <= cnt; r++) {
					if (!M.count(A[i] + A[j] + A[r])) {
						flg = false;
						goto QwQ;
					}
				}
			}
		}
        QwQ:;
		printf("%s\n", flg ? "Yes" : "No");
	}
	return 0;
}
```



 