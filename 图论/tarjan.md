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
                st[i]=st[i^1]=1;//标记桥
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

## 圆方树

```cpp
#include <bits/stdc++.h>
using namespace std;
const int N = 1e6 + 10;

int n, m, cnt;
vector<int> to[N], tree[N * 2]; // 原图，圆方树

int dfn[N], low[N], dfc;
int stk[N], top;

void tarjan(int u)
{
    low[u] = dfn[u] = ++dfc; // low 初始化为当前节点 dfn
    stk[++top] = u;          // 加入栈中
    for (auto v : to[u])
    { // 遍历 u 的相邻节点
        if (!dfn[v])
        {                                 // 如果未访问过
            tarjan(v);                    // 递归
            low[u] = min(low[u], low[v]); // 未访问的和 low 取 min
            if (low[v] == dfn[u])
            {          // 标志着找到一个以 u 为根的点双连通分量
                ++cnt; // 增加方点个数
                // 将点双中除了 u 的点退栈，并在圆方树中连边
                for (int x = 0; x != v; --top)
                {
                    x = stk[top];
                    tree[cnt].push_back(x);
                    tree[x].push_back(cnt);
                }
                // 注意 u 自身也要连边（但不退栈）
                tree[cnt].push_back(u);
                tree[u].push_back(cnt);
            }
        }
        else
            low[u] = min(low[u], dfn[v]); // 已访问的和 dfn 取 min
    }
}

int main()
{
    scanf("%d%d", &n, &m);
    cnt = n; // 点双 / 方点标号从 N 开始
    for (int i = 1; i <= m; ++i)
    {
        int u, v;
        scanf("%d%d", &u, &v);
        to[u].push_back(v); // 加双向边
        to[v].push_back(u);
    }
    // 处理非连通图
    for (int u = 1; u <= n; ++u)
        if (!dfn[u])
            tarjan(u), --top;
    // 注意到退出 Tarjan 时栈中还有一个元素即根，将其退栈
    return 0;
}
```

