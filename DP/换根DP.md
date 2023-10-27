给定一个$n$个节点的树，求一个点使得该点为根时所有节点的深度之和最大。

```cpp
int dp[N], siz[N];
void dfs(int u, int fa) {
    siz[u] = 1;
    for (int v : g[u]) {
        if (v == fa)continue;
        dfs(v, u);
        siz[u] += siz[v];
        dp[u] += dp[v] + siz[v];
    }
}
int ans = 0, ans_id = 0;
void dfs1(int u, int fa) {
    if (dp[u] > ans) {
        ans = dp[u];
        ans_id = u;
    }
    for (int v : g[u]) {
        if (v == fa)continue;
        dp[u] -= (dp[v] + siz[v]); 
        int t1 = dp[v] + siz[v];
        siz[u] -= siz[v]; 
        int t2 = siz[v];
        dp[v] += (dp[u] + siz[u]);
        siz[v] += siz[u];
        dfs1(v, u);
        dp[u] += t1;
        siz[u] += t2;
    }
}
void slove() {
    cin >> n;
    for (int i = 1; i <= n - 1; i++) {
        int u, v; 
        cin >> u >> v;
        g[u].push_back(v);
        g[v].push_back(u);
    }
    dfs(1, 0);
    dfs1(1, 0);
    cout << ans_id << endl;
}
```

