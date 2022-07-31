```c++
#define lson tr[u].s[0]
#define rson tr[u].s[1]
const int N=1e5+10;//这是节点个数
int root,idx,w[N];//root是根节点
struct node{//根据value排序
	int s[2],p;
	int val,sz;
}tr[N];
//我们经常额外插入-INF和INF当作哨兵位，更方便
void pushup(int u){
	tr[u].sz=tr[lson].sz+tr[rson].sz+1;
}
void pushdown(int u){
	//只有存在该儿子时才pushdown
}
//rotate和splay一般不用pushdown，因为get_k和insert里面pushdown过了
void rotate(int x){
	int y=tr[x].p,z=tr[y].p;
	tr[z].s[y==tr[z].s[1]]=x,tr[x].p=z;
	int k=x==tr[y].s[1];
	tr[y].s[k]=tr[x].s[k^1],tr[tr[x].s[k^1]].p=y;
	tr[x].s[k^1]=y,tr[y].p=x;
	pushup(y),pushup(x);
}
void splay(int x,int k){
	while(tr[x].p!=k){
		int y=tr[x].p,z=tr[y].p;
		if(z!=k){
			if((tr[z].s[1]==y)^(tr[y].s[1]==x)) rotate(x);
			else rotate(y);
		}
		rotate(x);
	}   
	if(k==0) root=x;
}
int newnode(int val,int p){
	int u=++idx;
	tr[u].p=p;
	tr[u].val=val;
}
int insert(int val){
	int u=root,p=0;
	while(u){
		pushdown(u);
		p=u;
		u=tr[u].s[val>tr[u].val];
	}
	u=newnode(val,p);
	if(p) tr[p].s[val>tr[p].val]=u;
	splay(u,0);
	return u;
}
int get(int val){//找到大于等于某个数的第一个数
	int u=root,res;
	while(u){
		pushdown(u);
		if(tr[u].val>=val) res=u,u=lson;
		else u=rson;
	}
	return res;
}
int get_kth(int k){//找到第k小的数对应节点编号
	int u=root;
	while(u){
		pushdown(u);
		if(tr[lson].sz>=k) u=lson;
		else if(tr[lson].sz+1==k) return u;
		else k-=tr[lson].sz+1,u=rson;
	}
	return -1;
}
int build(int l,int r,int p){
	int mid=l+r>>1;
	int u=newnode(w[mid],p);
	if(l<mid) lson=build(l,mid-1,u);
	if(mid<r) rson=build(mid+1,r,u);
	pushup(u);
	return u;
}
void del(int& root,int x){
	int u=root;
	while(u){
		if(tr[u].val==x) break;
		if(tr[u].val<x) u=tr[u].s[1];
		else u=tr[u].s[0];
	}
	splay(u,0);
	int l=tr[u].s[0],r=tr[u].s[1];
	while(tr[l].s[1]) l=tr[l].s[1];
	while(tr[r].s[0]) r=tr[r].s[0];
	splay(l,0),splay(r,l);
	tr[r].s[0]=0;
	pushup(r),pushup(l);
}
```

