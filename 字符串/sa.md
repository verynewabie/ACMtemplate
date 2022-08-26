```c++
int n,m;
char s[N];
namespace SA{
	int sa[N],x[N],y[N],c[N],rk[N],height[N];
	void get_sa(){
		for(int i=1;i<=n;i++) c[x[i]=s[i]]++;
		for(int i=2;i<=m;i++) c[i]+=c[i-1];
		for(int i=n;i;i--) sa[c[x[i]]--]=i;
		for(int k=1;k<=n;k<<=1){
			int num=0;
			for(int i=n-k+1;i<=n;i++) y[++num]=i;
			for(int i=1;i<=n;i++)
				if(sa[i]>k)
					y[++num]=sa[i]-k;
			for(int i=1;i<=m;i++) c[i]=0;
			for(int i=1;i<=n;i++) c[x[i]]++;
			for(int i=2;i<=m;i++) c[i]+=c[i-1];
			for(int i=n;i;i--) sa[c[x[y[i]]]--]=y[i],y[i]=0;
			swap(x,y);
			x[sa[1]]=1,num=1;
			for(int i=2;i<=n;i++)
				x[sa[i]]=(y[sa[i]]==y[sa[i-1]]&&y[sa[i]+k]==y[sa[i-1]+k])?num:++num;
			if(num==n) break;
			m=num;
		}
	}
	void get_height(){
		for(int i=1;i<=n;i++) rk[sa[i]]=i;
		for(int i=1,k=0;i<=n;i++){
			if(rk[i]==1) continue;
			if(k) k--;
			int j=sa[rk[i]-1];
			while(i+k<=n&&j+k<=n&&s[i+k]==s[j+k]) k++;
			height[rk[i]]=k;
		}
	}
}
int main(){
	scanf("%s",s+1);
	n=strlen(s+1),m='z';
	SA::get_sa();
	SA::get_height();
	for(int i=1;i<=n;i++) cout<<SA::sa[i]<<" \n"[i==n];
	return 0;
}

//另一种写法
int n,m;
char s[N];//字符不要从0开始
namespace SA{
	int sa[N],rk[N],c[N],height[N],x[N],y[N];
	//sa:排名对应编号
	//rk:编号对应排名
	//c:桶
	//height:排名为i的后缀与它前一名的lcp
	//x:编号对应排名
	//y:排名对应编号
	void sort(){//x为第一关键字,y为第二关键字排序
		for(int i=1;i<=m;i++) c[i]=0;
		for(int i=1;i<=n;i++) c[x[i]]++;
		for(int i=1;i<=m;i++) c[i]+=c[i-1];
		for(int i=n;i;i--) sa[c[x[y[i]]]--]=y[i];
	}
	bool check(int i,int k){
		return y[sa[i-1]]==y[sa[i]]&&y[sa[i-1]+k]==y[sa[i]+k];
	}
	void get_sa(){
		for(int i=1;i<=n;i++) x[i]=s[i],y[i]=i;
		sort();
		for(int k=1,num=0;num<n;k<<=1,m=num){
			num=0;
			//2k长度的第二关键字排序
			for(int i=1;i<=k;i++) y[++num]=n-k+i;
			for(int i=1;i<=n;i++)
				if(sa[i]>k)
					y[++num]=sa[i]-k;
			//上一轮的x就是这一轮的第一关键字
			sort();
			swap(x,y);
			//后面的x是2k长度的排序
			x[sa[1]]=num=1;
			for(int i=2;i<=n;i++) x[sa[i]]=check(i,k)?num:++num;
		}
	}
	void get_height(){
		for(int i=1;i<=n;i++) rk[sa[i]]=i;
        //设h[i]为height[rk[i]],即编号为i的后缀与它前一名的lcp,h[i]>=h[i-1]-1;
		for(int i=1,k=0;i<=n;i++){
			if(rk[i]==1) continue;
			if(k) k--;
			int j=sa[rk[i]-1];
			while(i+k<=n&&j+k<=n&&s[i+k]==s[j+k]) k++;
			height[rk[i]]=k;
		}
	}
}
//树上后缀排序,每个点的字符串为它到根的路径上的,相同字符串编号小的在前
#include<bits/stdc++.h>
using namespace std;
const int N=5e5+10;
int n,m,fa[N][21];
char s[N];
namespace SA{
	int sa[N],rk[N],c[N],x[N],y[N],rk_p[N],z[N];
	//sa:排名对应编号
	//rk:编号对应排名
	//c:桶
	//x:编号对应排名
	//y:排名对应编号
	//sz:第一关键字范围
	void sort(int* x,int* y,int* sa,int sz){//x为第一关键字,y为第二关键字排序
		for(int i=0;i<=sz;i++) c[i]=0;
		for(int i=1;i<=n;i++) c[x[i]]++;
		for(int i=1;i<=sz;i++) c[i]+=c[i-1];
		for(int i=n;i;i--) sa[c[x[y[i]]]--]=y[i];
	}
	bool check(int i,int k){
		return y[sa[i-1]]==y[sa[i]]&&y[fa[sa[i-1]][k]]==y[fa[sa[i]][k]];
	}
	void get_sa(){
		int num=0;
		for(int i=1;i<=n;i++) z[i]=s[i],y[i]=i;
		sort(z,y,sa,m);//先按第一个字母和顺序排
		x[sa[1]]=rk[sa[1]]=num=1;
		for(int i=2;i<=n;i++){
			x[sa[i]]=z[sa[i-1]]==z[sa[i]]?num:++num;
			rk[sa[i]]=i;
		}
		for(int k=1,p=0;k<n;k<<=1,p++){
			for(int i=1;i<=n;i++) rk_p[i]=rk[fa[i][p]];//第一关键字的顺序
			sort(rk_p,sa,y,n);//父亲排名为第一关键字,自己排名为第二关键字
			sort(x,y,sa,num);//若两个字符串相同,按父亲排名,否则按自己排名
			swap(x,y);
			x[sa[1]]=rk[sa[1]]=num=1;
			for(int i=2;i<=n;i++){
				x[sa[i]]=check(i,p)?num:++num;
				rk[sa[i]]=i;
			}
		}
	}
}
int main(){
	scanf("%d",&n);
	for(int i=2;i<=n;i++){
		scanf("%d",&fa[i][0]);
		for(int k=1;k<21;k++)
			fa[i][k]=fa[fa[i][k-1]][k-1];
	}
	scanf("%s",s+1);
	m='z';
	SA::get_sa();
	for(int i=1;i<=n;i++) cout<<SA::sa[i]<<" \n"[i==n];
	return 0;
}
//结构体写法
struct SA{
	int sa[N],rk[N],c[N],height[N],x[N],y[N];
	int mi[N][M],n,m;
	char s[N];//字符不要从0开始
	//sa:排名对应编号
	//rk:编号对应排名
	//c:桶
	//height:排名为i的后缀与它前一名的lcp
	//x:编号对应排名
	//y:排名对应编号
	void init(){//最多只会越界1个位置
		x[n+1]=y[n+1]=0;
	}
	void sort(){//x为第一关键字,y为第二关键字排序
		for(int i=1;i<=m;i++) c[i]=0;
		for(int i=1;i<=n;i++) c[x[i]]++;
		for(int i=1;i<=m;i++) c[i]+=c[i-1];
		for(int i=n;i;i--) sa[c[x[y[i]]]--]=y[i];
	}
	bool check(int i,int k){//这里可能会越界,因此多测要清空x和y数组
		return y[sa[i-1]]==y[sa[i]]&&y[sa[i-1]+k]==y[sa[i]+k];
	}
	void get_sa(){
		for(int i=1;i<=n;i++) x[i]=s[i],y[i]=i;
		sort();
		for(int k=1,num=0;num<n;k<<=1,m=num){
			num=0;
			//2k长度的第二关键字排序
			for(int i=1;i<=k;i++) y[++num]=n-k+i;
			for(int i=1;i<=n;i++)
				if(sa[i]>k)
					y[++num]=sa[i]-k;
			//上一轮的x就是这一轮的第一关键字
			sort();
			swap(x,y);
			//后面的x是2k长度的排序
			x[sa[1]]=num=1;
			for(int i=2;i<=n;i++) x[sa[i]]=check(i,k)?num:++num;
		}
	}
	void get_height(){
		for(int i=1;i<=n;i++) rk[sa[i]]=i;
		//设h[i]为height[rk[i]],即编号为i的后缀与它前一名的lcp,h[i]>=h[i-1]-1;
		for(int i=1,k=0;i<=n;i++){
			if(rk[i]==1) continue;
			if(k) k--;
			int j=sa[rk[i]-1];
			while(i+k<=n&&j+k<=n&&s[i+k]==s[j+k]) k++;
			height[rk[i]]=k;
		}
	}
	void buildST(){
		for(int i=1;i<=n;i++) mi[i][0]=height[i];
		for(int i=1;i<16;i++)
			for(int j=1;j+(1<<i)-1<=n;j++)
				mi[j][i]=min(mi[j][i-1],mi[j+(1<<i-1)][i-1]);
	}
	int query(int l,int r){//查询l开头的后缀和r开头的后缀的lcp
		int a=rk[l],b=rk[r];
		l=min(a,b)+1;
		r=max(a,b);
		int len=log_2[r-l+1];
		return min(mi[l][len],mi[r-(1<<len)+1][len]);
	}
};
```

