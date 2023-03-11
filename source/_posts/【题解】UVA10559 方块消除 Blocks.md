---
title: 【题解】UVA10559 方块消除 Blocks
date: 2022-04-22 12:29:00
tags: [动态规划, 题解]
mathjax: true
excerpt: false
---



### 设计状态

- $f(i,j)$ 表示合并 $i$ 区间至 $j$ 区间可得的最大分数

  但如果合并一段之后，前后两段接在了一起，那么接在一起的这段能产生的分数一定多于两段分别消除所得分数（因为 $(a+b)^2\geq a^2+b^2$ ）

  那么可以考虑向当前区间后面再接 $k$ 个相同颜色的块一起消除，那么所产生的总分数是：接在一起后的区间消除所得分数+ 消除中间的杂色块所得分数

- 那么状态就变成了 $f(i,j,k)$ ，表示合并 $i$ 区间至 $j$ 区间，并在 $j$ 区间后补上与 $j$ 颜色相同的 $k$ 个能获得的最大分数

### 状态转移方程

为了方便，

令 $\text{len}_i$ 表示第 $i$ 段颜色相同且连续的区间的长度

对于一个区间 $[l,r]$，右边有 $k$ 个与 $j$ 同色的方块，我们可以

1. 将 $r$ 和后面的 $k$ 个方块一起消掉， $f(l,r,k)=f(l,r-1,0)+(\text{len}_r+k)^2$ 
2. 在 $[l,r-1]$ 中寻找一个与 $r$ 颜色相同的块 $p$ ，消除 $p$ 和 $r$ 之间的所有方块后 $p$ 和 $r$ 相邻可以一起消除，$f(l,r,k)=f(p+1,r-1,0)+f(l,p,k+1)$

### 实现细节

~~这里在同一个在代码块中的代码可能不在同一个缩进层级下~~

- 因为连续的同色方块一定是一起消除的，所以可以将一段连续的同色方块看成一个方块

  ```cpp
  struct Block{
      int len,col;
  }block[maxN];
  
  int cnt = 0;
  block[0].col = -1;
  
  for(int i = 1;i<=N;i++){
      int col;
      scanf("%d",col);
      if(col == block[cnt].col) block[cnt].len++;
      else{
          block[++cnt].len = 1;
          block[cnt].col = col;
      }
  }
  
  N = cnt;
  ```

- 因为这是一道区间dp，所以基本框架还是区间dp的框架：

  ```cpp
  for(int len = 0;len <= N;len++){
      for(int l = 1;l + len <= N;l++){
          int r = l + len;
          ...
      }
  }
  ```

- 对于情况1，我们可以预处理出来如果每个方块后有 $k$ 个同色方块时可以获得的分数

  $k$ 最大为 $\text{sum}_n-\text{sum}_{j-1}$ ，$\text{sum}_i$ 表示前 $i$ 段颜色相同的区间包含的方块总数，可以预处理出来

  ```cpp
  for(int i = 1;i<=N;i++) sum[i] = sum[i-1] + block[i].len;
  ```

  ```cpp
  for(int k = 0;k <= sum[N]-sum[r-1];k++){
      dp[l][r][k] = dp[l][r-1][0] + pow2(block[r].len + k);
  }
  ```

- 对于情况2，可以枚举 $p$ 所在位置，然后去更新该区间的得分

  ```cpp
  for(int p = l;p<r;p++){
      if(block[p].col != block[r].col) continue;
      for(int k = 0;k<=sum[N]-sum[j-1];k++){
          dp[l][r][k] = max(dp[l][r][k],dp[l][p][block[r].len+k]+dp[p+1][r-1][0])
      }
  }
  ```

最后的结果是 $f(1,N,0)$

### 完整代码

```cpp
#include<bits/stdc++.h>
using namespace std;

const int maxN = 400;

struct Block{
	int len,col;
}block[maxN];

int dp[maxN][maxN][maxN];
int sum[maxN];

int N;
int cnt;

inline int pow2(int x){
	return x * x;
}

inline void INIT(){
	block[0].col = -1;
	cnt = 0;
	memset(sum,0,sizeof(sum));
	memset(dp,0,sizeof(dp));
}

inline void work(int Case){
	INIT();
	scanf("%d",&N);
	for(int i = 1;i<=N;i++){
		int col;
		scanf("%d",&col);
		if(col == block[cnt].col) block[cnt].len++;
		else{
			block[++cnt].len = 1;
			block[cnt].col = col;
		}
	}
	N = cnt;

	for(int i = 1;i<=N;i++) sum[i] = sum[i-1] + block[i].len;

	for(int len = 0;len<=N;len++){
		for(int l = 1;l + len <= N;l++){
			int r = l + len;
			for(int k = 0;k<=sum[N]-sum[r-1];k++){
				dp[l][r][k] = dp[l][r-1][0] + pow2(block[r].len + k);
			}
			for(int p = l;p<r;p++){
				if(block[p].col != block[r].col) continue;
				for(int k = 0;k<=sum[N]-sum[r-1];k++){
					dp[l][r][k] = max(dp[l][r][k],dp[l][p][block[r].len+k]+dp[p+1][r-1][0]);
				}
			}
		}
	}
	printf("Case %d: %d\n",Case,dp[1][N][0]);
}
int main(){
	int T;
	scanf("%d",&T);
	int Case = 1;
	while(Case <= T) work(Case++);
	return 0;
}

```
