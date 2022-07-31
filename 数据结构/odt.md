```c++
struct node{
	LL _l,_r;
	LL _val;//set里的值是不能改的，如果声明为mutable，可以改
	node(LL l,LL r=0,LL val=0):_l(l),_r(r),_val(val){}
	bool operator<(const node&a)const{return _l<a._l;};
};
struct ODT{
	set<node> s;
	set<node>::iterator split(LL pos) {
		auto it=s.lower_bound(node(pos));
		if (it!=s.end()&&it->_l==pos){
			return it;
		}
		it--;
		if(it->_r<pos) return s.end();
		LL l=it->_l;
		LL r=it->_r;
		LL v=it->_val;
		s.erase(it);
		s.insert(node(l,pos-1,v));
		//insert函数返回pair，其中的first是新插入结点的迭代器
		return s.insert(node(pos,r,v)).first;
	}
	void assign(LL l, LL r, LL x) {
		set<node>::iterator itr=split(r+1),itl=split(l);
		s.erase(itl,itr);
		s.insert(node(l, r, x));
	}
};
```

