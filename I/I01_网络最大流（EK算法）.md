# 网络最大流（EK算法）

边的容量：$c(x,y)$
边的流量：$f(x,y)$

流函数f满足三个条件：
1. $f(x,y) \leq c(x,y)$
2. $f(x,y) = -f(y,x)$
3. $\forall x \neq S , x\neq T , \sum_{(u,x) \in E} f(u,x) = \sum_{(x,v) \in E} f(x,v)$

对应流函数的三个性质：
1. 容量限制：每条边的流量总不可能大于该边的容量的（不然水管就爆了）
2. 斜对称：正向边的流量=反向边的流量（反向边后面会具体讲）
3. 流量守恒：正向的所有流量和=反向的所有流量和（就是总量始终不变）

在任意时刻，网络中所有节点以及剩余容量大于0的边构成的子图被称为**残量网络**

---

$Edmonds−Karp增广路算法$

**不断用BFS寻找增广路并不断更新最大流量值，直到网络上不存在增广路为止**

时间复杂度为$O(nm^2)$，一般能处理$10^3 \sim 10^4$规模的网络

```cpp
//P3376完整代码（EK算法） 
#include<iostream>
#include<queue>
using namespace std;
#define ll long long
const int maxn=2e2+5;
const int maxm=5e3+5;
const int inf=0x3f3f3f3f;
/*
增广路：一条从S到T的所有边剩余容量都大于0的路径（流量可增大的路径）
反向边提供“反悔”机会 
*/
struct Node{
	int to,nxt;
	ll val;
}e[maxm<<1]; //包含正向边和反向边 
int head[maxn],flag[maxn][maxn],pre[maxm],tot=1; //边实际从2开始编号 
bool vis[maxn];
int n,m,s,t;
ll dis[maxn],ans;

void insert(int x,int y,ll w){
	e[++tot].to=y; //正向边为偶数，反向边为奇数 
	e[tot].val=w;
	e[tot].nxt=head[x];
	head[x]=tot;
	e[++tot].to=x;
	e[tot].val=0;
	e[tot].nxt=head[y];
	head[y]=tot;
}
bool bfs(){ //bfs寻找增广路 
	for(int i=1;i<=n;i++) vis[i]=0;
	queue<int> q;
	q.push(s);
	vis[s]=1;
	dis[s]=inf; //源点dis设为无穷大，保证在min函数中不被选择 
	while(!q.empty()){
		int u=q.front();q.pop();
		for(int i=head[u];i;i=e[i].nxt){
			if(e[i].val==0) continue; //只看剩余流量>0的边 
			int v=e[i].to;
			if(vis[v]==1) continue; //只看未访问过的增广路 
			dis[v]=min(dis[u],e[i].val);
			pre[v]=i; //记录前驱，方便修改边权 
			vis[v]=1;
			q.push(v);
			if(v==t) return 1; //找到了一条增广路 
		}
	}
	return 0;
}
void update(){ //更新所经过边的正向边权和反向边权 
	int x=t;
	while(x!=s){
		int v=pre[x];
		e[v].val-=dis[t]; //奇数异或1相当于-1，偶数异或1相当于+1
		e[v^1].val+=dis[t];
		x=e[v^1].to;
	}
}

int main(){
	scanf("%d%d%d%d",&n,&m,&s,&t);
	for(int i=1;i<=m;i++){
		int x,y;
		ll w;
		scanf("%d%d%lld",&x,&y,&w);
		if(flag[x][y]==0){ //处理重边 
			insert(x,y,w);
			flag[x][y]=tot-1; //flag存储正向边的编号 
		}else{
			e[flag[x][y]].val+=w;
		}
	}
	while(bfs()){ //直到找不到增广路 
		ans+=dis[t]; //累加每一条增广路径的最小流量值 
		update();
	}
	printf("%lld",ans);
	return 0;
}
```