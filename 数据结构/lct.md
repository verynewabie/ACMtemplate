## splay版本

```c++
struct node{
    int s[2],p,v;
    int sum,rev;
}tr[N];
//lct中每个点最多与一个子节点间有实边
bool isroot(int u){//判断u是否是其所在splay的根
    return tr[tr[u].p].s[0]!=u&&tr[tr[u].p].s[1]!=u;
}
void pushup(int u){
    //维护信息
}
void pushrev(int u){
    swap(tr[u].s[0],tr[u].s[1]);
    tr[u].rev^=1;
}
void pushdown(int u){
    if (tr[u].rev){
        pushrev(tr[u].s[0]);
        pushrev(tr[u].s[1]);
        tr[u].rev=0;
    }
}
void rotate(int x){
    int y=tr[x].p,z=tr[y].p;
    int k=tr[y].s[1]==x;
    if(!isroot(y)) tr[z].s[tr[z].s[1]==y]=x;//唯一的不同处
    tr[x].p=z;
    tr[y].s[k]=tr[x].s[k^1],tr[tr[x].s[k^1]].p=y;
    tr[x].s[k^1]=y,tr[y].p=x;
    pushup(y),pushup(x);
}
void splay(int x){//把x旋转到所在splay的根
    static int stk[N];
    int tt=0,t=x;
    stk[++tt]=t;
    while(!isroot(t)) stk[++tt]=t=tr[t].p;
    while(tt) pushdown(stk[tt--]);
    while (!isroot(x)){
        int y=tr[x].p,z=tr[y].p;
        if(!isroot(y))
            if((tr[z].s[1]==y)^(tr[y].s[1]==x)) rotate(x);
            else rotate(y);
        rotate(x);
    }
}
void access(int x){//建立一条从根节点到x的实链,同时将x变成对应splay的根节点,同时这里会让x与子节点的实边变为虚边
    int z=x;//记录初始的节点编号
    for(int y=0;x;y=x,x=tr[x].p){//x沿着虚边往上找根
        splay(x);//先转到当前辅助树的根
        tr[x].s[1]=y,pushup(x);//把上个树接到中序遍历后面
    }
    splay(z);//把初始的节点转到根
}
void makeroot(int x){//将x变成原树的根节点且左子树为空
    access(x);//此时x为辅助树的根节点,直接反转中序遍历即可
    pushrev(x);
}
int findroot(int x){//找到x所在的原树的根节点,再将原树的根节点旋转到辅助树的根节点
    access(x);//打通根节点到x的实链,当前x位于辅助树的根节点位置
    while(tr[x].s[0]) pushdown(x),x=tr[x].s[0];//找到辅助树中序遍历的第一个元素
    splay(x);//转到根节点
    return x;
}
void split(int x,int y){//将x到y的路径变为实边路径,查询x到y的路径上信息时用,用完后y是根
    makeroot(x);//先把x设为根
    access(y);//在打通根到y的实链即可
}
void link(int x,int y){//若x,y不连通,则加入(x,y)这条边
    makeroot(x);//先把x设为根
    if(findroot(y)!=x) tr[x].p=y;//如果不连通,则把x的实链接到y上即可
    //findroot(y)时会删掉y与儿子间的实边
}
void cut(int x,int y){//若边x,y存在,则删掉x,y这条边
    makeroot(x);
    //y必然是x的后继
    if(findroot(y)==x&&tr[x].s[1]==y&&!tr[y].s[0]){
        tr[y].p=tr[x].s[1]=0;
        pushup(x);
    }
}
//查询区间信息
split(x,y);
printf("%d\n",tr[y].sum);
//连接(已连则忽略)
link(x,y);
//断开(未连则忽略)
cut(x,y);
//修改x节点
splay(x);
tr[x].v=y;
pushup(x);
```
