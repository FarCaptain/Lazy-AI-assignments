这个项目是一个简单的A\*算法，试图封装了一个“棋盘”的数据结构，将A\*作为其中主要功能。不怎么写这样的代码，写得比较笨拙。

优先队列的部分，之前都是复制一份node塞进去，用对应的x，y来做与图的映射。这样如果有重复出队的节点，就跳过后出队的节点。缺点是要重复出队，会变慢。

这回试着存一队列的地址，如果涉及修改，就直接修改原node的值。

代码如下
```cpp
#include <cstdlib>
#include <iostream>
#include <algorithm>
#include <vector>
#include <queue>
#include <functional>
#include <stack>
#define INF 0x7fffffff
enum
{
    inacc = -1,
    fre = 0,
    ope = 1,
    closed = 2
};
using namespace std;

struct node
{
    int x, y, h, g;
    node* fa;
    int stat;
    node()
        : x(0)
        , y(0)
        , h(0)
        , stat(fre)
        , fa(NULL)
        , g(INF)
    {
    }
    bool greaterthan(const node* pnode) const
    {
        return (h + g) > (pnode->h + pnode->g);
    }
};

class nodePointerComparer
{
public:
    nodePointerComparer()
    {
    }
    bool operator()(const node* x, const node* y) const
    {
        return x->greaterthan(y);
    }
};

class map
{
private:
    int dx[4] = { 0, 0, -1, 1 };
    int dy[4] = { 1, -1, 0, 0 };
    int len, wid;
    vector<vector<node> > mx; // f = h+g
    priority_queue<node*, vector<node*>, nodePointerComparer> open; // store f_val =>open table
    stack<node> path;
    node *st, *ed;

public:
    map(int _wid, int _len)
        : wid(_wid)
        , len(_len) //error control here
    {
        mx.resize(wid);
        for (int i = 0; i < wid; ++i)
        {
            mx[i].resize(len);
            for (int j = 0; j < len; ++j)
            {
                mx[i][j].x = i;
                mx[i][j].y = j;
            }
        }
        st = &mx[0][0];
        ed = &mx[0][0];
    }
    void init(int _x, int _y, int __stat)
    {
        node& cur = mx[_x][_y];
        cur.stat = __stat;
    }
    void updateValue() // in case for a st/ed change
    {
        for (int i = 0; i < wid; i++)
        {
            for (int j = 0; j < len; ++j)
            {
                node& cur = mx[i][j];
                cur.h = (i - ed->x) + (j - ed->y); // using manhattan dis
            }
        }
    }
    void setStart(int _sx, int _sy) //! if didn't set init return error!
    {
        st = &mx[_sx][_sy];
    }
    void setEnd(int _ex, int _ey)
    {
        ed = &mx[_ex][_ey];
    }
    bool check(int _x, int _y)
    {
        if ((_x < wid) && (_x >= 0) && (_y < len) && (_y >= 0))
            return true;
        return false;
    }
    int Astar()
    {
        int dis = 0;
        open.push(st);
        st->g = 0;
        while (!open.empty())//open store pointer of node
        {
            node* cur = open.top();
            open.pop();
            cur->stat = closed; // put into closed
            if (cur == ed)
            {
                dis = cur->g;
                break;
            }

            for (int i = 0; i < 4; ++i)
            { // extend 4directions
                int nx = cur->x + dx[i];
                int ny = cur->y + dy[i];

                if (!check(nx, ny))
                    continue;

                node* adj = &mx[nx][ny];
                int sta = adj->stat;
                if (sta == inacc)
                    continue;
                if (sta == ope)
                {
                    if (adj->g > cur->g + 1)
                    {
                        adj->g = cur->g + 1;
                        cout << "g=" << adj->g << endl;
                        adj->fa = cur; // update father node
                    }
                    continue;
                }
                if (sta == fre)
                {
                    adj->g = cur->g + 1;
                    adj->fa = cur;
                    open.push(adj);
                }
            }
        }
        return dis;
    }
    void FindPath()
    {
        // cout << ed->fa << endl;
        node* tmp = ed;
        while (tmp != NULL)
        {
            path.push(*tmp);
            tmp = tmp->fa;
        }
        while (path.size())
        {
            node cur = path.top();
            path.pop();
            cout << "(" << cur.x << "," << cur.y << ")" << endl;
        }
    }
};

int main()
{
    int m, n;
    cin >> m >> n;
    map mp(m, n);

    for (int i = 0; i < m; i++)
    {
        for (int j = 0; j < n; ++j)
        {
            int tmp;
            cin >> tmp;
            if (tmp)
                mp.init(i, j, inacc);
        }
    }
    cin >> m >> n;
    // 7 4
    mp.setStart(m, n);
    cin >> m >> n;
    mp.setEnd(m, n);
    mp.updateValue();
    cout << "here!" << endl;
    int dis = mp.Astar();
    cout << dis << endl;
    mp.FindPath();
    // system("pause");
    return 0;
}
/*
3 3
0 1 1
0 1 0
0 0 0
0 0
2 2

10 8
0 0 0 0 0 0 0 0
0 0 0 0 0 0 0 0
0 0 0 0 0 0 0 0
0 0 0 0 0 0 0 0
0 0 1 1 1 1 1 0
0 1 0 0 0 0 0 0
0 0 1 0 0 0 1 0
0 0 0 0 0 0 0 0
0 0 0 0 0 0 0 0
0 0 0 0 0 0 0 0
6 3
3 2


*/
```