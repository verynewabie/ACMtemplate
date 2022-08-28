```cpp
int q[N],hh,tt;
struct node{
	int ch[26],fail;
}tr[N];
void insert(){
	int u=0;
	for(int i=0;s[i];i++){
		int k=s[i]-'a';
		if(!tr[u].ch[k]) tr[u].ch[k]=++idx;
		u=tr[u].ch[k];
	}
	tr[u].isend=1;
}
void build(){//build之后q数组里就是拓扑序
	hh=0,tt=-1;
	for(int i=0;i<26;i++)
		if(tr[0].ch[i])
			q[++tt]=tr[0].ch[i];
	while(hh<=tt){
		auto t=q[hh++];
		for(int k=0;k<26;k++){
			if(!tr[t].ch[k]) continue;
			int j=tr[t].fail;
			while(j&&!tr[j].ch[k]) j=tr[j].fail;
			if(tr[j].ch[k]) j=tr[j].ch[k];
			tr[tr[t].ch[k]].fail=j;
			q[++tt]=tr[t].ch[k];
		}
	}
}
/*map写法
struct node{
	map<int,int> ch;
	int fail;
}tr[M];
void insert(){
	for(int i=0;s[i];i++){
		int tmp=s[i]-'a';
		if(tr[u].ch.count(tmp)==0)
			tr[u].ch[tmp]=++idx;
		u=tr[u].ch[tmp];
	}
}
void build(){//build之后q数组里就是拓扑序
	int hh=0,tt=-1;
	for(auto it=tr[0].ch.begin();it!=tr[0].ch.end();it++)
		q[++tt]=it->second;
	while(hh<=tt){
		auto t=q[hh++];
		for(auto it=tr[t].ch.begin();it!=tr[t].ch.end();it++){
			int j=tr[t].fail;
			while(j&&!tr[j].ch.count(it->first)) j=tr[j].fail;
			if(tr[j].ch.count(it->first)) j=tr[j].ch[it->first];
			tr[it->second].fail=j;
			q[++tt]=it->second;
		}
	}
}
*/
//trie图
struct node{
	int ch[26],fail;
	bool end;
}tr[N];
void insert(){
	int u=0;
	for(int i=0;s[i];i++){
		int tmp=s[i]-'a';
		if(!tr[u].ch[tmp]) tr[u].ch[tmp]=++idx;
		u=tr[u].ch[tmp];
	}
	tr[u].end=1;
}
void build(){//build之后q数组里就是拓扑序
	int hh=0,tt=-1;
	for(int i=0;i<26;i++)
		if(tr[0].ch[i])
			q[++tt]=tr[0].ch[i];
	while(hh<=tt){
		auto t=q[hh++];
		for(int i=0;i<26;i++){
			int &p=tr[t].ch[i];
			if(!p){
				p=tr[tr[t].fail].ch[i];
			}
			else{
				tr[p].fail=tr[tr[t].fail].ch[i];//要注意,此时tr[p].fail这个节点不一定已经出队了
				q[++tt]=p;
			}
		}
	}
}
```

