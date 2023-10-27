```c++
int tot=1,last=1;//tot是总点数,last是上一个插入的点,空节点编号为1
struct Node{//每个节点
    int len,fa;//len表示该状态的最长子串的长度,fa是它绿边连向的父亲
    int ch[26];
    //firstpos代表endpos里最小的一个
    //int firstpos;
}node[N];//N的大小为二倍的字符串长度,node[1].len=node[1].fa=0,转移数不会超过3倍的字符串长度
char s[N];
int f[N];//f[i]代表i这个状态的endpos的集合大小
int h[N],e[M],ne[M],idx;//转移数不会超过3倍的字符串长度
void add(int a,int b){
	e[++idx]=b,ne[idx]=h[a],h[a]=idx;
}
void extend(int c){
    int p=last,np=last=++tot;
    f[tot]=1;//tot节点就是该字符串的一个前缀
    node[np].len=node[p].len+1;
    //node[np].firstpos=node[np].len-1;//下标从0开始
    //node[np].firstpos=node[np].len;//下标从1开始
    for(;p&&!node[p].ch[c];p=node[p].fa) node[p].ch[c]=np;//从p开始跳fa,并向新点连边直到原本边就存在
    if(!p) node[np].fa=1;//路上所有点都不存在向c的边,那么把新点的fa置为1
    else{
        int q=node[p].ch[c];//否则找到走到的状态
        if(node[q].len==node[p].len+1) node[np].fa=q;//看p与q的len的差值
        else{
            int nq=++tot;//否则新建一个
            node[nq]=node[q],node[nq].len=node[p].len+1;
            node[q].fa=node[np].fa=nq;
            for(;p&&node[p].ch[c]==q;p=node[p].fa) node[p].ch[c]=nq;//把链上的边都指向nq
        }
    }
}
void dfs(int u){
    for(int i=h[u];i;i=ne[i]){
        dfs(e[i]);
        f[u]+=f[e[i]];
    }
}
int main(){
    scanf("%s",s);
    for(int i=0;s[i];i++){
        extend(s[i]-'a');
    }
    for(int i=2;i<=tot;i++) add(node[i].fa,i);
    dfs(1);
    return 0;
}
//不同子串的个数
//ans[i]即以它能到达的点为结尾的子串数
ll ans[N];
bool vis[N];
ll dfs(int x){//dfs(1)后ans[1]即为答案
    if(vis[x]) return ans[x];
    vis[x]=1;
    for(int i=0;i<26;i++) if(node[x].ch[i]) ans[x]+=dfs(node[x].ch[i])+1;
    return ans[x];
}

//每个子串出现的次数
//即该子串所在状态的endpos的集合大小

//求两个串的最长公共子串
scanf("%s",s);
for(int i=0;s[i];i++) extend(s[i]-'a');//首先对一个串建立后缀自动机
//然后让第二个串在后缀自动机上跑
int p=1,t=0,ans=0;//p是第二个串当前所在的状态,t是当前匹配的字符串的长度
scanf("%s",s);
for(int i=0;s[i];i++){
    int c=s[i]-'a';
    while(p>1&&!node[p].ch[c]) p=node[p].fa,t=node[p].len;//若当前节点无法走到下一个状态,则跳到绿边所指位置,因为只有它有可能可以走到以s[i]字母为后缀的位置,类似于kmp的时间复杂度分析,该算法也是线性的
    if(node[p].ch[c]) p=node[p].ch[c],t++;
    else p=1,t=0;
    ans=max(ans,t);
}
//求经过每一个点的子串个数(子串相同但位置不同的也算)
ll ans[N];//初始时ans[i]=f[i],ans[1]=f[1]=0
bool vis[N];
ll dfscnt(int u){
	if(vis[u]) return ans[u];
	vis[u]=1;
	for(int i=0;i<26;i++)
		if(node[u].ch[i])
			ans[u]+=dfscnt(node[u].ch[i]);
	return ans[u];
}
//两个前缀的最长公共后缀就是这两点fail树上的lca(两点相同的情况特殊)
//要找所有终止状态,只需加完字符串后从last跳fa即可
//排完之后就是fail树的bfs序/自动机的拓扑序
for(int i=1;i<=node;i++) cnt[node[i].len]++;
for(int i=1;i<=node;i++) cnt[i]+=cnt[i-1];
for(int i=1;i<=node;i++) q[cnt[node[i].len]--]=i;
//若要求endpos集合,extends[i]之后tr[last].endpos.insert(i),然后dfs fail树的同时线段树合并即可
```

