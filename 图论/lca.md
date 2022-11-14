## 倍增

```c++
int fa[N][K];
int dep[N],q[N];
vector<int> to[N];
void bfs(int root){
    memset(dep,0x3f,sizeof dep);
    dep[0]=0,dep[root]=1;
    int hh=0,tt=0;
    q[0]=root;
    while(hh<=tt){
        int t=q[hh++];
        for(auto j:to[t]){
            if(dep[j]>dep[t]+1){
                dep[j]=dep[t]+1;
                q[++tt]=j;
                fa[j][0]=t;
                for(int k=1;k<K;k++){
                    fa[j][k]=fa[fa[j][k-1]][k-1];
                }
            }
        }
    }
}
int lca(int a,int b){//一堆节点的lca为dfn最小的和dfn最大的两个点的lca
    if(dep[a]<dep[b]) swap(a,b);
    for(int k=K-1;k>=0;k--){
        if(dep[fa[a][k]]>=dep[b]){
            a=fa[a][k];
        }
    }
    if(a==b) return a;
    for(int k=K-1;k>=0;k--){
        if(fa[a][k]!=fa[b][k]){
            a=fa[a][k];
            b=fa[b][k];
        }   
    }
    return fa[a][0];
} 
```

