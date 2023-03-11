---
title: 【YbtOJ】单词替换
date: 2022-03-03 23:00:00
tags: [题解, 注意事项]
mathjax: true
excerpt: false
---



题面就不放了

整体思路挺简单的

就是读入句子，按空格分成若干单词存起来

读入单词 $a,b$ ，扫一遍存的单词进行替换

最后输出

如果按照正统做法其实到这里就结束了

### 不算坑的坑

```cpp
#include<bits/stdc++.h>
using namespace std;

string S[10000];

int main(){
	char s[150];
	scanf("%[^\n]",s);
	string f,t;
	cin>>f>>t;
	char wd[150];
	int p = 0;
	int ind = 0;
	for(int i = 0;i<=strlen(s);i++){
		if(s[i] == ' ' || i == strlen(s)){
			wd[p] = '\0';
			string SS = wd;
			if(SS == f) SS = t;
			S[ind++] = SS;
			for(int j = 0;j<p;j++) wd[j] = '\0';
			p = 0;
		}else wd[p++] = s[i];
	}
	for(int i = 0;i<ind;i++){
		cout<<S[i]<<" ";
	}
	return 0;
}
```

$\small\text{YbtOJ上的90分代码}$

但是，数据应该是在 $\text{Windows}$ 下出的

这意味着什么呢

$\text{Windows}$ 下的换行符是 `\r\n`

$\text{Linux}$ 下的换行符是 `\n`

这一点区别在使用 `getline(cin,s)` 的时候没有影响

但是当使用 `scanf("%[^\n]")` 的时候，问题出现了

`scanf("%[^\n]")`的意思是一直读，直到遇见 `\n` 为止

**这会造成每一行最后一个字符串多读了一个 `\r`**

多出来的这个 `\r` 会对字符串匹配造成影响

意思就是，**一旦需要更改的字符串出现在原字符串的末尾，这个字符串是无法被匹配到并更改的**

解决方法也很简单

只需要把 `scanf("%[^\n]")` 改成 `scanf("%[^\r\n]") ` 即可

再或者直接用 `getline(cin,s)` （不过这里 `s` 为 `string` 型）

~~我在思考这组数据是巧合还是特意卡我这种人呢（毕竟在 openjudge 上都AC了~~
