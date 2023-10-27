时间复杂度$O(n^{2}m)$，跑二分图是$O(m\sqrt n)$的

```c++
//最大匹配数=最小点覆盖=总点数-最大独立集=总点数-最小路径覆盖
//最小点覆盖：选出点集S,使得每条边至少有一个端点在S中
//最大独立集：选出点集S,使用任意两点都不相邻
//最大团：G的最大团是指G中所含顶点数最多的完全子图
//补图：点不变,边取反
//原图的最大团等于补图的最大独立集
int e[M],ne[M],h[N],idx,f[M],S,T;
int dep[N],cur[N],hh,tt,q[N];
void add(int a,int b,int c){
    e[idx]=b,ne[idx]=h[a],f[idx]=c,h[a]=idx++;
    e[idx]=a,ne[idx]=h[b],f[idx]=0,h[b]=idx++;
}
bool bfs(){
    memset(dep,0,sizeof dep);
    q[0]=S,hh=tt=0,cur[S]=h[S],dep[S]=1;
    while(hh<=tt){
        auto t=q[hh++];
        for(int i=h[t];~i;i=ne[i]){
            int j=e[i];
            if(!dep[j]&&f[i]){
                dep[j]=dep[t]+1;
                cur[j]=h[j];
                if(j==T) return true;
                q[++tt]=j;
            }
        }
    }
    return false;
}
int find(int u,int limit){
    if(u==T) return limit;
    int flow=0;
    for(int i=cur[u];~i&&flow<limit;i=ne[i]){
        int j=e[i];
        cur[u]=i;
        if(dep[j]==dep[u]+1&&f[i]){
            int t=find(j,min(f[i],limit-flow));
            if(!t) dep[j]=0;
            flow+=t,f[i]-=t,f[i^1]+=t;
        }
    }
    return flow;
}
int dinic(){
    int res=0,flow;
    while(bfs())
        while(flow=find(S,INF))
            res+=flow;
    return res;
}
```

