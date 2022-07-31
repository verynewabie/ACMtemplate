## 倍增

```c++
int fa[N][16];
int depth[N],q[N];
vector<int> to[N];
void bfs(int root){
    memset(depth,0x3f,sizeof depth);
    depth[0]=0,depth[root]=1;
    int hh=0,tt=0;
    q[0]=root;
    while(hh<=tt){
        int t=q[hh++];
        for(auto j:to[t]){
            if(depth[j]>depth[t]+1){
                depth[j]=depth[t]+1;
                q[++tt]=j;
                fa[j][0]=t;
                for(int k=1;k<K;k++){
                    fa[j][k]=fa[fa[j][k-1]][k-1];
                }
            }
        }
    }
}
int lca(int a,int b){
    if(depth[a]<depth[b]) swap(a,b);
    for(int k=K-1;k>=0;k--){
        if(depth[fa[a][k]]>=depth[b]){
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

