```c++
int tot=1,last=1;//tot是总点数,last是上一个插入的点,空节点编号为1
struct Node{//每个节点
    int len,fa;//len表示该状态的最长子串的长度,fa是它绿边连向的父亲
    int ch[26];
}node[N];//N的大小为二倍的字符串长度,node[1].len=node[1].fa=0
char s[N];
int f[N];//f[i]代表i这个状态的endpos的集合大小
int h[N],e[M],ne[M],idx;//M大小为字符串长度的三倍,求f才用
void add(int a,int b){//求f才用
	e[idx]=b,ne[idx]=h[a],h[a]=idx++;
}
void extend(int c){//这个函数我是真的不懂,反正从前往后依次插入每个字符即可
    int p=last,np=last=++tot;
    f[tot]=1;//tot节点就是该字符串的一个前缀
    node[np].len=node[p].len+1;
    for(;p&&!node[p].ch[c];p=node[p].fa) node[p].ch[c]=np;
    if(!p) node[np].fa=1;
    else{
        int q=node[p].ch[c];
        if(node[q].len==node[p].len+1) node[np].fa=q;
        else{
            int nq=++tot;
            node[nq]=node[q],node[nq].len=node[p].len+1;
            node[q].fa=node[np].fa=nq;
            for(;p&&node[p].ch[c]==q;p=node[p].fa) node[p].ch[c]=nq;
        }
    }
}
void dfs(int u){//求f才用
    for(int i=h[u];~i;i=ne[i]){
        dfs(e[i]);
        f[u]+=f[e[i]];
    }
}
int main(){
    memset(h,-1,sizeof h);
    scanf("%s",s);
    for(int i=0;s[i];i++) extend(s[i]-'a');
    for(int i=2;i<=tot;i++) add(node[i].fa,i);
    dfs(1);//求f
    return 0;
}
//不同子串的个数
//ans[i]即代表从i点开始走,不包括i点自己的子串数
ll dfs(int x){//dfs(1)后ans[1]即为答案
	if(~ans[x]) return ans[x];
    ans[x]=0;
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
```

