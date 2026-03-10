## 最短路计数（Dijkstra）
[P1144 最短路计数](https://www.luogu.com.cn/training/208#problems)
```cpp
//洛谷P1144完整代码 SPFA思路类似
#include<iostream>
#include<cstring>
#include<queue>
using namespace std;
const int maxn=1e6+5;
const int maxm=2e6+5;
const int mod=100003;

struct edge{
    int to,nxt;
}e[maxm<<1];
struct node{
    int dis,id;
    bool operator <(const node&x)const{
        return x.dis<dis;
    } 
};
int head[maxn],tot;
int n,m;
int dis[maxn],ans[maxn];
bool vis[maxn];
void add(int u,int v){
    e[++tot].to=v;
    e[tot].nxt=head[u];
    head[u]=tot;
}
void dj(){
    priority_queue<node> q;
    q.push((node){0,1});
    while(!q.empty()){
        int u=q.top().id;
        q.pop();
        if(vis[u]) continue;
        vis[u]=1;
        for(int i=head[u];i;i=e[i].nxt){
            int v=e[i].to;
            if(!vis[v]&&dis[v]>dis[u]+1){
                dis[v]=dis[u]+1;
                ans[v]=ans[u]; //核心：计数操作
                q.push((node){dis[v],v});
            }else if(dis[v]==dis[u]+1){ //核心：计数操作
                ans[v]+=ans[u];
                ans[v]%=mod;
            }
        }
    }
}

int main(){
    cin>>n>>m;
    for(int i=1;i<=m;i++){
        int x,y;
        cin>>x>>y;
        if(x==y) continue;
        add(x,y);
        add(y,x);
    }
    memset(dis,0x7f,sizeof(dis));
    dis[1]=0;ans[1]=1;
    dj();

    for(int i=1;i<=n;i++) cout<<ans[i]<<'\n';
}
```