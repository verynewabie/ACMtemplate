```cpp
dfs(1,0,0);
void dfs(int u,int fa,bool clean){
    for(auto j:to[u]){//先算轻儿子的贡献再删除
        if(j==fa||j==son[u]) continue;
        dfs(j,u,1);
    }
    if(son[u]) dfs(son[u],u,0);//算重儿子贡献不删除
    insert(val);//加上u的贡献
    for(auto j:to[u]){//计算轻儿子的贡献
        if(j==fa||j==son[u]) continue;
        calc(j,u);
    }
    ans[u]=sum;
    if(clean) ;
}
```

