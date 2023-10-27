![image-20230801220737205](https://cartoonwqy.oss-cn-nanjing.aliyuncs.com/2023/08/798735a730e0a8483d19ed9f7ff0401a.png)

```c++
// 下标从0开始,z[i]表示从i开始到末尾的子串与原串的lcp,z[0]=0
for (int i = 1, l = 0, r = 0; i < n; ++i) {
    if (z[i - l] < r - i + 1)
        z[i] = z[i - l];
    else {
        z[i] = max(r - i + 1, 0);
        while (i + z[i] < n && s[z[i]] == s[i + z[i]])
            z[i]++;
        l = i, r = i + z[i] - 1;
    }
}
```

![image-20230312153955070](https://cartoonwqy.oss-cn-nanjing.aliyuncs.com/boke/202303121539147.png)

## KMP自动机

```cpp
int ne[N],pre[N],stk[N],top;
void add(int x){
	int j=top;
	while(j&&stk[ne[j]+1]!=x) j=pre[j];
	stk[++top]=x,j=ne[j]+1;
	if(top==1) ne[1]=pre[1]=0;
	else if(stk[j]==x){
		ne[top]=j;
		if(stk[ne[j]+1]==stk[j+1]) pre[top]=pre[j];
		else pre[top]=j;
	}
	else ne[top]=pre[top]=0;
}
void del(){
	top--;
}
```

## KMP

```cpp
char s[N],t[N];
cin>>n>>m;
cin>>s+1>>t+1;
for(int i=2,j=0;i<=m;i++){
    while(j&&t[i]!=t[j+1]) j=ne[j];
    if(t[i]==t[j+1]) j++;
    ne[i]=j;
}
for(int i=1,j=0;i<=n;i++){
    while(j&&s[i]!=t[j+1]) j=ne[j];
    if(s[i]==t[j+1]) j++;
    if(j==m){
        j=ne[j];
    }
}
```

border树中$sz[i]$就代表长度为$i$的border在整个字符串中出现的次数。

