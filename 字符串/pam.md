```cpp
struct node{
    int ch[26],len,fail;
}tr[N];
struct PAM{
	int cnt,last;
	PAM(){tr[0].fail=1,tr[cnt=1].len=-1;last=0;}
	int getfail(int u,int i,char* s){
		while(i-tr[u].len-1<0||s[i-tr[u].len-1]!=s[i]) u=tr[u].fail;
		return u;
	}
	void extend(int i,char* s){//建立时要一个一个extend
		int pos=getfail(last,i,s);//找到cur的fail链中能匹配i位的最长回文后缀
		if(!tr[pos].ch[cast[s[i]]]){//不存在,就先创建一个
			tr[++cnt].fail=tr[getfail(tr[pos].fail,i,s)].ch[cast[s[i]]];//求该节点的fail
			tr[pos].ch[cast[s[i]]]=cnt;
			tr[cnt].len=tr[pos].len+2;
			//tr[cnt].num=tr[tr[cnt].fail].num+1;更新该位置结尾的回文子串个数
		}
		last=tr[pos].ch[cast[s[i]]];
		//printf("%d ",tr[last].num);以当前位置结尾的回文子串的个数  
	}
};
```

对一个字符串，分别求$1\sim i$中双倍回文串的总个数（双倍回文串即形如$abbaabba$这样的字符串）。

```cpp
struct node
{
    int ch[26], len, fail;
    int trans;
    int num;
} tr[N];
int cast[130];
struct PAM
{
    char s[N];
    int cnt, last;
    void init()
    {
        //多测清空
        memset(tr, 0, sizeof(node) * (cnt + 1));
        tr[0].fail = 1, tr[cnt = 1].len = -1;
        last = 0;
    }
    int getfail(int u, int i)
    {
        while (i - tr[u].len - 1 < 0 || s[i - tr[u].len - 1] != s[i])
            u = tr[u].fail;
        return u;
    }
    void extend(int i)
    {
        int pos = getfail(last, i);
        if (!tr[pos].ch[cast[s[i]]])
        {                                                                
            tr[++cnt].fail = tr[getfail(tr[pos].fail, i)].ch[cast[s[i]]];
            tr[pos].ch[cast[s[i]]] = cnt;
            tr[cnt].len = tr[pos].len + 2;
            if (tr[cnt].len <= 2)
                tr[cnt].trans = tr[cnt].fail;
            else
            {
                int tmp = tr[pos].trans;
                while (i - tr[tmp].len - 1 < 0 || s[i - tr[tmp].len - 1] != s[i] ||
                       ((tr[tmp].len + 2) << 1) > tr[cnt].len)
                    tmp = tr[tmp].fail;
                //trans指向不大于该字符串长度一半的border
                tr[cnt].trans = tr[tmp].ch[cast[s[i]]];
            }
            if ((tr[tr[cnt].trans].len << 1) == tr[cnt].len)
                tr[cnt].num = tr[tr[cnt].fail].num + 1;
            else
                tr[cnt].num = tr[tr[cnt].fail].num;
        }
        last = tr[pos].ch[cast[s[i]]];
        res += tr[last].num;
    }
} pam;
void solve()
{
    for (int i = 'a'; i <= 'z'; i++)
        cast[i] = i - 'a';
    scanf("%s", pam.s + 1);
    pam.init();
    res = 0;
    for (int i = 1; pam.s[i]; i++)
        pam.extend(i), cout << res << " ";
    cout << endl;
}
```

