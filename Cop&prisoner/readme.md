这个项目是为了自动生成一个可视化的输入文件，从而自动画出一个搜索树，因为这就是个用完就扔的小程序，代码比较粗糙哈，见谅。
为了实现我的目的，我找到了一个可视化的小工具，可以将缩进好的文本转化为思维导图。正好符合搜索树的结构。
这个项目是开源的，见：
[text2mindmap][1]
powered by @TobLoef

appreciation！
代码如下：
```cpp
#include <bits/stdc++.h>
using namespace std;
struct nod{
    int crs;
    bool ab;//0->here 1->there
    int dep;
    int hc,hp;
    int tc,tp;
    bool operator < (const nod& x) const
    {
       if(hc==x.hc){
         if(hp==x.hp){
            if(tc==x.tc){
                if(tp==x.tp)
                    return crs < x.crs;
                else return tp<x.tp;
            }
            else return tc<x.tc;
         }
        else return hp < x.hp;
       }
       else return hc < x.hc;
    }
};
int dx[5] = {-1,-2,0,0,-1};
int dy[5] = {0,0,-1,-2,-1};
queue<nod> que;
map<nod,int> mp;

void show(nod x)
{
    for(int i=1;i<x.dep;++i){
        printf("\t");
    }
    printf(" ");
    printf("[(%d,%d)(%d,%d)%d]",x.hc,x.hp,x.tc,x.tp,x.crs);
    if(!x.ab)printf("X");
    printf("\n");
}

bool if_show(nod x)
{
    if(x.hc<0||x.hp<0||x.tc<0||x.tp<0)return false;
    return true;
}
bool check(nod x)
{
    if(x.hc!=0&&x.hc<x.hp){return false;}
    if(x.tc!=0&&x.tc<x.tp){return false;}
    if(mp[x]){return false;}
    return true;
}

bool is_end(nod x)
{
    //0,0,3,3,1
    if(x.hc==0&&x.hp==0&&x.tc==3&&x.tp==3&&x.crs==1)return true;
    return false;
}

void dfs(nod cur)
{
    if(if_show(cur)){
        //cout <<"here"<<endl;
        cur.ab = check(cur);
        show(cur);
    }
    else return;
    if(cur.ab==false)return;
    if(is_end(cur))return;
    mp[cur] = 1;
    nod tmp;

    if(cur.crs)
        for(int i=0;i<5;i++){
            nod tmp;
            tmp.crs = 0;
            tmp.dep = cur.dep+1;
            tmp.hc = cur.hc-dx[i];
            tmp.hp = cur.hp-dy[i];
            tmp.tc = cur.tc+dx[i];
            tmp.tp = cur.tp+dy[i];
            dfs(tmp);
        }
    else
        for(int i=0;i<5;i++){
            nod tmp;
            tmp.crs = 1;
            tmp.dep = cur.dep+1;
            tmp.hc = cur.hc+dx[i];
            tmp.hp = cur.hp+dy[i];
            tmp.tc = cur.tc-dx[i];
            tmp.tp = cur.tp-dy[i];
            dfs(tmp);
        }
}

int main()
{
    freopen("input.txt","w",stdout);
    nod st;
    st.crs=0, st.hc=3, st.hp=3, st.tc=0, st.tp=0, st.dep=1, st.ab=1;
    dfs(st);
    return 0;
}
```


  [1]: https://github.com/TobLoef/text2mindmap
