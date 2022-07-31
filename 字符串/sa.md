```c++
int n,m;
char s[N];
int sa[N],x[N],y[N],c[N],rk[N],height[N];//sa[i]是i排名对应的编号,rk[i]是编号对应排名,height[i]是排名为i的与i-1的lcp,x[i]是编号对应排名,y[i]是排名对应编号
void get_sa(){
    for(int i=1;i<=n;i++) c[x[i]=s[i]]++;
    for(int i=2;i<=m;i++) c[i]+=c[i-1];
    for(int i=n;i;i--) sa[c[x[i]]--]=i;
    for(int k=1;k<=n;k<<=1){
        int num=0;
        for(int i=n-k+1;i<=n;i++) y[++num]=i;
        for(int i=1;i<=n;i++)
            if(sa[i]>k)
                y[++num]=sa[i]-k;
        for(int i=1;i<=m;i++) c[i]=0;
        for(int i=1;i<=n;i++) c[x[i]]++;
        for(int i=2;i<=m;i++) c[i]+=c[i-1];
        for(int i=n;i;i--) sa[c[x[y[i]]]--]=y[i],y[i]=0;
        swap(x,y);
        x[sa[1]]=1,num=1;
        for(int i=2;i<=n;i++)
            x[sa[i]]=(y[sa[i]]==y[sa[i-1]]&&y[sa[i]+k]==y[sa[i-1]+k])?num:++num;
        if(num==n) break;
        m=num;
    }
}
void get_height(){
    for(int i=1;i<=n;i++) rk[sa[i]]=i;
    for(int i=1,k=0;i<=n;i++){
        if(rk[i]==1) continue;
        if(k) k--;
        int j=sa[rk[i]-1];
        while(i+k<=n&&j+k<=n&&s[i+k]==s[j+k]) k++;
        height[rk[i]]=k;
    }
}
int main(){
    scanf("%s",s+1);
    n=strlen(s+1),m='z';
    get_sa();
    get_height();
    return 0;
}
//计算不同子串个数
LL ans=0;//记录答案
for(int i=1;i<=n;i++){//根据排名遍历
    int id=sa[i];//找到对应编号
    int len=n-id+1;//计算出长度
    ans+=len-height[i];//统计答案
}
//求两个串的LCS
scanf("%s",s+1);
int len=strlen(s+1);
scanf("%s",s+len+1);
n=strlen(s+1),m='z';
get_sa();
get_height();
int ans=0;
for(int i=2;i<=n;++i)//只用看排名相邻的就可以了，因为排名离得远的话不会变大
    if(height[i]>ans&&((sa[i]<=len&&sa[i-1]>len)||(sa[i]>len&&sa[i-1]<=len)))//判断是否是两个字符串的
        ans=height[i];
```

