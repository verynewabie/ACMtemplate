## 平衡树

```c++
#define ls tr[u].l 
#define rs tr[u].r 
mt19937 rnd(random_device{}());
int idx,rt;
struct node{
	int l,r,sz,val,key;
}tr[N];
void pushup(int u){
	tr[u].sz=tr[ls].sz+tr[rs].sz+1;
}
int new_node(int v){
	idx++;
	tr[idx]={0,0,1,v,(int)rnd()};
	return idx;
}
int merge(int a,int b){
	if(!a||!b) return a+b;
	if(tr[a].key<tr[b].key){
		tr[a].r=merge(tr[a].r,b);
		pushup(a);
		return a;
	}
	else{
		tr[b].l=merge(a,tr[b].l);
		pushup(b);
		return b;
	}
}
void splitbyval(int u,int k,int &x,int &y){//分割后，x是左树的根，y是右树的根，左树都小于等于k，右树都大于k
	if(!u) x=y=0;
	else{
		if(tr[u].val<=k) x=u,splitbyval(tr[u].r,k,tr[u].r,y);
		else y=u,splitbyval(tr[u].l,k,x,tr[u].l);
		pushup(u);
	}
}
void splitbysize(int u,int k,int &x,int &y){//分割后，x是左树的根，y是右树的根，左树大小为k
	if(!u) x=y=0;
	else{
		if(k<=tr[ls].sz) y=u,splitbysize(tr[u].l,k,x,tr[u].l);
		else x=u,splitbysize(tr[u].r,k-tr[ls].sz-1,tr[u].r,y);
		pushup(u);
	}
}
int kth(int u,int k){//查找第k小的地址
	while(1){
		if(k<=tr[ls].sz) u=tr[u].l;
		else if(k==tr[ls].sz+1) return u;
		else k-=tr[ls].sz+1,u=tr[u].r;
	}
}
void insert(int a){
	int x,y;
	splitbyval(rt,a,x,y);
	rt=merge(merge(x,new_node(a)),y);
}
void del(int a){//只删除一个
	int x,y,z;
	splitbyval(rt,a,x,z);
	splitbyval(x,a-1,x,y);
	y=merge(tr[y].l,tr[y].r);
	rt=merge(merge(x,y),z);
}
int query(int num){//查询num是第几小
	int x,y;
	splitbyval(rt,num-1,x,y);
	int ans=tr[x].sz+1;
	rt=merge(x,y);
	return ans;
}
int queryprev(int num){//查询num前驱(不等于num)的地址
	int x,y;
	splitbyval(rt,num-1,x,y);
	int ans=kth(x,tr[x].sz);
	rt=merge(x,y);
	return ans;
}
int querynext(int num){//后继
	int x,y;
	splitbyval(rt,num,x,y);
	int ans=kth(y,1);
	rt=merge(x,y);
	return ans;
}
```

## 文艺平衡树

```cpp
#include<bits/stdc++.h>
#define ls tr[u].l 
#define rs tr[u].r 
using namespace std;
mt19937 rnd(random_device{}());
const int N=1e5+10;
int idx,rt,n,m;
struct node{
	int l,r,sz,val,key;
	bool lazy;
}tr[N];
void calc(int u){
	tr[u].lazy^=1;
	swap(ls,rs);
}
void pushup(int u){
	tr[u].sz=tr[ls].sz+tr[rs].sz+1;
}
void pushdown(int u){
	if(tr[u].lazy){
		if(ls) calc(ls);
		if(rs) calc(rs);
		tr[u].lazy=0;
	}
}
int new_node(int v){
	idx++;
	tr[idx]={0,0,1,v,(int)rnd()};
	return idx;
}
int merge(int a,int b){
	if(!a||!b) return a+b;
	pushdown(a),pushdown(b);//下传懒标记,否则就合并错了
	if(tr[a].key<tr[b].key){
		tr[a].r=merge(tr[a].r,b);
		pushup(a);
		return a;
	}
	else{
		tr[b].l=merge(a,tr[b].l);
		pushup(b);
		return b;
	}
}
void splitbysize(int u,int k,int &x,int &y){//分割后，x是左树的根，y是右树的根，左树大小为k
	if(!u) x=y=0;
	else{
		pushdown(u);//下传懒标记,否则分割会出错
		if(k<=tr[ls].sz) y=u,splitbysize(tr[u].l,k,x,tr[u].l);
		else x=u,splitbysize(tr[u].r,k-tr[ls].sz-1,tr[u].r,y);
		pushup(u);
	}
}
int build(int l,int r){
	if(l>r) return 0;
	int mid=l+r>>1;
	int u=new_node(mid);
	ls=build(l,mid-1);
	rs=build(mid+1,r);
	pushup(u);
	return u;
}
void dfs(int u){
	pushdown(u);
	if(ls) dfs(ls);
	cout<<tr[u].val<<" ";
	if(rs) dfs(rs);
}
int main(){
	cin>>n>>m;
	rt=build(1,n);
	while(m--){
		int a,b;
		cin>>a>>b;
		int w,x,y,z;
		splitbysize(rt,b,w,x);
		splitbysize(w,a-1,y,z);
		calc(z);
		rt=merge(merge(y,z),x);
	}
	dfs(rt);
	return 0;
}
```

