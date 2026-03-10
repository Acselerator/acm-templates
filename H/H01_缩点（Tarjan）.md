# 缩点（Tarjan）

[P3387 【模板】缩点](https://www.luogu.com.cn/problem/P3387)

**缩点**：将有向有环图中的环缩成一个个点，形成一张有向无环图。

**强连通分量**：顶点两两相互可达的极大子图。孤立的一个点也是一个强连通分量。

**Tarjan:**

tarjan的过程就是dfs过程。

三个数组：

- stac（栈，从深搜根节点到当前点路径上的点）

- dfn（时间戳，首次被搜索到的时间）

- low（能追溯到的dfn最早的栈中节点的次序号）

深搜到的目标点已被遍历过时：
- 若目标点不在栈中：该边为横向边。
- 横叉边一定不产生环。
- 若目标点在栈中：该边为后向边。
- 环一定由**后向边**产生。



```cpp
//洛谷P3387完整代码 
#include<iostream>
#include<queue>
using namespace std;
const int maxn=1e4+5;
const int maxm=1e5+5;

struct edge{
    int frm,to,nxt;
}e[maxm],e1[maxm]; //缩点前后两张图 
int head[maxn],tot;
int head1[maxn],tot1;

int val[maxn]; //点权 
int dfn[maxn],low[maxn],stac[maxn];
int tim;
int sd[maxn]; //strongly connected component dominater，环的代表点 
int top;
int in[maxn];
int dist[maxn]; //以当前点为终点的全部路径的最大路径点权和 
bool vis[maxn]; //是否在栈中 
int n,m;

void add(int u,int v){
    e[++tot].frm=u;
    e[tot].to=v;
    e[tot].nxt=head[u];
    head[u]=tot;
}

void add1(int u,int v){
    e1[++tot1].frm=u;
    e1[tot1].to=v;
    e1[tot1].nxt=head1[u];
    head1[u]=tot1;
}

void tarjan(int x){
    dfn[x]=low[x]=++tim; //初始化dfn和low 
    stac[++top]=x; //入栈 
    vis[x]=1; //显示在栈中 
    for(int i=head[x];i;i=e[i].nxt){
        int y=e[i].to;
        if(!dfn[y]){ 
            tarjan(y);
            low[x]=min(low[x],low[y]);
        }else if(vis[y]){
            low[x]=min(low[x],low[y]);
        }
    }
    if(dfn[x]==low[x]){ //找到环中最靠上的点（单独一个点也看作环） 
        int y=0;
        while(y!=x){
            y=stac[top--];
            sd[y]=x; //指定sd 
            vis[y]=0;
            if(x==y) break;
            val[x]+=val[y]; //点权累加给环的代表点 
        }
    }
}

int topo(){ //dp的思想，拓扑排序保证无后效性 
    queue<int> Q;
    for(int i=1;i<=n;i++){
        if(!in[i]){
            Q.push(i);
            dist[i]=val[i];
        }
    }
    while(!Q.empty()){
        int x=Q.front();
        Q.pop();
        for(int i=head1[x];i;i=e1[i].nxt){
            int y=e1[i].to;
            dist[y]=max(dist[y],dist[x]+val[y]);
            in[y]--;
            if(in[y]==0) Q.push(y);
        }
    }
    int ans=0;
    for(int i=1;i<=n;i++){
        ans=max(ans,dist[i]);
    }
    return ans;
}

int main(){
    scanf("%d %d",&n,&m);
    for(int i=1;i<=n;i++){
        scanf("%d",&val[i]);
    }
    for(int i=1;i<=m;i++){
        int u,v;
        scanf("%d %d",&u,&v);
        add(u,v);
    }
    for(int i=1;i<=n;i++)
        if(!dfn[i]) tarjan(i); //未被遍历到的点执行tarjan 
    for(int i=1;i<=m;i++){
        int x=sd[e[i].frm],y=sd[e[i].to];
        if(x!=y){ //相连的两环的代表点在新图中建边 
            add1(x,y);
            in[y]++;
        }
    }
    printf("%d",topo());
}
```