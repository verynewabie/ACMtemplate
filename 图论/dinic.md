```c++
//最大匹配数=最小点覆盖=总点数-最大独立集=总点数-最小路径覆盖
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

