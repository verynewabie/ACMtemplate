## 动态插入型

```c++
const double gap=0.75;
#define ls tr[u].l
#define rs tr[u].r
struct point{
	int x,y;
}pos[N];
struct node{
	int l,r;//l是左儿子,r是右儿子
	point p;//当前点
	int x0,y0,x1,y1;//左下角点,右上角点
	int sz;
}tr[N];
int n,idx,root,buf[N],tot,compare;//compare为0比较x,否则比较y
//buf是可用的节点,tot是可用的节点数
bool cmp(const point& a,const point& b){
	if(compare==0) return a.x<b.x||a.x==b.x&&a.y<b.y;
	return a.y<b.y||a.y==b.y&&a.x<b.x;
}
int getnode(){
	if(!tot) return ++idx;
	return buf[tot--];
}
void pushup(int u){
	tr[u].sz=tr[ls].sz+tr[rs].sz+1;
	tr[u].x0=min(tr[u].p.x,min(tr[ls].x0,tr[rs].x0));
	tr[u].y0=min(tr[u].p.y,min(tr[ls].y0,tr[rs].y0));
	tr[u].x1=max(tr[u].p.x,max(tr[ls].x1,tr[rs].x1));
	tr[u].y1=max(tr[u].p.y,max(tr[ls].y1,tr[rs].y1));
}
void getseq(int u,int cnt){
	if(ls) getseq(ls,cnt);
	buf[++tot]=u,pos[tr[ls].sz+1+cnt]=tr[u].p;
	if(rs) getseq(rs,cnt+tr[ls].sz+1);
}
int rebuild(int l,int r,int k){
	if(l>r) return 0;
	int mid=l+r>>1;
	compare=k;
	int u=getnode();
	nth_element(pos+l,pos+mid,pos+r+1,cmp);
	tr[u].p=pos[mid];
	ls=rebuild(l,mid-1,k^1),rs=rebuild(mid+1,r,k^1);
	pushup(u);
	return u;
}
void maintain(int &u,int k){
	if(tr[u].sz*gap<tr[ls].sz||tr[u].sz*gap<tr[rs].sz)
		getseq(u,0),u=rebuild(1,tot,k);	
}
void insert(int &u,const point& p,int k){
	if(!u){
		u=getnode();
		tr[u].l=tr[u].r=0;
		tr[u].p=p;
		pushup(u);
		return;
	}
	compare=k;
	if(cmp(p,tr[u].p)) insert(ls,p,k^1);
	else insert(rs,p,k^1);
	pushup(u);
	maintain(u,k);
}
void init(){tr[0].x0=INF,tr[0].y0=INF,tr[0].x1=-INF,tr[0].y1=-INF;}
//查询矩阵和
struct point{
	int x,y,w;
}pos[N];
void pushup(int u){
	tr[u].sum=tr[u].p.w+tr[ls].sum+tr[rs].sum;
	tr[u].sz=tr[ls].sz+tr[rs].sz+1;
	tr[u].x0=min(tr[u].p.x,min(tr[ls].x0,tr[rs].x0));
	tr[u].y0=min(tr[u].p.y,min(tr[ls].y0,tr[rs].y0));
	tr[u].x1=max(tr[u].p.x,max(tr[ls].x1,tr[rs].x1));
	tr[u].y1=max(tr[u].p.y,max(tr[ls].y1,tr[rs].y1));
}
bool in(node u,int x0,int y0,int x1,int y1){//某矩阵是否在该矩阵内
	return u.x0>=x0&&u.x1<=x1&&u.y0>=y0&&u.y1<=y1;	
}
bool in(point p,int x0,int y0,int x1,int y1){//某点是否在该矩阵中
	return p.x>=x0&&p.x<=x1&&p.y>=y0&&p.y<=y1;
}
bool out(node u,int x0,int y0,int x1,int y1){//某矩阵是否在该矩阵外
	return u.x0>x1||u.y0>y1||u.x1<x0||u.y1<y0;	
}
int query(int u,int x0,int y0,int x1,int y1){
	if(in(tr[u],x0,y0,x1,y1)) return tr[u].sum;
	else if(out(tr[u],x0,y0,x1,y1)) return 0;
	int res=0;
	if(in(tr[u].p,x0,y0,x1,y1)) res+=tr[u].p.w;
	res+=query(ls,x0,y0,x1,y1)+query(rs,x0,y0,x1,y1);
	return res;
}
```