![image-20220902200114965](https://cartoonwqy.oss-cn-nanjing.aliyuncs.com/boke/image-20220902200114965.png)

tarjan缩点时只考虑流量为1的边，只有二分图能用这个

对于每个点是否一定在最大匹配中，可以先“删去”改点，即修改与改点匹配的点到汇点的边的流量，然后再增广

## 无源汇上下界可行流

对于每条边（从$a$到$b$）都有一个上界$u$和下界$v$，我们把流量转为$0\sim u-v$，那么对于一个点，以它为入点的边相当于少流入了$v$，我们记录下来（加上），对于以它为出点的边，相当于少流出了，我们也记录下来（减去），最后如果该点权值大于$0$，说明少流入了，从$S$向此点连一条容量为该权值的边，否则从该点向$T$连边。最后如果最大流满流，说明存在可行流。

## 有源汇上下界最大流

```cpp
int n, m, S, T;
int h[N], e[M], f[M], ne[M], idx;
int q[N], d[N], cur[N], A[N];
void add(int a, int b, int c){
    e[idx] = b, f[idx] = c, ne[idx] = h[a], h[a] = idx ++ ;
    e[idx] = a, f[idx] = 0, ne[idx] = h[b], h[b] = idx ++ ;
}

bool bfs(){
    int hh = 0, tt = 0;
    memset(d, -1, sizeof d);
    q[0] = S, d[S] = 0, cur[S] = h[S];
    while (hh <= tt){
        int t = q[hh ++ ];
        for (int i = h[t]; ~i; i = ne[i]){
            int ver = e[i];
            if (d[ver] == -1 && f[i]){
                d[ver] = d[t] + 1;
                cur[ver] = h[ver];
                if (ver == T) return true;
                q[ ++ tt] = ver;
            }
        }
    }
    return false;
}
int find(int u, int limit){
    if (u == T) return limit;
    int flow = 0;
    for (int i = cur[u]; ~i && flow < limit; i = ne[i]){
        cur[u] = i;
        int ver = e[i];
        if (d[ver] == d[u] + 1 && f[i]){
            int t = find(ver, min(f[i], limit - flow));
            if (!t) d[ver] = -1;
            f[i] -= t, f[i ^ 1] += t, flow += t;
        }
    }
    return flow;
}
int dinic(){
    int r = 0, flow;
    while (bfs()) while (flow = find(S, INF)) r += flow;
    return r;
}
int main(){
    int s, t;
    scanf("%d%d%d%d", &n, &m, &s, &t);
    S = 0, T = n + 1;
    memset(h, -1, sizeof h);
    while (m -- ){
        int a, b, c, d;
        scanf("%d%d%d%d", &a, &b, &c, &d);
        add(a, b, d - c);
        A[a] -= c, A[b] += c;
    }
    int tot = 0;
    for (int i = 1; i <= n; i ++ )
        if (A[i] > 0) add(S, i, A[i]), tot += A[i];
        else if (A[i] < 0) add(i, T, -A[i]);
    add(t, s, INF);
    if (dinic() < tot) puts("No Solution");
    else{
        int res = f[idx - 1];
        S = s, T = t;
        f[idx - 1] = f[idx - 2] = 0;
        printf("%d\n", res + dinic());
    }
    return 0;
}
```

## 有源汇上下界最小流

```cpp
int n, m, S, T;
int h[N], e[M], f[M], ne[M], idx;
int q[N], d[N], cur[N], A[N];
void add(int a, int b, int c){
    e[idx] = b, f[idx] = c, ne[idx] = h[a], h[a] = idx ++ ;
    e[idx] = a, f[idx] = 0, ne[idx] = h[b], h[b] = idx ++ ;
}
bool bfs(){
    int hh = 0, tt = 0;
    memset(d, -1, sizeof d);
    q[0] = S, d[S] = 0, cur[S] = h[S];
    while (hh <= tt){
        int t = q[hh ++ ];
        for (int i = h[t]; ~i; i = ne[i]){
            int ver = e[i];
            if (d[ver] == -1 && f[i]){
                d[ver] = d[t] + 1;
                cur[ver] = h[ver];
                if (ver == T) return true;
                q[ ++ tt] = ver;
            }
        }
    }
    return false;
}
int find(int u, int limit){
    if (u == T) return limit;
    int flow = 0;
    for (int i = cur[u]; ~i && flow < limit; i = ne[i]){
        cur[u] = i;
        int ver = e[i];
        if (d[ver] == d[u] + 1 && f[i]){
            int t = find(ver, min(f[i], limit - flow));
            if (!t) d[ver] = -1;
            f[i] -= t, f[i ^ 1] += t, flow += t;
        }
    }
    return flow;
}
int dinic(){
    int r = 0, flow;
    while (bfs()) while (flow = find(S, INF)) r += flow;
    return r;
}
int main(){
    int s, t;
    scanf("%d%d%d%d", &n, &m, &s, &t);
    S = 0, T = n + 1;
    memset(h, -1, sizeof h);
    while (m -- ){
        int a, b, c, d;
        scanf("%d%d%d%d", &a, &b, &c, &d);
        add(a, b, d - c);
        A[a] -= c, A[b] += c;
    }
    int tot = 0;
    for (int i = 1; i <= n; i ++ )
        if (A[i] > 0) add(S, i, A[i]), tot += A[i];
        else if (A[i] < 0) add(i, T, -A[i]);
    add(t, s, INF);
    if (dinic() < tot) puts("No Solution");
    else{
        int res = f[idx - 1];
        S = t, T = s;
        f[idx - 1] = f[idx - 2] = 0;
        printf("%d\n", res - dinic());
    }
    return 0;
}
```

## 关键边

即扩流后整个网络最大流就会增加的边。

```cpp
int n, m, S, T;
int h[N], e[M], f[M], ne[M], idx;
int q[N], d[N], cur[N];
bool vis_s[N], vis_t[N];
void add(int a, int b, int c){
    e[idx] = b, f[idx] = c, ne[idx] = h[a], h[a] = idx ++ ;
    e[idx] = a, f[idx] = 0, ne[idx] = h[b], h[b] = idx ++ ;
}
bool bfs(){
    int hh = 0, tt = 0;
    memset(d, -1, sizeof d);
    q[0] = S, d[S] = 0, cur[S] = h[S];
    while (hh <= tt){
        int t = q[hh ++ ];
        for (int i = h[t]; ~i; i = ne[i]){
            int ver = e[i];
            if (d[ver] == -1 && f[i]){
                d[ver] = d[t] + 1;
                cur[ver] = h[ver];
                if (ver == T) return true;
                q[ ++ tt] = ver;
            }
        }
    }
    return false;
}
int find(int u, int limit){
    if (u == T) return limit;
    int flow = 0;
    for (int i = cur[u]; ~i && flow < limit; i = ne[i]){
        cur[u] = i;
        int ver = e[i];
        if (d[ver] == d[u] + 1 && f[i]){
            int t = find(ver, min(f[i], limit - flow));
            if (!t) d[ver] = -1;
            f[i] -= t, f[i ^ 1] += t, flow += t;
        }
    }
    return flow;
}
int dinic(){
    int r = 0, flow;
    while (bfs()) while (flow = find(S, INF)) r += flow;
    return r;
}
void dfs(int u, bool st[], int t){
    st[u] = true;
    for (int i = h[u]; ~i; i = ne[i]){
        int j = i ^ t, ver = e[i];
        if (f[j] && !st[ver])
            dfs(ver, st, t);
    }
}
int main(){
    scanf("%d%d", &n, &m);
    S = 0, T = n - 1;
    memset(h, -1, sizeof h);
    for (int i = 0; i < m; i ++ ){
        int a, b, c;
        scanf("%d%d%d", &a, &b, &c);
        add(a, b, c);
    }
    dinic();
    dfs(S, vis_s, 0);
    dfs(T, vis_t, 1);
    int res = 0;
    for (int i = 0; i < m * 2; i += 2)
        if (!f[i] && vis_s[e[i ^ 1]] && vis_t[e[i]])
            res ++ ;
    printf("%d\n", res);
    return 0;
}
```

## 无向边

对于无向边，如果两个方向的消耗相同，我们给正反边都加同样的流量即可。

## 二分图博弈

给定起点$H$和一张二分图，A和B轮流操作，每次只能选与上个被选择的点（第一回合则是点 $H$ ）相邻的点，且不能选择已选择过的点，无法选点的人输掉。

结论：如果最大匹配**一定**包含$H$ ，那么先手必胜，否则先手必败。

解法：采用Dinic，不要根据有没有$H$点建两次图。而是在建图时把涉及$H$点的边存下来，跑完第一次Dinic后再建这些边，第二次Dinic看有没有增加流量。

## 最小路径覆盖

![image-20230722125539922](https://cartoonwqy.oss-cn-nanjing.aliyuncs.com/2023/07/2a554497e3cca0f8c5fe777d25e9cb57.png)

对于最小路径重复覆盖，可以先Floyd处理一下点对间的连通性，然后根据Floyd之后的边来建图即可。

对于最小路径覆盖问题，可以用并查集维护起点，然后dfs输出答案。
