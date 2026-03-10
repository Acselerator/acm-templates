# 最小费用最大流（SSP）

**SSP**（Successive Shortest Path）算法：在最大流的 **EK 算法** 求解最大流的基础上，把 **用 BFS 求解任意增广路** 改为 **用 SPFA 求解单位费用之和最小的增广路** 即可。

相当于 把费用作为边权，在残存网络上求最短路 。

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
    ll flow,cap;
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
int dis[MAXN],pre[MAXN]; //与EK算法有所区别，dis[i]表示到 i 点为止的路径的最小单位费用之和
bool inQ[MAXN];
ll mxflow,mncap;

bool bfs(){ //spfa
    for(int i=1;i<=n;i++){
        dis[i]=INF;
        inQ[i]=0;
    }
    dis[s]=0;
    queue<int> Q;
    Q.push(s);
    inQ[s]=1;
    while(!Q.empty()){
        int x=Q.front();
        inQ[x]=0;
        Q.pop();
        for(int i=head[x];i;i=e[i].nxt){
            int y=e[i].to;
            // if(dis[y]!=INF) continue;
            // 上一行代码是错误的，因为bfs过程是说spfa松弛操作，允许对已访问节点进行多次松弛，以找到更优路径
            if(dis[y]>dis[x]+e[i].cap&&e[i].flow){
                dis[y]=dis[x]+e[i].cap;
                pre[y]=i;
                if(!inQ[y]){
                    Q.push(y);
                    inQ[y]=1;
                }
            }
        }
    }
    return dis[t]!=INF;
}

void update(){
    ll x=t,mn=INF; //mn:路径最“窄”处的流量
    while(x!=s){
        mn=min(mn,e[pre[x]].flow);
        x=e[pre[x]^1].to;
    }
    x=t;
    while(x!=s){
        e[pre[x]].flow-=mn;
        e[pre[x]^1].flow+=mn;
        x=e[pre[x]^1].to;
    }
    mxflow+=mn;
    mncap+=mn*dis[t]; //dis[i]也就是到 i 点为止的路径上各边的单位费用加和
}

int main(){
    scanf("%d%d%d%d",&n,&m,&s,&t);
    for(int i=1;i<=m;i++){
        int u,v,w,c;
        scanf("%d%d%d%d",&u,&v,&w,&c);
        add(u,v,w,c);
    }
    while(bfs()){
        update();
    }
    printf("%lld %lld\n",mxflow,mncap);
}
```