```cpp
#include<bits/stdc++.h>
using namespace std;
int a,b,mod;//a~b中各位数字之和是mod倍数的数的个数
int num[11],len;
int dp[11][2][2][100];
int dfs(int now,bool limit,bool zero,int rem){
    if(!now) return !rem;
    int& v=dp[now][limit][zero][rem];
    if(~v) return v;
    v=0;
    int up=limit?num[now]:9;
    for(int i=0;i<=up;i++)
        v+=dfs(now-1,limit&&i==num[now],zero&&i==0,(rem+i)%mod);
    return v;
}
int calc(int x){
    memset(dp,-1,sizeof dp);
    len=0;
    while(x) num[++len]=x%10,x/=10;
    return dfs(len,1,1,0);
}
signed main(){
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    while(cin>>a>>b>>mod) cout<<calc(b)-calc(a-1)<<endl;
    return 0;
}
```

