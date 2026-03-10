# 差分约束（Bellman_Ford）   

[P5960 【模板】差分约束](https://www.luogu.com.cn/problem/P5960)

因为图的最短路径不包含负环或正环，故显然最多只能包含$n−1$条边。若经过$n−1$轮松弛操作后仍能松弛，说明图存在负权回路。

```cpp
//P5960完整代码 Bellman_Ford写法
#include<iostream>
using namespace std;
typedef long long ll;
const int maxn=5e3+5;
const int INF=1e9; //题目要求答案中的数据在 int 范围内！！！
//若INF太大会导致输出数据超出 int 而错误
struct edge{
    int u,v,w;
}e[maxn];
int tot;

int n,m;
int dis[maxn];

void add(int u,int v,int w){
    e[++tot].u=u;
    e[tot].v=v;
    e[tot].w=w;
}

int main(){
    scanf("%d %d",&n,&m);
    for(int i=1;i<=m;i++){
        int u,v,w;
        scanf("%d %d %d",&u,&v,&w);
        add(v,u,w);
    }
    for(int i=1;i<=n;i++){
        dis[i]=INF;
    }dis[1]=0;
    for(int i=1;i<n;i++){
        for(int j=1;j<=m;j++){
            int u=e[j].u,v=e[j].v,w=e[j].w;
            dis[v]=min(dis[v],dis[u]+w);
        }
    }
    for(int j=1;j<=m;j++){
        int u=e[j].u,v=e[j].v,w=e[j].w;
        if(dis[v]>dis[u]+w){
            printf("NO\n");
            return 0;
        }
    }
    for(int i=1;i<=n;i++){
        printf("%d ",dis[i]);
    }
}
```