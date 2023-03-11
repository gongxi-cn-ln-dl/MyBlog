---
title: 【模板】 Floyd, Dijkstra, SPFA
date: 2022-06-16 21:02:00
tags: [模板]
mathjax: true
excerpt: false
---



```cpp
//Floyd
inline void Floyd(){
    memset(dis,0x3f,sizeof(dis));
	for(int k = 1;k<=N;k++){
		for(int i = 1;i<=N;i++){
			for(int j = 1;j<=N;j++){
				dis[i][j] = min(dis[i][k]+dis[k][j],dis[i][j]);
			}
		}
	}
} 
```



```cpp
//Dijkstra
struct Node{
	int dis,pos;
	friend bool operator < (Node A,Node B){
		return A.dis > B.dis;
	} 
	Node(int d,int p) : dis(d) , pos(p) {};
};

priority_queue<Node> Q;
int dis[maxN];
int vis[maxN];

inline void Dijkstra(int u){
	memset(dis,0x3f,sizeof(dis));
	memset(vis,0,sizeof(vis));
	
	dis[u] = 0;
	vis[u] = 1;
	Q.push(Node(0,u));
	
	while(!Q.empty()){
		int pos = Q.top().pos;
		Q.pop();
		
		for(int i = head[pos];i;i = edge[i].nxt){
			int to = edge[i].to;
			int wei = edge[i].w;
			if(dis[to] > dis[pos] + wei){
				dis[to] = dis[pos] + wei;
				if(!vis[to]) Q.push(Node(dis[to],to));
			}
		}
	}
}
```



```cpp
//SPFA
inline void SPFA(int u){
	memset(dis,0x3f,sizeof(dis));
	memset(vis,0,sizeof(vis));
	
	queue<int>Q;
	
	Q.push(u);
	vis[u] = 1;
	dis[u] = 0;
	
	while(!Q.empty()){
		int id = Q.front();
		Q.pop();
		vis[id] = 0;
		for(int i = head[id]; i; i = edge[i].nxt) {
			int to = edge[i].to;
			if(dis[to] > dis[id] + edge[i].w) {
				dis[to] = dis[id] + edge[i].w;
				if(!vis[to]) {
					Q.push(to);
					vis[to] = 1;
				}
			}
		}
	}
}
```
