```cpp
struct pam{
    struct node{
        int ch[26],len,fail;
    }tr[N];
    int cnt,last;
    pam(){tr[0].fail=1,tr[1].len=-1;}
    int getfail(int u,int i,char* s){
        while(i-tr[u].len-1<0||s[i-tr[u].len-1]!=s[i]) u=tr[u].fail;
        return u;
    }
    void extend(int i,char* s){//建立时要一个一个extend
        int pos=getfail(last,i,s);//找到cur的fail链中能匹配i位的最长回文后缀
        if(!tr[pos].ch[s[i]-'a']){//不存在,就先创建一个
            tr[++cnt].fail=tr[getfail(fail[pos],i)].ch[s[i]-'a'];//求该节点的fail
            tr[pos].ch[s[i]-'a']=cnt;
            tr[cnt].len=tr[pos].len+2;
            //tr[cnt].num=tr[tr[cnt].fail].num+1;更新该位置结尾的回文子串个数
        }
        last=tr[pos].ch[s[i]-'a'];
        //printf("%d ",tr[last].num);以当前位置结尾的回文子串的个数  
    }
};
```

