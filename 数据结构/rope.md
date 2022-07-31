```c++
#include <iostream>
#include <ext/rope>

using namespace std;
using namespace __gnu_cxx;

const int N = 2000010;

int n, m, Q, cur;
char t[N];
rope<char> s;

int main()
{
    cin >> Q;
    string op;
    while (Q -- )
    {
        cin >> op;
        if (op == "Move")
        {
            cin >> n;
            cur = n;
        }
        else if (op == "Insert")
        {
            cin >> m;
            int i = 0, k = m;
            while (k)
            {
                t[i] = getchar();
                if (t[i] >= 32 && t[i] <= 126)
                {
                    i ++ , k -- ;
                }
            }
            t[i] = '\0';
            s.insert(cur, t);
        }
        else if (op == "Delete")
        {
            cin >> n;
            s.erase(cur, n);
        }
        else if (op == "Get")
        {
            cin >> n;
            cout << s.substr(cur, n) << endl;
        }
        else if (op == "Prev") cur -- ;
        else if (op == "Next") cur ++ ;
    }
    return 0;
}
```

