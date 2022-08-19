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
	或者
	for(int i=0;i<=idx;i++) tr[i][0]=tr[i][1]=0;
	idx=0;
*/
int n,tr[N*31][2],idx;
void insert(int x){
	int u=0;
	for(int i=30;i>=0;i--){
		int tmp=x>>i&1;
		if(!tr[u][tmp])  tr[u][tmp]=++idx;
		u=tr[u][tmp];
	}
}
int query(int x){
	int u=0,res=0;
	for(int i=30;i>=0;i--){
		int tmp=x>>i&1;
		if(tr[u][!tmp]){
			res+=1<<i;
			u=tr[u][!tmp];
		} 
		else u=tr[u][tmp];
	}
	return res;
}
//trie
const int N=1e5+10;
int tr[N][26],idx=1,cnt[N];
char s[N],tmp;
void insert(){
	int u=0;
	for(int i=0;s[i];i++){
		int tmp=s[i]-'a';
		if(!tr[u][tmp]) tr[u][tmp]=idx++;
		u=tr[u][tmp];
	}
	cnt[u]++;
}
int query(){
	int u=0;
	for(int i=0;s[i];i++){
		int tmp=s[i]-'a';
		if(!tr[u][tmp]) return 0;
		u=tr[u][tmp];
	}
	return cnt[u];
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

