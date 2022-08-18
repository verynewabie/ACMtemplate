```cpp
#include<bits/stdc++.h>
char s[N];
int tr[N][26],n,q[N],hh,tt,idx,ne[N];
void insert(){
	int u=0;
	for(int i=0;s[i];i++){
		int k=s[i]-'a';
		if(!tr[u][k]) tr[u][k]=++idx;
		u=tr[u][k];
	}
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
//trie图
void build(){
    hh=0,tt=-1;
    for(int i=0;i<26;i++)
        if(tr[0][i])
            q[++tt]=tr[0][i];
    while(hh<=tt){
        int t=q[hh++];
        for(int i=0;i<26;i++){
            int &p=tr[t][i];
            if(!p) p=tr[ne[t]][i];
            else{
                ne[p]=tr[ne[t]][i];
                q[++tt]=p;
            }
        }
    }
}
```

