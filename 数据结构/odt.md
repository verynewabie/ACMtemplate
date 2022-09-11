```c++
struct node {
	int _l, _r;
	mutable int _val;//set里的值是不能改的，如果声明为mutable，可以改
	node(int l, int r = -1, int val = 0) :_l(l), _r(r), _val(val) {}
	bool operator<(const node& a)const { return _l < a._l; };
};
struct ODT {
	set<node> s;
	set<node>::iterator split(int pos) {
		auto it = s.lower_bound(node(pos));
		if (it != s.end() && it->_l == pos) {
			return it;
		}
		it--;
		//if (it->_r < pos) return s.end();这一行应该用不到,只要你插入的时候右边界多一个
		int l = it->_l;
		int r = it->_r;
		int v = it->_val;
		s.erase(it);
		s.insert(node(l, pos - 1, v));
		//insert函数返回pair，其中的first是新插入结点的迭代器
		return s.insert(node(pos, r, v)).first;
	}
	void assign(int l, int r, int x) {
		auto itr = split(r + 1), itl = split(l);
		s.erase(itl, itr);
		s.insert(node(l, r, x));
	}
};
```

