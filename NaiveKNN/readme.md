
不多说，散步去:）
```cpp
#include <bits/stdc++.h>
using namespace std;

struct vec{
    int a,b,c,d;
    bool op;
};
vec sam[12];
vec cur;

double sq(double x)
{
    return x*x;
}

double dis(vec x,vec y)
{
    double res = 0;
    res = sq(1.0*x.a-y.a)+sq(1.0*x.b-y.b)+sq(1.0*x.c-y.c)+sq(1.0*x.d-y.d);
    return sqrt(res);
}

bool cmp(vec x, vec y)
{
    return dis(cur,x)<dis(cur,y);
}

int main()
{
    sam[0] = (vec){1 ,1 ,1,0,false};
    sam[1] = (vec){-1,-1,0,1,false};
    sam[2] = (vec){1 ,0 ,1,0,false};
    sam[3] = (vec){-1,0 ,1,1,false};
    sam[4] = (vec){0 ,-1,0,1,true};
    sam[5] = (vec){1 ,-1,0,0,true};
    sam[6] = (vec){-1,0 ,0,0,true};
    sam[7] = (vec){1 ,1 ,0,1,true};
    sam[8] = (vec){-1,0 ,1,1,true};
    sam[9] = (vec){-1,1 ,0,0,true};


    scanf("%d%d%d%d",&cur.a,&cur.b,&cur.c,&cur.d);
    //1 0 1 0
    //double dst[12];
    for(int i=0;i<10;++i){
        printf("%.3f ",dis(cur,sam[i]));
    }
    puts("");
    int k;
    scanf("%d",&k);
    sort(sam,sam+10,cmp);
    int boll =0;
    for(int i=0;i<k;i++){
        if(sam[i].op){
            boll++;
        }
        else boll--;
    }
    if(boll>0)cout << "Y";
    else cout <<"N";
    return 0;
}
```