## 搜索剪枝型

```cpp
//哈密顿距离
#include<bits/stdc++.h>
using namespace std;
const int N=1e6+10,INF=0x3f3f3f3f;
const double gap=0.75;
#define ls tr[u].l
#define rs tr[u].r
struct point{
	int x,y;
}pos[N];
struct node{
	int l,r;//l是左儿子,r是右儿子
	point p;//当前点
	int x0,y0,x1,y1;//左下角点,右上角点
	int sz;
}tr[N];
int n,m,idx,root,buf[N],tot,compare,res;//compare为0比较x,否则比较y
//buf是可用的节点,tot是可用的节点数
bool cmp(const point& a,const point& b){
	if(compare==0) return a.x<b.x||a.x==b.x&&a.y<b.y;
	return a.y<b.y||a.y==b.y&&a.x<b.x;
}
int dist(const point& a,const point& b){
	return abs(a.x-b.x)+abs(a.y-b.y);
}
int calc(const node& u,const point& p){//计算点到矩阵的估算距离
	int x=p.x,y=p.y;
	return max(0,u.x0-x)+max(0,u.y0-y)+max(0,x-u.x1)+max(0,y-u.y1);
}
int getnode(){
	if(!tot) return ++idx;
	return buf[tot--];
}
void pushup(int u){
	tr[u].sz=tr[ls].sz+tr[rs].sz+1;
	tr[u].x0=min(tr[u].p.x,min(tr[ls].x0,tr[rs].x0));
	tr[u].y0=min(tr[u].p.y,min(tr[ls].y0,tr[rs].y0));
	tr[u].x1=max(tr[u].p.x,max(tr[ls].x1,tr[rs].x1));
	tr[u].y1=max(tr[u].p.y,max(tr[ls].y1,tr[rs].y1));
}
void getseq(int u,int cnt){
	if(ls) getseq(ls,cnt);
	buf[++tot]=u,pos[tr[ls].sz+1+cnt]=tr[u].p;
	if(rs) getseq(rs,cnt+tr[ls].sz+1);
}
int rebuild(int l,int r,int k){
	if(l>r) return 0;
	int mid=l+r>>1;
	compare=k;
	int u=getnode();
	nth_element(pos+l,pos+mid,pos+r+1,cmp);
	tr[u].p=pos[mid];
	ls=rebuild(l,mid-1,k^1),rs=rebuild(mid+1,r,k^1);
	pushup(u);
	return u;
}
void maintain(int &u,int k){
	if(tr[u].sz*gap<tr[ls].sz||tr[u].sz*gap<tr[rs].sz)
		getseq(u,0),u=rebuild(1,tot,k);	
}
void insert(int &u,const point& p,int k){
	if(!u){
		u=getnode();
		tr[u].l=tr[u].r=0;
		tr[u].p=p;
		pushup(u);
		return;
	}
	compare=k;
	if(cmp(p,tr[u].p)) insert(ls,p,k^1);
	else insert(rs,p,k^1);
	pushup(u);
	maintain(u,k);
}
void init(){
	tr[0].x0=tr[0].y0=INF,tr[0].x1=tr[0].y1=-INF;
}
void query(int u,const point& p){
	if(!u) return ;
	res=min(res,dist(tr[u].p,p));
	int lans=INF,rans=INF;
	if(ls) lans=calc(tr[ls],p);
	if(rs) rans=calc(tr[rs],p);
	if(lans<rans){
		if(lans<res) query(ls,p);
		if(rans<res) query(rs,p);
	}
	else{
		if(rans<res) query(rs,p);
		if(lans<res) query(ls,p);
	}
}
int main(){
	scanf("%d%d",&n,&m);
	init();
	for(int i=1;i<=n;i++){
		int a,b;
		scanf("%d%d",&a,&b);
		pos[i]={a,b};
	}
	root=rebuild(1,n,0);
	while(m--){
		int op,x,y;
		scanf("%d%d%d",&op,&x,&y);
		if(op==1) insert(root,{x,y},0);
		else{
			res=INF;
			query(root,{x,y});
			printf("%d\n",res);
		}
	}
	return 0;
}
//k远对距离
#include<bits/stdc++.h>
using namespace std;
const int N=1e6+10,INF=0x3f3f3f3f;
const double gap=0.75;
#define ls tr[u].l
#define rs tr[u].r
struct point{
	int x,y;
}pos[N];
using ll=long long;
struct node{
	int l,r;//l是左儿子,r是右儿子
	point p;//当前点
	int x0,y0,x1,y1;//左下角点,右上角点
	int sz;
}tr[N];
int n,m,idx,root,buf[N],tot,compare;//compare为0比较x,否则比较y
//buf是可用的节点,tot是可用的节点数
bool cmp(const point& a,const point& b){
	if(compare==0) return a.x<b.x||a.x==b.x&&a.y<b.y;
	return a.y<b.y||a.y==b.y&&a.x<b.x;
}
ll dist(const point& a,const point& b){
	int dx=a.x-b.x,dy=a.y-b.y;
	return 1ll*dx*dx+1ll*dy*dy;
}
ll calc(const node& u,const point& p){//计算点到矩阵的估算距离
	int dx=max(p.x-u.x0,u.x1-p.x),dy=max(p.y-u.y0,u.y1-p.y);
	return 1ll*dx*dx+1ll*dy*dy;
}
int getnode(){
	if(!tot) return ++idx;
	return buf[tot--];
}
void pushup(int u){
	tr[u].sz=tr[ls].sz+tr[rs].sz+1;
	tr[u].x0=min(tr[u].p.x,min(tr[ls].x0,tr[rs].x0));
	tr[u].y0=min(tr[u].p.y,min(tr[ls].y0,tr[rs].y0));
	tr[u].x1=max(tr[u].p.x,max(tr[ls].x1,tr[rs].x1));
	tr[u].y1=max(tr[u].p.y,max(tr[ls].y1,tr[rs].y1));
}
void getseq(int u,int cnt){
	if(ls) getseq(ls,cnt);
	buf[++tot]=u,pos[tr[ls].sz+1+cnt]=tr[u].p;
	if(rs) getseq(rs,cnt+tr[ls].sz+1);
}
int rebuild(int l,int r,int k){
	if(l>r) return 0;
	int mid=l+r>>1;
	compare=k;
	int u=getnode();
	nth_element(pos+l,pos+mid,pos+r+1,cmp);
	tr[u].p=pos[mid];
	ls=rebuild(l,mid-1,k^1),rs=rebuild(mid+1,r,k^1);
	pushup(u);
	return u;
}
void maintain(int &u,int k){
	if(tr[u].sz*gap<tr[ls].sz||tr[u].sz*gap<tr[rs].sz)
		getseq(u,0),u=rebuild(1,tot,k);	
}
void insert(int &u,const point& p,int k){
	if(!u){
		u=getnode();
		tr[u].l=tr[u].r=0;
		tr[u].p=p;
		pushup(u);
		return;
	}
	compare=k;
	if(cmp(p,tr[u].p)) insert(ls,p,k^1);
	else insert(rs,p,k^1);
	pushup(u);
	maintain(u,k);
}
void init(){
	tr[0].x0=tr[0].y0=INF,tr[0].x1=tr[0].y1=-INF;
}
priority_queue<ll,vector<ll>,greater<ll>> q;
void query(int u,const point& p){
	if(!u) return ;
	ll tmpdis=dist(p,tr[u].p);
	if(tmpdis>q.top()) q.pop(),q.push(tmpdis);
	ll lans=0,rans=0;
	if(ls) lans=calc(tr[ls],p);
	if(rs) rans=calc(tr[rs],p);
	if(lans>rans){
		if(lans>q.top()) query(ls,p);
		if(rans>q.top()) query(rs,p);
	}
	else{
		if(rans>q.top()) query(rs,p);
		if(lans>q.top()) query(ls,p);
	}
}
int main(){
	scanf("%d%d",&n,&m);
	m*=2;//因为点对无序
	init();
	for(int i=1;i<=n;i++){
		int a,b;
		scanf("%d%d",&a,&b);
		pos[i]={a,b};
	}
	root=rebuild(1,n,0);
	for(int i=1;i<=m;i++) q.push(0);
	for(int i=1;i<=n;i++) query(1,pos[i]);
	printf("%lld\n",q.top());
	return 0;
}
```

