```cpp
#define x first
#define y second
using ll=long long;
using pii=pair<int,int>;
using pll=pair<ll,ll>;
pii mod={1610612741,1e9+7},base={127,131};
pll has[N],b[N];
char s[N];
pii gethash(int l,int r){
    pii res;
    res.x=(has[r].x-has[l-1].x*b[r-l+1].x%mod.x+mod.x)%mod.x;
    res.y=(has[r].y-has[l-1].y*b[r-l+1].y%mod.y+mod.y)%mod.y;
    return res;
}
int main(){
    b[0]={1,1};
    cin>>s+1;
    for(int i=1;i<=n;i++){
        has[i].x=(has[i-1].x*base.x+s[i])%mod.x;
        has[i].y=(has[i-1].y*base.y+s[i])%mod.y;
        b[i].x=b[i-1].x*base.x%mod.x;
        b[i].y=b[i-1].y*base.y%mod.y;
    }
}
```

