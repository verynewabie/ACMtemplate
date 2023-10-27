```cpp
//spfa求负环：若某个点入队n次或者最短路中点数大于等于n，就有负环
int n,m,S;
int dist[N],cnt[N];//cnt数组记录以每个点结尾的最短路经过的边数
int idx,e[M],ne[M],h[N],w[M];
bool st[N];
void add(int a,int b,int c){
    e[idx]=b,ne[idx]=h[a],w[idx]=c,h[a]=idx++;
}
bool spfa(){//求最短路并判断是否有负环
    queue<int> q;
    memset(dist,0,sizeof dist);
    memset(st,0,sizeof st);
    memset(cnt,0,sizeof cnt);
    q.push(S);
    while(q.size()){
        auto t=q.front();
        q.pop();
        st[t]=0;
        for(int i=h[t];~i;i=ne[i]){
            int j=e[i];
            if(dist[j]>dist[t]+w[i]){
                dist[j]=dist[t]+w[i];
                cnt[j]=cnt[t]+1;
                if(cnt[j]>=n) return true;//有负环
                if(!st[j])q.push(j),st[j]=1;
            }
        }
    }
    return false;
}
```

