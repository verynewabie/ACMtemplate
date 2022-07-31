```c++
#include<bits/stdc++.h>
#define ls tr[u].l 
#define rs tr[u].r 
using namespace std;
mt19937 rnd(random_device{}());
const int N=1e5+10;
int idx,root;
struct node{
	int l,r,sz,val,key;
}tr[N];
void pushup(int u){
	tr[u].sz=tr[ls].sz+tr[rs].sz+1;
}
int new_node(int v){
	idx++;
	tr[idx]={0,0,1,v,rnd()};
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
int kth(int u,int k){//查找第k小
	while(1){
		if(k<=tr[ls].sz) u=tr[u].l;
		else if(k==tr[ls].sz+1) return u;
		else k-=tr[ls].sz+1,u=tr[u].r;
	}
}
void insert(int a){
	int x,y;
	splitbyval(root,a,x,y);
	root=merge(merge(x,new_node(a)),y);
}
void del(int a){//只删除一个
	int x,y,z;
	splitbyval(root,a,x,z);
	splitbyval(x,a-1,x,y);
	y=merge(tr[y].l,tr[y].r);
	root=merge(merge(x,y),z);
}
/*
删除所有值为a的
void del(int a){
	int x,y,z;
	splitbyval(root,a,x,z);
	splitbyval(x,a-1,x,y);
	root=merge(x,z);
}
*/
int main(){
	int x,y,z;
}
```

