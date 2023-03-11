---
title: 【YbtOJ】最大均值
date: 2022-02-18 12:32:00
tags: [题解]
mathjax: true
excerpt: false
---



### 题目描述

- 给定正整数序列 $A$，求一个平均数最大的，长度不小于 $L$ 的（连续的）子段。

### 分析

- 问题可以转化为 “**是否存在一个长度不小于 $L$ 的子段，使平均值不小于 $\text{mid}$**”

  这个问题的答案显然有两种：存在与不存在

  1. 若不存在，说明 $\text{mid}$ 选得过大应该缩小
  2. 若存在，说明 $\text{mid}$ 选得有些小应该扩大

  当 $\text{mid}$ 的范围已经被缩小为一个数的时候，这个 $\text{mid}$ 就处于该问题的两种答案的分界线上

  即最后的这个 $\text{mid}$ 

  - 比它小的所有值，都**能够**使要求的子段存在
  - 比它大的所有值，都**不能**使要求的子段存在

  因此，这个 $\text{mid}$ 就是所求的最大的平均值

- 将上个转化来的问题继续转换

  既然上个问题中是使平均值不小于 $\text{mid}$ ，那么如果将 $A$ 序列的所有值都减去 $\text{mid}$ ，那么问题又变成了

  **是否存在一个长度不小于 $L$ 的子段，子段和非负**

  子段和可以通过前缀和相减得到，即设 $\text{sum}_i$ 为 $[A_1,A_i]$ 的和，则有

  $$
  \displaystyle\max_{i-j\geq L}\{A_{j+1}+\cdots+A_i\}=\max_{L\leq i\leq n}\{\text{sum}_i-\min_{0\leq j \leq i-L}\{\text{sum}_j\}\}
  $$

  $[j,i]$ 即为一个长度不小于 $L$ 的子段

  由于 $j$ 的取值范围 $[0,i-L]$ 随着 $i$ 的增长每次只扩大 $1$ （即每次只有一个新值进入范围），那么其实不需要枚举 $j$ ，直接比较上一部分的最小值与新值即可

### 代码

```cpp
#include<bits/stdc++.h>
using namespace std;

const int maxN = (1e5+5);
const double eps = 1e-5;

double A[maxN];
double sum[maxN];
int n,L;
double B[maxN];

bool check(double mid){
	for(int i = 1;i<=n;i++) B[i] = A[i] - mid;
	for(int i = 1;i<=n;i++) sum[i] = sum[i-1] + B[i];
	int ans = -1e9;
	int min_ = 1e9;
	for(int i = L;i<=n;i++){
		min_ = min(min_,sum[i-L]);
		ans = max(ans,sum[i]-min_); 
	}
	return ans >= 0;
}

int main(){
	scanf("%d%d",&n,&L);
	for(int i = 1;i<=n;i++){
		scanf("%lf",&A[i]);
	}
	double l = -1e6;
	double r = 1e6;
	while(l + eps < r){
		double mid = (l+r)/2;
		if(check(mid)) l = mid;
		else r = mid;
	}
	printf("%d",int(r*1000));
	return 0;
}
```



  

  

  

  