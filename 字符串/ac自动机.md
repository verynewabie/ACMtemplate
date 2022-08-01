```c++
#include<bits/stdc++.h>
const int N=1e4+10,M=51,TOT=N*M,S=1e6+10;
char s[S];
int tr[TOT][26],cnt[TOT],n,T,q[TOT],hh,tt,idx,ne[TOT];
bool vis[TOT];
void insert(){
	int u=0;
	for(int i=0;s[i];i++){
		int k=s[i]-'a';
		if(!tr[u][k]) tr[u][k]=++idx;
		u=tr[u][k];
	}
	cnt[u]++;
}
void build(){//build之后q数组里就是拓扑序
	hh=0,tt=-1;
	for(int i=0;i<26;i++)
		if(tr[0][i])
			q[++tt]=tr[0][i];
	while(hh<=tt){
		auto t=q[hh++];
		for(int k=0;k<26;k++){
			if(!tr[t][k]) continue;
			int j=ne[t];
			while(j&&!tr[j][k]) j=ne[j];
			if(tr[j][k]) j=tr[j][k];
			ne[tr[t][k]]=j;
			q[++tt]=tr[t][k];
		}
	}
}
//统计多少个单词出现了
int main(){
	scanf("%s",s);
	int ans=0;
	for(int i=0,j=0;s[i];i++){
		int k=s[i]-'a';
		while(j&&!tr[j][k]) j=ne[j];
		if(tr[j][k]) j=tr[j][k];
		int tmp=j;
		while(tmp&&!vis[tmp]){
			ans+=cnt[tmp];
			vis[tmp]=1;
			tmp=ne[tmp];
		}
	}
	printf("%d\n",ans);
	return 0;
}
```

