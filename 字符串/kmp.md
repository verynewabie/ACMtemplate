```c++
#include<cstring>
#include<algorithm>
using namespace std;
int ne[N],ext[N];//下标从0开始,ne[i]表示模式串以i开头的后缀与模式串的LCP
//ext[i]表示以i开头的文本串的后缀与模式串的LCP
void exkmp(char *c){
	int len=strlen(c);
	int p=0,k=1,l;//我们会在后面先逐位比较出 nxt[1] 的值，这里先设 k 为 1
	//如果 k = 0，p 就会锁定在 |c| 不会被更改，无法达成算法优化的效果啦
	ne[0]=len; //以 c[0] 开始的后缀就是 c 本身，最长公共前缀自然为 |c|
	while(p+1<len&&c[p]==c[p+1]) p++;
	ne[1]=p; //先逐位比较出 nxt[1] 的值
	for(int i=2;i<len;i++){
		p=k+ne[k]-1; //定义
		l=ne[i-k]; //定义
		if(i+l<=p) ne[i]=l; //如果灰方框小于初始的绿方框,直接确定 nxt[i] 的值
		else{
			int j=max(0,p-i+1);
			while(i+j<len&&c[i+j]==c[j]) j++; //否则进行逐位比较
			ne[i]=j;
			k=i; //此时的 x + nxt[x] - 1 一定刷新了最大值，于是我们要重新赋值 k
		}
	}
}
void exkmp(char *a, char *b){
	int la=strlen(a),lb=strlen(b);
	int p=0,k=0,l;
	while(p<la&&p<lb&&a[p]==b[p]) p++; //先算出初值用于递推
	ext[0]=p;
	for(int i=1;i<la;i++){
		p=k+ext[k]-1;
		l=ne[i-k];
		if(i+l<=p) ext[i]=l;
		else{
			int j=max(0,p-i+1);
			while(i+j<la&&j<lb&&a[i+j]==b[j]) j++;
			ext[i]=j;
			k=i;
		}
	}
}
```

