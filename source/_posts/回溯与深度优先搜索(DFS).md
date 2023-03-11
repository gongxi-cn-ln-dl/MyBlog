---
title: 回溯与深度优先搜索(DFS)
date: 2022-01-23 10:57:00
tags: []
mathjax: true
excerpt: false
---

## 回溯与深度优先搜索（DFS）

~~完了我真成Typora忠实用户了 现在在网页端的MD编辑器上码血压飙升（当然要是用博客园自带的可能已经当场去世了~~

### 板子（伪

```cpp
void dfs(int x,int y){
	if(条件){
		……
		return;
	}
	for(int i = x;i<=n;i++){
		A[y] = i;
		dfs(i+1,y+1);
	}
}
```

### 例题

- [P1157 组合的输出](https://www.luogu.com.cn/problem/P1157)

   1. DFS法
   
   其实跟全排列差不多（
   
   ```cpp
	void dfs(int x,int y){
		//x为上一步填的数，y为已填位数 
		if(y == r){
			//满足条件就输出 
			for(int i = 0;i<r;i++){
				printf("%3d",A[i]);
			}
			printf("\n");
			return;
		}
		//i从x开始可以确保不重复，就不需要像回溯算法那样开一个数组确保当前的数没有用过 
		for(int i = x;i<=n;i++){
			//填数 
			A[y] = i;
			//当前位数填了i，下一位就从[i+1,n]中选数去填入第y+1位 
			dfs(i+1,y+1);
		}
	}
	```

   2. 回溯法
   
   ```cpp
   void S(int l,int x){
		//l用来记录上一位填的数（因为是组合所以只能向后不能向前）
		//x用来记录当前位数 
		for(int i = l+1;i<=n;i++){
			//B数组用来记录是否用过i 
			if(!B[i]){
				//A数组用来填数 
				A[x] = i;
				B[i] = 1;
				if(x == r){
					//符合条件就输出 
					print();
				}else{
					//否则继续填数 
					S(i,x+1);
				}
			//回溯（注意这句不能放进else中
			//因为输出后如果i还没走到n，仍可以回溯到这一个数 
			B[i] = 0;
			}
		}
	}
	```

- [P2404 自然数的拆分问题](https://www.luogu.com.cn/problem/P2404)

   1. DFS法
   
   ```cpp
   	void dfs(int l,int sum,int z){
		//l为上一步填的数字，sum为当前数字和，z为当前位数 
		if(sum>n) return;
		//符合条件就输出 
		if(sum == n){
			print(z);
			return;
		}
		//否则继续深搜，从l开始，因为拆分后的数字一定小于原数字所以不用带等号 
		for(int i = l;i<n;i++){
			A[z] = i;
			//当前位数填了i，所以总和要加上i 
			dfs(i,sum+i,z+1);
		
	}
	```
	
   2. 回溯法
	
	```cpp
	void Search(int l,int x,int s){
		//l为上一位填的数
		//因为这一位填的数一定大于等于上一位填的数，所以填的数从l开始
		//x为当前位数
		//s为目前需要继续拆分的数 
		for(int i = l;i<=s;i++){
			//拆分一定是当前要填的数小于被拆分数 
			if(i<n){
				A[x] = i;
				//在第x位填数之后，下一个待拆分的数是s-i 
				s -= i; 
					if(s == 0){
						print(x);
				}else{
					//当前填了i，下一位填的数从i开始 
					Search(i,x+1,s);
				}
				//回溯 
				s += i;
			}
		}
	}
	```

- [又一年拔河比赛](#)

   ~~找不到题面了，找到了再把题面补上~~
   
   题面大概是
   
   n个人进行拔河比赛，为了公平，分成的两队人数最多相差1人，求两队体重差的最小绝对值。
   
   易知分成的两队其中一队的人数是$\lfloor \frac{n}{2} \rfloor$
   
   那么只需要深搜出n人取$\lfloor \frac{n}{2} \rfloor$人的所有组合并求出其与$\lfloor \frac{W_总}{2} \rfloor$的差的绝对值并记录，最后在所有被记录过的差值中选出最小的即可。
   
   代码(其实在组合的输出那题的代码上稍微改改就好了)
   
   ```cpp
	#include<bits/stdc++.h>
	using namespace std;

	int A[23];
	int B[23];
	int n,r;

	int rx = 1e9;
	int alw = 0;

	void dfs(int x,int y){
		if(y == r){
			int s = 0;
			for(int i = 0;i<r;i++){
				s += A[i];
			}
			rx = min(rx,abs(alw-2*s));
			return;
		}
		for(int i = x;i<n;i++){
			A[y] = B[i];
			dfs(i+1,y+1);
		}
	}

	int main(){
		int t;
		scanf("%d",&t);
		for(int i = 0;i<t;i++){
			alw = 0;
			memset(A,0,sizeof(A));
			memset(B,0,sizeof(B));
			scanf("%d",&n);
			r = n/2;
			for(int i = 0;i<n;i++){
				scanf("%d",&B[i]);
				alw += B[i];
			}
			dfs(0,0);
			printf("%d\n",rx);
			rx = 1e9;
		} 
		return 0;
	}
	```

- [P1025 数的划分](https://www.luogu.com.cn/problem/P1025)

   在拔河比赛的基础上改的代码（

   只不过需要进行一个剪枝

   ```cpp
	void dfs(int l,int sum,int z){
		if(sum == n && z == r){
			cnt++;
			return;
		}
		for(int i = l;i<n;i++){
			//z+1>r的时候就停止
			//一直卡后两个点，最后发现是sum+i>n写成了sum+1>n...... 
			if(sum+i>n||z+1>r) return;
			dfs(i,sum+i,z+1);
		}
		return;
   }
   ```

   
