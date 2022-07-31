```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=2e7+10;
char a[N],b[N];
int p[N],n;
void init(){
    int k=0;
    b[k++]='@',b[k++]='#';
    for(int i=0;i<n;i++) b[k++]=a[i],b[k++]='#';
    b[k++]='$';
    n=k;
}
void manacher(){
    int maxr=0,mid;
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

