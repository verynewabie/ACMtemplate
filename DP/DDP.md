## 结合树剖的树上DDP

```cpp
//带修改,求树的最大权独立集的权值
int n,m;
int w[N];
vector<int> to[N];
int id[N],cnt;
int dep[N],sz[N],top[N],fa[N],son[N],ed[N],cast[N];
int f[N][2],g[N][2];
//f[i][0]表示不选i的最大值,f[i][1]表示选i的最大值
//g[i][0]表示不选i、轻儿子可选可不选的最大值,g[i][1]表示选i、轻儿子都不选的最大值
struct martix{
    int n,m;
    martix(int _n=2,int _m=2):n(_n),m(_m){}
    int w[S][S]={0};
    void init(){//对于单位矩阵,这个函数用来初始化
        for(int i=0;i<n;i++)
            for(int j=0;j<m;j++)
                w[i][j]=(i==j);
    }
};
martix operator*(const martix& a,const martix& b){
    martix ans(a.n,b.m);
    for(int i=0;i<a.n;i++)
        for(int j=0;j<b.m;j++)
            for(int k=0;k<a.m;k++)
                ans.w[i][j]=max(ans.w[i][j],a.w[i][k]+b.w[k][j]);
    return ans;
}
struct node{
    int l,r;
    martix now;
}tr[N*4];
void pushup(int u){
    tr[u].now=tr[u<<1].now*tr[u<<1|1].now;
}
void build(int u,int l,int r){
    tr[u]={l,r};
    if(l==r){
        tr[u].now.w[0][0]=g[cast[l]][0];
        tr[u].now.w[0][1]=g[cast[l]][0];
        tr[u].now.w[1][0]=g[cast[l]][1];
        tr[u].now.w[1][1]=-INF;
        return ;
    }
    int mid=l+r>>1;
    build(u<<1,l,mid);
    build(u<<1|1,mid+1,r);
    pushup(u);
}
void modify(int u,int pos,martix& val){
    if(tr[u].l==tr[u].r) return tr[u].now=val,void();
    int mid=tr[u].l+tr[u].r>>1;
    if(pos<=mid) modify(u<<1,pos,val);
    else modify(u<<1|1,pos,val);
    pushup(u);
}
martix query(int u,int l,int r){
    if(tr[u].l>=l&&tr[u].r<=r) return tr[u].now;
    int mid=tr[u].l+tr[u].r>>1;
    if(r<=mid) return query(u<<1,l,r);
    else if(l>mid) return query(u<<1|1,l,r);
    else return query(u<<1,l,r)*query(u<<1|1,l,r);
}
void dfs1(int u,int father,int depth){
    dep[u]=depth,fa[u]=father,sz[u]=1;
    for(auto j:to[u]){
        if(j==father) continue;
        dfs1(j,u,depth+1);
        sz[u]+=sz[j];
        if(sz[son[u]]<sz[j]) son[u]=j;
    }
}
void dfs2(int u,int t){
    id[u]=++cnt,top[u]=t,cast[cnt]=u;
    ed[t]=max(ed[t],cnt);
    g[u][1]=w[u];
    f[u][1]=w[u];
    if(!son[u]) return;
    dfs2(son[u],t);
    f[u][0]+=max(f[son[u]][0],f[son[u]][1]);
    f[u][1]+=f[son[u]][0];
    for(auto j:to[u]){
        if(j==fa[u]||j==son[u]) continue;
        dfs2(j,j);
        f[u][0]+=max(f[j][0],f[j][1]);
        f[u][1]+=f[j][0];
        g[u][0]+=max(f[j][0],f[j][1]);
        g[u][1]+=f[j][0];
    }
}
void update(int u,int val){
    g[u][1]+=val-w[u];
    w[u]=val;
    martix pre,suf;
    while(u!=0){
        pre=query(1,id[top[u]],ed[top[u]]);
        martix tmp;
        tmp.w[0][0]=tmp.w[0][1]=g[u][0];
        tmp.w[1][0]=g[u][1],tmp.w[1][1]=-INF;
        modify(1,id[u],tmp);
        suf=query(1,id[top[u]],ed[top[u]]);
        u=fa[top[u]];
        g[u][0]+=max(suf.w[0][0],suf.w[1][0])-max(pre.w[0][0],pre.w[1][0]);
        g[u][1]+=suf.w[0][0]-pre.w[0][0];
    }
}
void solve(){
    cin>>n>>m;
    for(int i=1;i<=n;i++) cin>>w[i];
    for(int i=1;i<n;i++){
        int a,b;
        cin>>a>>b;
        to[a].push_back(b);
        to[b].push_back(a);
    }
    dfs1(1,0,1);
    dfs2(1,1);
    build(1,1,n);
    while(m--){
        int x,y;
        cin>>x>>y;
        update(x,y);
        martix ans=query(1,id[1],ed[1]);
        cout<<max(ans.w[0][0],ans.w[1][0])<<endl;
    }
}
```

