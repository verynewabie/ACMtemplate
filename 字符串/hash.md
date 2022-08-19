## 字符串哈希

```cpp
pii mod={1e9+9,1e9+7},base={127,131};//1610612741
pii operator+(pii a,pii b){
    return {(a.x+b.x)%mod.x,(a.y+b.y)%mod.y};
}
pii operator-(pii a,pii b){
    return {(a.x+mod.x-b.x)%mod.x,(a.y+mod.y-b.y)%mod.y};
}
pii operator*(pii a,pii b){
    return {1ll*a.x*b.x%mod.x,1ll*a.y*b.y%mod.y};
}
pii gethash(int l,int r){
    return has[r]-has[l-1]*b[r-l+1];
}
void init(int tot){
    b[0]={1,1};
    for(int i=1;i<=tot;i++){
        has[i]=has[i-1]*base+make_pair(s[i],s[i]);
        b[i]=b[i-1]*base;
    }
}
```

## unordered_map重载

```cpp
struct hashfunc{
    template<typename T,typename U>
    size_t operator()(const pair<T,U> &a)const{
        return hash<T>()(a.first) ^ hash<U>()(a.second);//这里的哈希方法是分别调用第一个参数的哈希函数和第二个参数的哈希函数做异或
    }
};
unordered_map<pair<int,int>,int,hashfunc> cast;
```



## 线段树维护哈希

```cpp
pii quehash(int u,int l,int r){
	if(tr[u].l>=l&&tr[u].r<=r) return tr[u].has;
	int mid=tr[u].l+tr[u].r>>1;
	if(r<=mid) return quehash(u<<1,l,r);
	else if(l>mid) return quehash(u<<1|1,l,r);
	else return quehash(u<<1,l,r)*b[min(r,tr[u].r)-mid]+quehash(u<<1|1,l,r);//这种写法简便
}
```

