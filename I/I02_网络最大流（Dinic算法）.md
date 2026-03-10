# 网络最大流（Dinic算法）

EK算法的改进

$Dinic算法$框架
1. 在残量网络上$BFS$求出节点的层次，构造分层图
2. 在分层图上$DFS$寻找增广路，在回溯时同时更新边权

时间复杂度：$O(n^2m)$，一般能够处理$10^4 \sim 10^5$规模的网络

代码使用到的优化：当前弧优化+剪枝

```cpp
//P3376完整代码（Dinic算法） 
#include<iostream>
#include<queue>
using namespace std;
#define ll long long
const int maxn=2e2+5;
const int maxm=5e3+5;
const ll inf=0x3f3f3f3f;
/*
Dinic算法
相比EK算法，每次BFS能找到多条增广路
除使用反向边，还运用了分层图和DFS
算法复杂度O(edge*dot^2)相比EK算法O(edge^2*dot)往往更快 
*/ 
struct Node{
	int to,nxt;
	ll val;
}e[2*maxm];
ll w,ans,dis[maxn];
int now[maxn],head[maxn],tot=1; 
int n,m,s,t,u,v;

void add(int u,int v,ll w){
	e[++tot].to=v;
	e[tot].val=w;
	e[tot].nxt=head[u];
	head[u]=tot;
	
	e[++tot].to=u;
	e[tot].val=0;
	e[tot].nxt=head[v];
	head[v]=tot;
}
bool bfs(){ //在残量网络中构造分层图 
	for(int i=1;i<=n;i++) dis[i]=inf; //初始化各点层数为inf 
	queue<int> q;
	q.push(s);
	dis[s]=0; //层数，从0起始 
	now[s]=head[s]; //先更新起点now 
	while(!q.empty()){
		int x=q.front();
		q.pop();
		for(int i=head[x];i;i=e[i].nxt){ //从head开始遍历 
			int v=e[i].to;
			if(e[i].val>0&&dis[v]==inf){ //找没访问过的且剩有流量的边 
				q.push(v);
				now[v]=head[v]; //更新now 
				dis[v]=dis[x]+1;
				if(v==t) return 1;
			}
		}
	}
	return 0;
}
int dfs(int x,ll sum){ //sum一开始是流入这个点的流量 
	if(x==t) return sum; //到达汇点的sum是整条增广路对最大流的贡献 
	ll k,res=0;
	for(int i=now[x];i&&sum;i=e[i].nxt){ //从now开始遍历
		now[x]=i; //当前弧优化，当前已经走过的最后一条边 
		int v=e[i].to;
		if(e[i].val>0&&(dis[v]==dis[x]+1)){ //找剩有流量且通向下一层（不通向同一层或上层）的点 
			k=dfs(v,min(sum,e[i].val)); //k为该点从该路流出的流量（该路当前最小的剩余流量） 
			if(k==0) dis[v]=inf; //点的层设为inf以剪枝，去掉增广完毕的点 
			e[i].val-=k;
			e[i^1].val+=k;
			res+=k; //res为从该点流出的总流量 
			sum-=k; //sum表示经过该点的剩余流量 
		}
	}
	return res;
}
int main(){
	scanf("%d%d%d%d",&n,&m,&s,&t);
	for(int i=1;i<=m;i++){
		scanf("%d%d%lld",&u,&v,&w);
		add(u,v,w);
	}
	while(bfs()){
		ans+=dfs(s,inf);
	}
	printf("%lld",ans);
}
```