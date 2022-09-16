求一个最小的非负整数$l$，满足$b^{l}\equiv n(mod\ p)$

```cpp
//这里枚举到了0~p-1之间的每一个数
void solve(){
    cin>>p>>b>>n;
    if(b%p==0) return cout<<"no solution"<<endl,void();
    int gap=ceil(sqrt(p));
    int now=1;
    for(int i=0;i<=gap;i++,now=now*1ll*b%p)
        val[n*1ll*now%p]=i;
    now=qmi(b,gap,p);
    for(int i=1,tmp=1;i<=gap;i++){
        tmp=tmp*1ll*now%p;
        if(val.count(tmp))
            return cout<<i*gap-val[tmp]<<endl,void();
    }
    return cout<<"no solution"<<endl,void();
}
```

