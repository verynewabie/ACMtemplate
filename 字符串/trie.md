## trie

```c++
//01trie
/*
	第i个测试用例时
	tr[0][0]=tr[0][1]=0;
	idx=1;
	插入时
	int u=0;
	for(int i=30;i>=0;i--){
	    int j=x>>i&1;
	    if(!tr[u][j]) tr[idx][0]=tr[idx][1]=0,tr[u][j]=idx++;
	    u=tr[u][j];
	}
*/
const int maxn=1e5+10;
int n,a[maxn*31][2],res,index;
void insert(int x){
	int p=0;
	for(int i=30;i>=0;i--){
		int tmp=x>>i&1;
		if(!a[p][tmp])  a[p][tmp]=++index;
		p=a[p][tmp];
	}
}
int query(int x){
	int p=0,res=0;
	for(int i=30;i>=0;i--){
		int tmp=x>>i&1;
		if(a[p][!tmp]){
			res+=1<<i;
			p=a[p][!tmp];
		} 
		else p=a[p][tmp];
	}
	return res;
}
//trie
const int N=1e5+10;
int t[N][26],idx=1,cnt[N];
char s[N],tmp;
void insert(){
	int p=0;
	for(int i=0;s[i];i++){
		int tmp=s[i]-'a';
		if(!t[p][tmp]) t[p][tmp]=idx++;
		p=t[p][tmp];
	}
	cnt[p]++;
}
int query(){
	int p=0;
	for(int i=0;s[i];i++){
		int tmp=s[i]-'a';
		if(!t[p][tmp]) return 0;
		p=t[p][tmp];
	}
	return cnt[p];
}
```



## 可持久化trie

```c++
const int N=1e6+10;
//多测memset会tle，我们这样搞
/*
	第i个测试用例时
	tr[0][0]=tr[0][1]=0,idx=1;
	插入时
	新开辟idx时
	tr[idx][0]=tr[idx][1]=0,tr[u][j]=idx++;
*/
struct Trie{
	int ch[N*31][2],cnt[N*31];
	int rt[N],idx=0;
	void insert(int &a,int b,int x){
		a=++idx;
		int p=a,q=b;
		cnt[p]=cnt[q]+1;
		for(int i=30;i>=0;i--){
			//先继承
			ch[p][0]=ch[q][0]; 
			ch[p][1]=ch[q][1];
			//要插入的另外开辟
			int c=x>>i&1;
			ch[p][c]=++idx;
			p=ch[p][c],q=ch[q][c];
			cnt[p]=cnt[q]+1;
		}
	}
	int query(int x,int a,int b){//a+1~b之间
		int p=rt[a],q=rt[b];
		int ans=0;
		for(int i=30;i>=0;i--){
			int c=((x>>i)&1);
			if (cnt[ch[q][c^1]]-cnt[ch[p][c^1]]>0) 
				ans|=1<<i,p=ch[p][c^1],q=ch[q][c^1];
			else p=ch[p][c],q=ch[q][c];
			// cnt 相减判断是否能走
		}
		return ans;
	}
};
```

