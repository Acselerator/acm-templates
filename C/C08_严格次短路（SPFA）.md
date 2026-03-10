# 严格次短路（SPFA）
[P2865 [USACO06NOV] Roadblocks G](https://www.luogu.com.cn/problem/P2865)

严格次短路

$d[i][0]$ 到$i$点的最短路

$d[i][1]$ 到$i$点的次短路

松弛操作有三种情况：

1. $d[u][0]$影响$d[v][0]$
2. $d[u][0]$影响$d[v][1]$
3. $d[u][1]$影响$d[v][1]$


```cpp
//洛谷P2865完整代码
#include<iostream>
#include<cstring>
#include<queue>
using namespace std;
const int maxn=5e3+5;
const int maxm=1e5+5;

struct edge{
    int to,nxt,val;
}e[maxm<<1];
int head[maxn],tot;
int d[maxn][2]; //d[i][0]和d[i][1]分别表示最短路和次短路
bool vis[maxn];
int n,r;
int A,B,D;

void add(int u,int v,int w){
    e[++tot].to=v;
    e[tot].val=w;
    e[tot].nxt=head[u];
    head[u]=tot;
}
void spfa(){
    queue<int> q;
    q.push(n);
    vis[n]=1;
    while(!q.empty()){
        int u=q.front();
        vis[u]=0;
        q.pop();
        for(int i=head[u];i;i=e[i].nxt){
            int v=e[i].to,w=e[i].val;
            if(d[v][0]>d[u][0]+w){ //d[u][0]影响v点最短路
                d[v][1]=d[v][0];
                d[v][0]=d[u][0]+w;
                if(!vis[v]) vis[v]=1,q.push(v);
            }
            if(d[v][0]<d[u][0]+w&&d[v][1]>d[u][0]+w){ //d[u][0]影响v点次短路
                d[v][1]=d[u][0]+w;
                if(!vis[v]) vis[v]=1,q.push(v);
            }
            if(d[v][1]>d[u][1]+w){ //d[u][1]影响v点次短路
                d[v][1]=d[u][1]+w;
                if(!vis[v]) vis[v]=1,q.push(v);
            }
        }
    }
}

int main(){
    cin>>n>>r;
    for(int i=1;i<=r;i++){
        cin>>A>>B>>D;
        add(A,B,D);
        add(B,A,D);
    }
    memset(d,0x7f,sizeof(d)); //初始化
    d[n][0]=0;
    spfa();
    cout<<d[1][1]<<'\n';
}
```