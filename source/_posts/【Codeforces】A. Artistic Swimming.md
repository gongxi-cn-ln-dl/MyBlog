---
title: 【Codeforces】A. Artistic Swimming
date: 2022-03-14 16:31:00
tags: [ACM, 题解, 总结]
mathjax: true
excerpt: false
---

## A. Artistic Swimming

[原题链接](https://codeforces.com/gym/372694/problem/A)

其实这道题挺简单的，基本上写个 $\operatorname{Dijkstra}$ 就出来了

不过我掉进了一个坑里：求多个点之间的最短路，这不就是多源最短路吗？

然后我非常果断地写了个 $\operatorname{Floyd}$ 

众所周知， $\operatorname{Floyd}$ 有三层循环，复杂度为 $O(N^3)$

但是，这张图有点权（算是吧

且每次询问的点权都不一定相同

这也就造成了，虽然看上去是多源最短路，实际上每次询问都需要重新跑一次 $O(N^3)$ 的 $\operatorname{Floyd}$ 

实际复杂度为 $O(N^4)$ 显然这过不去



我当时意识到了不能每次都求一遍多源最短路之后居然还没意识到这道题是单源最短路

我考虑了好久，最后想写一个改进版的 $\operatorname{Floyd}$ ，记录从 $i$ 到 $j$ 的所有路径经过点的数量以及路径长度

但是我不会写（

后来又盯了它好长时间，突然意识到完全没有必要求多源最短路

这完全可以用单源最短路，只不过在求的时候每条边都要加上一个 $x$ 

复杂度 $O(N^3)$ ，2121ms 过了

（写 $\operatorname{Dijkstra}$ 还因为变量名冲突Debug了好久，最后是重新写的函数）

（这道题是在4小时59分钟末切掉的，右下角的Accepped几乎与比赛结束的弹窗一起出现，钛刺激辣

```cpp
#include<bits/stdc++.h>
using namespace std;
 
const int maxN = 2000;

int n,m,qq;
int u,v,w;
int x;
int s;
 
int tot;
int head[maxN];
struct Edge{
	int to_;
	int nxt;
	int dis;
}edge[maxN];
 
void add_edge(int u,int v,int w){
	edge[++tot].dis = w;
	edge[tot].to_ = v;
	edge[tot].nxt = head[u];
	head[u] = tot;
}
 
struct Node{
	int dis;
	int pos;
	friend bool operator < (Node a, Node b){
		return a.dis > b.dis;
	}
};
 
priority_queue<Node> q;
 
int dis[maxN];
int vis[maxN];
 
void Dijkstra(int xx){
	memset(dis,0x3f,sizeof(dis));
	memset(vis,0,sizeof(vis));
	dis[s] = xx;
	
	q.push((Node){xx,s});
	while(!q.empty()){
		Node tmp = q.top();
		q.pop();
		int pos = tmp.pos;
		if(vis[pos]) continue;
		vis[pos] = 1;
		for(int i = head[pos];i;i = edge[i].nxt){
			int y = edge[i].to_;
			if(dis[y] > dis[pos] + edge[i].dis + xx){
				dis[y] = dis[pos] + edge[i].dis + xx;
				if(!vis[y]){
					q.push((Node){dis[y],y});
				}
			}
		}
	}
}
 
int main(){
	scanf("%d%d%d",&n,&m,&qq);
 
	for(int i = 0;i<m;i++){
		scanf("%d%d%d",&u,&v,&w);
		add_edge(u,v,w);		
	}
	while(qq--){
		scanf("%d%d%d",&s,&v,&x);
		Dijkstra(x);
		
		printf("%d\n",dis[v] == 0x3f3f3f3f ? -1 : dis[v]);
	}
	return 0;
}
```
