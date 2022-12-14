## 有向图缩点

```c++
int cnt,sz[N],timestamp,id[N],stk[N],top,dfn[N],low[N];//连通图tarjan完从cnt到1遍历即是拓扑序
vector<int> to[N];
bool st[N];
void tarjan(int u){
    dfn[u]=low[u]=++timestamp;
    stk[++top]=u,st[u]=1;
    for(auto j:to[u]){
        if(!dfn[j]){
            tarjan(j);
            low[u]=min(low[j],low[u]);
        }
        else if(st[j]) low[u]=min(low[u],dfn[j]);
    }
    if(dfn[u]==low[u]){
        int tmp;
        cnt++;
        do{
            tmp=stk[top--];
            st[tmp]=0;
            id[tmp]=cnt;
            sz[cnt]++;
        }while(tmp!=u);
    }
}
int main(){
    for(int i=1;i<=n;i++)
        if(!dfn[i])
            tarjan(i);
}
```

## 无向图点双

```c++
int cnt,timestamp,stk[N],top,dfn[N],low[N],num,id[N];//多测清top
vector<int> to[N],toc[N*2],dcc[N*2];
bool cut[N];
void tarjan(int u){
    dfn[u]=low[u]=++timestamp;
    stk[++top]=u;
    if(u==root&&to[u].size()==0){
        dcc[++cnt].push_back(u);
        return ;
    }
    int flag=0;
    for(auto j:to[u]){
        if(!dfn[j]){
            tarjan(j);
            low[u]=min(low[u],low[j]);
            if(low[j]>=dfn[u]){
                flag++;
                if(u!=root||flag>1) cut[u]=1;
                cnt++;
                int tmp;
                do{
                    tmp=stk[top--];
                    dcc[cnt].push_back(tmp);
                }while(tmp!=j);
                dcc[cnt].push_back(u);
            }
        }
        else low[u]=min(low[u],dfn[j]);
    }
}
void init(){
    num=cnt;
    for(int i=1;i<=n;i++)
        if(cut[i])
            id[i]=++num;//给割点一个新编号,同时这也是它所独占的点双
    for(int i=1;i<=cnt;i++)
        for(int j=0;j<dcc[i].size();j++){
            int u=dcc[i][j];
            if(cut[u]) toc[i].push_back(id[u]),toc[id[u]].push_back(i);//建新图
            else id[u]=i;//非割点属于第i个点双
        }
}
int main(){//注意,遇到自环要continue
    for(int i=1;i<=n;i++)
        if(!dfn[i])
            tarjan(i);
    init();
}
```



## 无向图边双

```c++
int n,m,e[M],ne[M],h[N],idx,hc[N],ec[M],nec[M],idxc;
int timestamp,dfn[N],low[N];
int stk[N],top;
bool st[M];
int cnt,id[N];
void add(int a,int b){
    e[idx]=b,ne[idx]=h[a],h[a]=idx++;
}
void add_c(int a,int b){
    ec[idx]=b,nec[idx]=hc[a],hc[a]=idx++;
}
void tarjan(int u,int from){
    dfn[u]=low[u]=++timestamp;
    stk[++top]=u;
    for(int i=h[u];~i;i=ne[i]){
        int j=e[i];
        if(!dfn[j]){
            tarjan(j,i);
            low[u]=min(low[u],low[j]);
            if(dfn[u]<low[j]){
                st[i]=st[i^1]=1;
            }
        }
        else if(i!=(from^1))low[u]=min(low[u],dfn[j]);//注意优先级问题
    }
    if(dfn[u]==low[u]){
        int tmp;
        cnt++;
        do{
            tmp=stk[top--];
            id[tmp]=cnt;
        }while(tmp!=u);
    }
}
int main(){
    for(int i=1;i<=n;i++)
        if(!dfn[i])
            tarjan(i,-1);
    for(int i=0;i<idx;i++){
        int a=e[i],b=e[i^1];
        if(id[a]!=id[b]) add_c(id[a],id[b]);
    }
}
```

