![image-20220927212943888](https://cartoonwqy.oss-cn-nanjing.aliyuncs.com/boke/image-20220927212943888.png)

![image-20220927230125920](https://cartoonwqy.oss-cn-nanjing.aliyuncs.com/boke/image-20220927230125920.png)

```cpp
//min_25模板题
#include<bits/stdc++.h>
#define x first
#define y second
#define endl '\n'
#define pb push_back
#define mp make_pair
#define all(x) (x).begin(),(x).end()
#define lowbit(x) ((x)&-(x))
#define SZ(x) (int)x.size()
using namespace std;
using ll=long long;
using ld=long double;
using pii=pair<int,int>;
using pll=pair<ll,ll>;
using pdd=pair<ld,ld>;
const int INF=0x3f3f3f3f,mod=1e9+7,N=1e7+10,inv2=mod+1>>1,inv6=166666668;
ll n;
ll v[N],res;
unordered_map<ll,ll> sum_g;
bool st[N];
int p[N],cnt,gap;
vector<ll> h[10000];
int f_g_sum(ll x){
    x%=mod;
    return x*(x*2+1)%mod*(x+1)%mod*inv6%mod;
}
int g_sum(ll x){
    x%=mod;
    return x*(x+1)%mod*inv2%mod;
}
ll qmi(ll a,ll b){
    ll res=1;
    a%=mod;
    while(b){
        if(b&1) res=a*res%mod;
        b>>=1;
        a=a*a%mod;
    }
    return res;
}
ll GetSum(ll n) {
    if(n<=gap) return v[n];
    if(sum_g.count(n)) return sum_g[n];
    ll ans = f_g_sum(n);
    for(ll l = 2, r; l <= n; l = r + 1) {
        r = (n / (n / l)); 
        ans -= (g_sum(r) - g_sum(l - 1)) * GetSum(n / l);
        ans = (ans % mod + mod) % mod;
    }
    return sum_g[n]=ans; 
}
void euler(int tot=gap){
    v[1]=1;
    for(int i=2;i<=tot;i++){
        if(!st[i]){  
            p[cnt++]=i;
            v[i]=i-1;
        }
        for(int j=0;p[j]<=tot/i;j++){
            int t=p[j]*i;
            st[t]=true;
            if(i%p[j]==0){
                v[t]=v[i]*p[j];
                break;
            }
            v[t]=v[i]*(p[j]-1);
        }
    }
    for(int i=1;i<=tot;i++) v[i]=v[i]*i%mod,v[i]=(v[i]+v[i-1])%mod;
}
void dfs(int pos,ll now,ll h_val){
    if(pos==cnt){
        res=(res+h_val*GetSum(n/now))%mod;
        return ;
    }
    ll str=1ll*p[pos]*p[pos];
    if(str<=n/now){
        dfs(pos+1,now,h_val);
        int k=2;
        while(str<=n/now){
            dfs(pos+1,str*now,h_val*h[pos][k]%mod);
            k++;
            str*=p[pos];
        }
    }
    else{
        res=(res+h_val*GetSum(n/now))%mod;
    }
}
void solve(){
    cin>>n;
    gap=pow(n,2/3.0);
    euler();
    for(int i=0;i<cnt&&p[i]<=1e5;i++){
        ll now=p[i]*p[i];
        h[i].push_back(1);
        h[i].push_back(0);
        for(int j=2;now<=n;j++,now*=p[i]){
            ll ans=qmi(p[i],j)*(qmi(p[i],j)-1+mod)%mod;
            ll invp=qmi(p[i],mod-2);
            ll invp2=invp*invp%mod;
            ll str=(p[i]-1)*qmi(p[i],2*j-1)%mod;
            for(int k=0;k<j;k++,str=str*invp2%mod){
                ans=(ans-str*h[i][k])%mod;
            }
            ans=(ans+mod)%mod;
            h[i].push_back(ans);
        }
    }
    dfs(0,1,1);
    cout<<res<<endl;
}
signed main(){
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    solve();
    return 0;
}
```

