```cpp
const int mod1=1610612741,mod2=1e9+7,base1=131,base2=127;
ll hash1[N],hash2[N],b1[N],b2[N];
pii gethash(int l,int r){
    return {(hash1[r]-hash1[l-1]*b1[r-l+1]%mod1+mod1)%mod1,(hash2[r]-hash2[l-1]*b2[r-l+1]%mod2+mod2)%mod2};
} 
b1[0]=b2[0]=1;
for(int i=1;i<=n;i++){
    hash1[i]=(hash1[i-1]*base1+s[i])%mod1;
    hash2[i]=(hash2[i-1]*base2+s[i])%mod2;
    b1[i]=b1[i-1]*base1%mod1;
    b2[i]=b2[i-1]*base2%mod2;
}
```

