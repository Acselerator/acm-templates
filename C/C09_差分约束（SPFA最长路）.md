# 差分约束（SPFA最长路）   

[P5960 【模板】差分约束](https://www.luogu.com.cn/problem/P5960)

**差分约束系统**

如果一个不等式组由$n$个变量和$m$个约束条件组成，形成$m$个形如$x_j−x_i≤k$（$i$,$j$∈[$1$,$n$]且$k$为常数）的不等式，则称其为差分约束系统。换句话说，差分约束系统就是求解一组变量的不等式组的算法。

对于$x_j−x_i≤k$

1. 从$i$到$j$连边权为$k$的边，加入超级源点后求最短路
2. 从$j$到$i$连边权为$-k$的边，加入超级源点后求最长路

```cpp
//P5960完整代码 SPFA最长路写法
#include<iostream>
#include<queue>
using namespace std;
typedef long long ll;
const int maxn=5e3+5;
const int INF=(1ll<<31)-1;

struct edge{
    int to,nxt,val;
}e[maxn<<1]; //注意从超级源点也要建边，数组开够
int head[maxn],tot;

int n,m;
bool inQ[maxn];
int dis[maxn];
int vis[maxn];

void add(int u,int v,int w){
    e[++tot].to=v;
    e[tot].val=w;
    e[tot].nxt=head[u];
    head[u]=tot;
}

bool spfa(int x){ //SPFA求最长路
    for(int i=1;i<=n;i++){
        dis[i]=-INF;
    }dis[x]=0;
    queue<int> Q;
    Q.push(x);
    inQ[x]=1;
    while(!Q.empty()){
        x=Q.front();
        Q.pop();
        inQ[x]=0;
        vis[x]++;
        if(vis[x]>n) return 0; //判正环
        for(int i=head[x];i;i=e[i].nxt){
            int y=e[i].to,w=e[i].val;
            if(dis[y]<dis[x]+w){
                dis[y]=dis[x]+w;
                if(!inQ[y]) Q.push(y);
            }
        }
    }
    return 1;
}

int main(){
    scanf("%d %d",&n,&m);
    for(int i=1;i<=n;i++){
        add(0,i,0);
    }
    for(int i=1;i<=m;i++){
        int u,v,w;
        scanf("%d %d %d",&u,&v,&w);
        add(u,v,-w);
    }
    if(spfa(0)){
        for(int i=1;i<=n;i++){
            printf("%d ",dis[i]);
        }
    }else printf("NO\n");
}
```