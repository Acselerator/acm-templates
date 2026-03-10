# 最小费用最大流（类Dinic）

在 Dinic 算法的基础上进行改进，把 **BFS 求分层图** 改为用 SPFA求一条单位费用之和最小的路径（由于有负权的反向边，不能直接用 Dijkstra ）。

[P3381 【模板】最小费用最大流](https://www.luogu.com.cn/problem/P3381)

```cpp
//洛谷P3381 完整代码 
#include<iostream>
#include<queue>
using namespace std;
typedef long long ll;
const int MAXN=5e3+5;
const int MAXM=5e4+5;
const int INF=0x3f3f3f3f;

struct edge{
    int to,nxt;
    int flow,cap;
}e[MAXM<<1];
int head[MAXN],tot=1;
void add(int x,int y,int w,int c){
    e[++tot].to=y;
    e[tot].flow=w;
    e[tot].cap=c;
    e[tot].nxt=head[x];
    head[x]=tot;

    e[++tot].to=x;
    e[tot].flow=0;
    e[tot].cap=-c;
    e[tot].nxt=head[y];
    head[y]=tot;
}

int n,m,s,t;
int dis[MAXN],now[MAXN];
bool vis[MAXN]; //bfs与dfs中的vis毫无关系，只是共用一块内存而已，前者的vis相当于inQ
int mxflow,mncost;

bool bfs(){ //spfa
    for(int i=1;i<=n;i++){
        dis[i]=INF;
        vis[i]=0;
    }
    dis[s]=0;
    queue<int> Q;
    Q.push(s);
    vis[s]=1;
    now[s]=head[s];
    while(!Q.empty()){
        int x=Q.front();
        vis[x]=0;
        Q.pop();
        for(int i=head[x];i;i=e[i].nxt){
            int y=e[i].to;
            if(dis[y]>dis[x]+e[i].cap&&e[i].flow){
                dis[y]=dis[x]+e[i].cap;
                now[y]=head[y];
                if(!vis[y]){
                    Q.push(y);
                    vis[y]=1;
                }
            }
        }
    }
    return dis[t]!=INF;
}

int dfs(int x,int sum){
    if(x==t) return sum;
    vis[x]=1;
    int res=0;
    for(int i=now[x];i;i=e[i].nxt){
        now[x]=i;
        int y=e[i].to;
        if(!vis[y]&&e[i].flow&&dis[y]==dis[x]+e[i].cap){
            int tmp=dfs(y,min(sum,e[i].flow));
            if(tmp>0){
                e[i].flow-=tmp;
                e[i^1].flow+=tmp;
                sum-=tmp;
                res+=tmp;
                mncost+=e[i].cap*tmp;
            }
        }
    }
    vis[x]=0;
    return res;
}

int main(){
    scanf("%d%d%d%d",&n,&m,&s,&t);
    for(int i=1;i<=m;i++){
        int u,v,w,c;
        scanf("%d%d%d%d",&u,&v,&w,&c);
        add(u,v,w,c);
    }
    while(bfs()){
        mxflow+=dfs(s,INF);
    }
    printf("%d %d\n",mxflow,mncost);
}
```