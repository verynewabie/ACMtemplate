1. 是一个小/大根堆
2. $LCA(u,v)$的权值是**原图**$u$到$v$路径上**最大/小边权的最小/大值**
3. 有$2n-1$个点，原图中的点都是树中的叶子节点，树是二叉树

```cpp
int n,m,cnt;
struct edge{
    int a,b,w;
    bool operator<(const edge& ed)const{
		return w<ed.w;
    }
}edges[M];
vector<int> to[N];//即为重构树
int val[N],p[N];
int find(int x){
    return p[x]==x?p[x]:p[x]=find(p[x]);
}
int kruskal(){//返回根节点
    sort(edges+1,edges+m+1);
    for(int i=1;i<2*n;i++) p[i]=i;
    cnt=n;
    for(int i=1;i<=m;i++){
        int a=edges[i].a,b=edges[i].b,w=edges[i].w;
        int pa=find(a),pb=find(b);
        if(pa!=pb){
            val[++cnt]=w;
            p[pa]=p[pb]=cnt;
            to[cnt].push_back(pa);
            to[cnt].push_back(pb);
        }
    }
    return cnt;
}
```

