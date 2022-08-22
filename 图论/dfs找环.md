## 有向图

```cpp
bool vis[N];
bool rep[N];
void dfs(int u){//先确保u不含标记
	rep[u]=vis[u]=1;
	for(auto j:to[u]){
		if(rep[j]){
			cout<<"TAK";
			exit(0);
		}
		else if(!vis[j]&&!tag[j]){//比如要找不含标记的环
			dfs(j);
		}
	}
	rep[u]=0;
}
```

