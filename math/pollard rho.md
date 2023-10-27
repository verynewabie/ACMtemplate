```cpp
LL mul(LL a, LL b, LL p) {
    LL res = a * b - LL(1.L * a * b / p) * p;
    res %= p;
    if (res < 0) {
        res += p;
    }
    return res;
}
LL qpow(LL a, LL k, LL mod) {
    LL ans = 1;
    while (k) {
        if (k & 1) ans = mul(ans,a,mod);
        k >>= 1;
        a = mul(a,a,mod);
    }
    return ans;
}
const int pri[]={2,3,5,7,11,13,17,19,23,29,31,37};
bool mr(LL n)
{
    if(n==1) return false;
    for(int i=0;i<12;i++) if(n%pri[i]==0) return n==pri[i];
    LL res=n-1;int k=0;
    while(!(res&1)) res>>=1,++k;
    for(int i=0;i<12;i++)
    {
        LL x=qpow(pri[i],res,n);
        for(int j=0;j<k&&x>1;j++)
        {
            LL y=mul(x,x,n);
            if(y==1&&x!=n-1) return false;
            x=y;
        }
        if(x!=1) return false;
    }
    return true;
}
LL Find(LL n)
{
    for(int i=0;i<12;i++) if(n%pri[i]==0) return pri[i];
    LL x,y=rand(),c=rand();
    int w=1<<9;
    for(int l=1;;l<<=1)
    {
        x=y;
        for(int i=0;i<l;i++) y=(mul(y,y,n)+c)%n;
        for(int i=0;i<l;i+=w)
        {
            int le=min(w,l-i);
            LL g=1,las=y;
            for(int j=0;j<le;j++) y=(mul(y,y,n)+c)%n,g=mul(((unsigned long long)y+n-x)%n,g,n);
            g=__gcd(g,n);
            if(g==1) continue;
            if(g==n)
            {
                g=1,y=las;
                while(g==1) y=(mul(y,y,n)+c)%n,g=__gcd((LL)(((unsigned long long)y+n-x)%n),n);
            }
            return g;
        }
    }
}
LL rho(LL n)
{
    LL now=Find(n);
    while(now==n) now=Find(n);
    return now;
}
LL dfs(LL src){
    if(mr(src)) return src;
    return dfs(rho(src));
}
//rho就是给出随意一个非平凡因子(就是除了自己和1的因子)
//mr就是质数判断
```

$dfs$复杂度$O(n^{\frac{1}{4}}\ log\ n)$

$mr$中进行$k$轮的复杂度$O(k\ log^{3}n)$
