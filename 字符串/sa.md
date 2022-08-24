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
char s[N];
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
```

