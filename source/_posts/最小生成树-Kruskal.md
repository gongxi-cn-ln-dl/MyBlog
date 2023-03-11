---
title: 最小生成树-Kruskal
date: 2022-02-12 08:07:00
tags: [树, 最小生成树]
mathjax: true
excerpt: false
---



- 将所有边按照边权升序排列

- 依次考虑所有边，如果边的两端在不同联通块内，将该边加入生成树，合并联通块

- 并查集维护每个点处于哪个连通块内

- ```cpp
  for(int i = 0;i<m;i++){
      int u = edge[i].u;
      int v = edge[i].v;
      int x = find(u);
      int y = find(v);
      if(u == v) continue;
      f[u] = v;
      cnt++;//记录数的边权
      add_edge(u,v,w);
      add_edge(v,u,w);
  }
  ```
