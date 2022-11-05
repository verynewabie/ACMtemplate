时间复杂度$O(\frac{n^{\frac{3}{4}}}{log\ n})$

```cpp
#include <bits/stdc++.h>
using namespace std;
//空间开2sqrt(n)
const int N=2e5+10,mod=1e9+7;
using ll=long long;
ll n;
//函数f需要是积性函数,并且f(p)可以拆分成若干多项式
//id1[i]是通过n/x的取值找到对应的v中位置,id2[i]是通过n/(n/x)找到
int id1[N],id2[N],m;
int p[N],vis[N],cnt;
ll g[N],v[N];//v[i]存储的是n/x的所有取值,求完的g[i]表示1~v[i]的质数的f的前缀和,g[get(i)]即可得到当前1~i前缀和
//g(n,i)表示1~n内所有质数和最小质因子大于p[i]的数的f的和
int get(ll x){
	return x<N?id1[x]:id2[n/x];
}
ll get_sum(ll x){//1~x的f的前缀和,按拆分后的完全积性函数计算
	
}
ll F(ll x){//返回x的f值,这里不用拆开算
    
}
void init(int tot){
    for(int i=2;i<=tot;i++){
        if(!vis[i]) p[++cnt]=i;
        for(int j=1;p[j]<=tot/i;j++){
            vis[i*p[j]]=1;
            if(i%p[j]==0) break;
        }
    }
}
//s(n,i)表示1~n中所有最小质因子大于p[i]的数的f的和
ll S(ll x,int y){
    if(p[y]>=x) return 0;
    //s(n,i)=g(n)-g(p[i])+枚举
    ll res=(g[get(x)]-g[get(p[y])]+mod)%mod;//把拆开的合起来
    for(int i=y+1;i<=cnt&&p[i]<=x/p[i];i++){//枚举最小质因子
        ll w=p[i];
        for(int j=1;w<=x/p[i];j++,w=w*p[i])//次数
            res=(res+F(w)*S(x/w,i)%mod+F(w*p[i]))%mod;
    }
    return res;
}
int main(){
    scanf("%lld",&n),init(sqrt(n)+1);//筛出sqrt(n)以内的质数
    for(ll l=1,r;l<=n;l=r+1){//初始时,g[i](即g(v[i],0))计算1~v[i]的前缀和
        r=n/(n/l),v[++m]=n/l;//v[i]中i越大,v[i]越小
        if(v[m]<N) id1[v[m]]=m;
        else id2[n/v[m]]=m;
        //这里减去1,表示不考虑1的贡献,其它函数中也不算1,最后再加上
        g[m]=(get_sum(v[m])-1+mod)%mod;
    }
    //g(n,j)=g(n,j-1)-f(p[j])( g(n/p[j],j-1)-g(p[j-1],j-1) )
    for(int j=1;j<=cnt;j++){
        for(int i=1;i<=m&&p[j]<=v[i]/p[j];i++){
            //p[j]代表的是f(p)的值
            g[i]=(g[i]-p[j]*(g[get(v[i]/p[j])]-g[get(p[j-1])])%mod+mod)%mod;
        }
    }
    printf("%lld\n",(S(n,0)+1)%mod);
    return 0;
}
```

