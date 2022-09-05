```cpp
void dfs(int u){
    dfn[++timestamp]=u;sz[u]=1;
    for(auto j:to[u]){
        pre[j]=pre[u]+sum[u];
        dfs(j);
        siz[u]+=siz[j];
    }
}
//pre[i]记录选它之前必须要选的代价之和
for(int i=1;i<=timestamp;i++){//timestamp是时间戳
    for(int j=pre[dfn[i]];j<=m-v[dfn[i]];j++)
        dp[i+1][j+v[dfn[i]]]=max(dp[i+1][j+v[dfn[i]]],dp[i][j]+w[dfn[i]]);
    for(int j=pre[dfn[i]];j<=m;j++)
        dp[i+sz[dfn[i]]][j]=max(dp[i+sz[dfn[i]]][j],dp[i][j]);
}
//答案为dp[timestamp+1][m]
```

