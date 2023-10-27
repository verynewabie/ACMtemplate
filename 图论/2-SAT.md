![image-20230722124443849](https://cartoonwqy.oss-cn-nanjing.aliyuncs.com/2023/07/4f33c2ab52a7497a173515bb1d2a25a1.png)

```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=2e6+10;
int e[N],ne[N],h[N],idx=1,n,m;
int dfn[N],low[N],ts,cnt,id[N],stk[N],top;
bool st[N];
void add(int a,int b){
    e[idx]=b,ne[idx]=h[a],h[a]=idx++;
}
void tarjan(int u){
    dfn[u]=low[u]=++ts;
    stk[++top]=u,st[u]=1;
    for(int i=h[u];i;i=ne[i]){
        int j=e[i];
        if(!dfn[j]){
            tarjan(j);
            low[u]=min(low[u],low[j]);
        }
        else if(st[j]) low[u]=min(low[u],dfn[j]);
    }
    if(dfn[u]==low[u]){
        int tmp;
        cnt++;
        do{
            tmp=stk[top--];
            id[tmp]=cnt;
            st[tmp]=0;
        }while(tmp!=u);
    }
}
int main(){
    scanf("%d%d",&n,&m);
    while(m--){
        int i,x,j,y;
        scanf("%d%d%d%d",&i,&x,&j,&y);
        i--,j--;
        add(2*i+!x,2*j+y);//i不为x,则j为y
        add(2*j+!y,2*i+x);
    }
    for(int i=0;i<2*n;i++)
        if(!dfn[i]) 
            tarjan(i);
    for(int i=0;i<n;i++)
        if(id[i*2]==id[i*2+1]){//如果表示两个条件的点属于同一连通分量,就寄了
            puts("IMPOSSIBLE");
            return 0;
        }
    puts("POSSIBLE");
    for(int i=0;i<n;i++)
        if(id[i*2]<id[i*2+1]) printf("0 ");//1的拓扑序更靠前,若选1则会有1->0,所以选0
        else printf("1 ");
    return 0;
}
```

