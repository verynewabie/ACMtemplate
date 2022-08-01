```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=2e6+10;
char s[N];
int len[N],n,num[N],fail[N],cur,tr[N][26],tot=1;//tot是总共用的节点个数,cur是上一次插入/停止的节点,len是每个节点的回文子串长度
int getfail(int x,int i){
	while(i-len[x]-1<0||s[i-len[x]-1]!=s[i]) x=fail[x];
	return x;
}
int main(){
	scanf("%s",s);
	n=strlen(s);
	fail[0]=1;len[1]=-1;//1是奇树的根
	for(int i=0;i<=n-1;i++){
		int pos=getfail(cur,i);//找到cur的fail链中能匹配i位的最长回文后缀
		if(!tr[pos][s[i]-'a']){//不存在,就先创建一个
			fail[++tot]=tr[getfail(fail[pos],i)][s[i]-'a'];//求该节点的fail
			tr[pos][s[i]-'a']=tot;
			len[tot]=len[pos]+2;
			num[tot]=num[fail[tot]]+1;//更新该位置结尾的回文子串个数
		}
		cur=tr[pos][s[i]-'a'];
		printf("%d ",num[cur]);//num[cur]就是以当前位置结尾的回文子串的个数   
	}
	return 0;
}
```

