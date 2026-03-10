# 最短路（SPFA）
[P3371 【模板】单源最短路径（弱化版）](https://www.luogu.com.cn/problem/P3371)

SPFA可以有**负权边**。

SPFA也可以判**负环**，就是一个点被松弛了n次的情况。

```cpp
//洛谷P3371完整代码
#include<iostream>
#include<queue>
using namespace std;
const int INF=(1ll<<31)-1;
const int maxn=1e4+5;
const int maxm=5e5+5;

struct edge{
    int to,nxt,val;
}e[maxm];
int head[maxn],tot;
int n,m,s;

int dis[maxn];
int vis[maxn];
bool inQ[maxn];

void add(int x,int y,int w){
    e[++tot].to=y;
    e[tot].val=w;
    e[tot].nxt=head[x];
    head[x]=tot;
}

void spfa(){
    queue<int> Q;
    Q.push(s);
    inQ[s]=1;
    while(!Q.empty()){
        int t=Q.front();
        Q.pop();
        inQ[t]=0;
        vis[t]++;
        if(vis[t]>n){
            cout<<-1<<'\n';exit(0);
        }
        for(int i=head[t];i;i=e[i].nxt){
            int v=e[i].to,w=e[i].val;
            if(dis[v]>dis[t]+w){
                dis[v]=dis[t]+w;
                if(!inQ[v]){
                    Q.push(v);
                    inQ[v]=1;
                }
            }
        }
    }
}

int main(){
    cin>>n>>m>>s;
    for(int i=1;i<=m;i++){
        int x,y,w;
        cin>>x>>y>>w;
        add(x,y,w);
    }
    for(int i=1;i<=n;i++) if(i!=s)dis[i]=INF;
    spfa();
    for(int i=1;i<=n;i++){
        cout<<dis[i]<<' ';
    }
}
```