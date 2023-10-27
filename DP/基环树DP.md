## 单调型

给一个基环树森林，求所有连通块的最远点对的距离和

```cpp
int n,idx=2,e[N],ne[N],w[N],h[N],prep[N],prel[N];
bool vis[N],ins[N];
ll sum[N],ans,s[N],d[N];
int cnt,cir[N],ed[N];
int q[N],hh,tt;
void add(int a,int b,int c){
    e[idx]=b,ne[idx]=h[a],w[idx]=c,h[a]=idx++;
}
void dfs_c(int u,int from){
    vis[u]=ins[u]=1;
    for(int i=h[u];~i;i=ne[i]){
        if(i==(from^1)) continue;
        int j=e[i];
        prep[j]=u,prel[j]=w[i];
        if(!vis[j]) dfs_c(j,i);
        else if(ins[j]){
            cnt++;
            ed[cnt]=ed[cnt-1];
            ll tmp=w[i];
            for(int k=u;;k=prep[k]){
                s[k]=tmp;
                tmp+=prel[k];
                cir[++ed[cnt]]=k;
                if(k==j) break;
            }
        }
    }
    ins[u]=0;
}
ll dfs_d(int u){
    vis[u]=1;
    ll d1=0,d2=0;
    for(int i=h[u];~i;i=ne[i]){
        int j=e[i];
        if(vis[j]) continue;
        ll dist=dfs_d(j)+w[i];
        if(dist>=d1) d2=d1,d1=dist;
        else if(dist>d2) d2=dist;
    }
    ans=max(ans,d1+d2);
    return d1;
}
void solve(){
    memset(h,-1,sizeof h);
    cin>>n;
    for(int i=1;i<=n;i++){
        int j,w;
        cin>>j>>w;
        add(i,j,w);
        add(j,i,w);
    }
    for(int i=1;i<=n;i++) if(!vis[i]) dfs_c(i,0);
    memset(vis,0,sizeof vis);
    for(int i=1;i<=ed[cnt];i++) vis[cir[i]]=1;
    ll res=0;
    for(int i=1;i<=cnt;i++){
        ans=0;
        int sz=0;
        for(int j=ed[i-1]+1;j<=ed[i];j++){
            int k=cir[j];
            d[sz]=dfs_d(k);
            sum[sz]=s[k];
            sz++;
        }
        for(int j=0;j<sz;j++) d[sz+j]=d[j],sum[sz+j]=sum[j]+sum[sz-1];
        hh=0,tt=-1;
        for(int j=0;j<sz*2;j++){
            if(hh<=tt&&j-q[hh]>=sz) hh++;
            if(hh<=tt) ans=max(ans,d[j]+d[q[hh]]+sum[j]-sum[q[hh]]);
            while(hh<=tt&&d[q[tt]]-sum[q[tt]]<=d[j]-sum[j]) tt--;
            q[++tt]=j;
        }
        res+=ans;
    }
    cout<<res<<endl;
}
```

## 断环型

每个点有一个权值，不能同时选择一条边上的两个点，求最大权值

```cpp
int n;
int h[N], e[N], rm[N], w[N], ne[N], idx;
ll f1[N][2], f2[N][2];
bool st[N], ins[N];
ll ans;
inline void add(int a, int b){
    e[idx] = b, ne[idx] = h[a], h[a] = idx ++ ;
}
void dfs_f(int u, int ap, LL f[][2]){
    for (int i = h[u]; ~i; i = ne[i]){
        if (rm[i]) continue;
        int j = e[i];
        dfs_f(j, ap, f);
        f[u][0] += max(f[j][0], f[j][1]);
    }
    f[u][1] = -INF;
    if (u != ap){
        f[u][1] = w[u];
        for (int i = h[u]; ~i; i = ne[i]){
            if (rm[i]) continue;
            int j = e[i];
            f[u][1] += f[j][0];
        }
    }
}
void dfs_c(int u, int from){
    st[u] = ins[u] = true;
    for (int i = h[u]; ~i; i = ne[i]){
        int j = e[i];
        if (!st[j]) dfs_c(j, i);
        else if (ins[j]){
            rm[i] = 1;
            dfs_f(j, -1, f1);
            dfs_f(j, u, f2);
            ans += max(f1[j][0], f2[j][1]);
        }
    }
    ins[u] = false;
}
int main(){
    scanf("%d", &n);
    memset(h, -1, sizeof h);
    for (int i = 1; i <= n; i ++ ){
        int a, b;
        scanf("%d%d", &a, &b);
        add(b, i); //为什么这样建图,见下图
        w[i] = a;
    }
    for (int i = 1; i <= n; i ++ )
        if (!st[i])
            dfs_c(i, -1);
    printf("%lld\n", ans);
    return 0;
}
```

![image-20221208004929706](https://cartoonwqy.oss-cn-nanjing.aliyuncs.com/boke/202212080049790.png)

这样的话我们最终找到环时$u=4 \ \ j=1$，遍历不到$1$的子树，所以我们向每个点连边，而不是从每个点出发连边。