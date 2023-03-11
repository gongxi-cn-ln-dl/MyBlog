---
title: 最长上升子序列（LIS）
date: 2022-02-10 22:52:00
tags: [动态规划, 算法]
mathjax: true
excerpt: false
---



- > 给定长度为$N$的序列$a_i$，求序列中一个长度最长且递增的子序列。输出子序列的长度。$N\leq 1000$。

### $O(N^2)$解法

- 设$f[i]$为以$a_i$结尾的最长上升子序列的长度

  易知$f[i]$的最大值为 区间$[1,i)$中的 小于$a_i$的最大的$j$加上1

  意思就是，

  假设$a_j$为区间$[1,i)$中小于$a_i$的最大值，那么$a_i$就可以拼在$a_j$的后面，这时以$a_i$结尾的LIS的长度就为以$a_j$结尾的LIS长度+1

  用状态转移方程表示就是

  $\large{f[i]=\displaystyle\max_{j=1}^{i-1}(f[j])+1,a_i>a_j}$

- 用代码表示就是

  ```cpp
  int A[N+1];
  int f[N+1];
  for(int i = 1;i<=N;i++){
      for(int j = 1;j<i;j++){
          if(A[i] <= A[j]) continue;
          f[i] = max(f[i],f[j]+1);
      }
  }
  int max_ = -1;
  for(int i = 1;i<=N;i++) max_ = max(f[i],max_);
  ```

### $O(N\log N)$解法

- 设$f[i]$为长度为$i$的上升子序列的最后一个数字

  设$a_j$为序列的第$j$个元素

  若$f_{i}\leq a_j < f_{i+1}$，则将$f_i$的值更新为$a_j$

  相当于将长度为$i$的上升子序列的最后一位$f_i$改为了更小的$a_j$，显然这样更有利于在后面接其他元素

- 但是目前遍历长为$n$的序列，对于其中每项$a_j$，都需要进行至多$i$次操作去寻找合适的区间，时间复杂度仍然为$O(N^2)$

- 但是，由于$f_i$单调递增，可以在查找的时候使用二分查找来优化，这样就至多进行$\log N$次查找，时间复杂度降为$O(N\log N)$

- 代码实现

  ```cpp
  int A[N+1];
  int f[N+1];
  int cnt = -1;
  //cnt代表当前最长上升子序列的长度
  fill(f+1,f+N+1,1e9);
  //fill(st,ed,val)意为用val填充数组[st,ed)区间
  //使用极大值填充区间
  for(int i = 1;i<=N;i++){
      int j = lower_bound(f+1,f+N+1,A[i]) - f;
      //lower_bound(st,ed,val)意为在[st,ed)区间内查找第一个不小于val的元素
      //lower_bound返回一个指向找到元素的迭代器
      //减去的f是地址
      //地址 - 地址 = 下标
      cnt = max(cnt,j);
      f[j] = A[i];
  }
  int ans = cnt;
  ```
