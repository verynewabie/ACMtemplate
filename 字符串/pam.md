```cpp
struct node{
    int ch[26],len,fail;
    int trans;
}tr[N];
struct pam{
	int cnt,last;
	pam(){tr[0].fail=1,tr[cnt=1].len=-1;last=0;}
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
            /*求trans,即小于等于当前节点长度一半的最长回文后缀
			if(tr[cnt].len<=2) tr[cnt].trans=tr[cnt].fail;
			else{
				int tmp=tr[pos].trans;
				while(i-tr[tmp].len-1<0||s[i-tr[tmp].len-1]!=s[i]||
					((tr[tmp].len+2)<<1)>tr[cnt].len) tmp=tr[tmp].fail;
				//注意拓展后的长度为len[tmp]+2
				tr[cnt].trans=tr[tmp].ch[cast[s[i]]];
			}
			*/
		}
		last=tr[pos].ch[cast[s[i]]];
		//printf("%d ",tr[last].num);以当前位置结尾的回文子串的个数  
	}
};
```

