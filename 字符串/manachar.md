```cpp
//本质不同回文串至多n个，只会出现在最右串右移的时候
#include<bits/stdc++.h>
using namespace std;
const int N=2e7+10;
char a[N],b[N];
int p[N],n;//p[i]是i位置的最长回文半径
void init(){
    int k=0;
    b[k++]='@',b[k++]='#';
    for(int i=0;i<n;i++) b[k++]=a[i],b[k++]='#';
    b[k++]='$';
    n=k;
}
void manacher(){
    int maxr=0,mid;//maxr是当前所有回文串的最右端,mid是该回文串的中心
    for(int i=1;i<n;i++){
        if(i<maxr) p[i]=min(p[mid*2-i],maxr-i+1);
        else p[i]=1;
        while(b[i-p[i]]==b[i+p[i]]) p[i]++;
        if(i+p[i]-1>maxr){
            maxr=i+p[i]-1;
            mid=i;
        }
    }
}
int main(){
    scanf("%s",a);
    n=strlen(a);
    init();
    manacher();
    int res=0;
    for(int i=0;i<n;i++) res=max(res,p[i]);
    cout<<res-1<<endl;
    return 0;
}
```

