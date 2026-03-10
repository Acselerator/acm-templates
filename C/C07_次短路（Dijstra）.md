# 次短路（Dijstra）
[P1491 集合位置](https://www.luogu.com.cn/problem/P1491)

非严格次短路

首先找出一条最短路，之后一次去掉一条最短路上的边，对于每一次去掉一条边后的图都跑一次最短路，其中最小的答案就是次短路。

```cpp
//洛谷P1491完整代码 
#include<iostream>
#include<cstring>
#include<queue>
#include<cmath>
using namespace std; 
const int maxn=200+5;
const int maxm=1e4+5;;
const int INF=(1ll<<31)-1;

struct pnt{
	int x,y;
}pt[maxn];
double dis[maxn];

struct edge{
	int to,nxt;
	double val;
}e[maxm<<1];
int head[maxn],tot;

struct node{
	int pos;
	double d;
	bool operator <(const node&x)const{
		return x.d<d;
	}
};

int n,m;
bool vis[maxn];
int path[maxn];

double getdis(int u,int v){
	return sqrt(1.0*pow(pt[u].x-pt[v].x,2)+pow(pt[u].y-pt[v].y,2));
}

void add(int u,int v,double w){
	e[++tot].to=v;
	e[tot].val=w;
	e[tot].nxt=head[u];
	head[u]=tot;
}

void dj(int u,int v){ //u,v是当前次要删去的边的两端点
	memset(vis,0,sizeof(vis));
	for(int i=1;i<=n;i++){
		dis[i]=INF;
	}dis[1]=0;
	priority_queue<node> Q;
	Q.push((node){
		1,0
	});
	while(!Q.empty()){
		int x=Q.top().pos;
		Q.pop();
		if(vis[x]) continue;
		vis[x]=1;
		for(int i=head[x];i;i=e[i].nxt){
			int y=e[i].to;
			double w=e[i].val;
			if((x==u&&y==v)||(x==v&&y==u)) continue; //忽略该边
			if(dis[y]>dis[x]+w){
				dis[y]=dis[x]+w;
				if(u==-1&&v==-1) path[y]=x; //只有求最短路时记录路径
				if(!vis[y]) Q.push((node){
					y,dis[y]
				});
			}
		}
	}
	
}

int main(){
	scanf("%d %d",&n,&m);
	for(int i=1;i<=n;i++){
		scanf("%d %d",&pt[i].x,&pt[i].y);
	}
	for(int i=1;i<=m;i++){
		int u,v;
		scanf("%d %d",&u,&v);
		double w=getdis(u,v);
		add(u,v,w);
		add(v,u,w);
	}
	dj(-1,-1); //第一次不用删边
	double ans=INF;
	for(int i=n;i!=1;i=path[i]){
		dj(i,path[i]);
		
		ans=min(ans,dis[n]);
	}
	if(INF-ans<1e-6) printf("-1\n");
	else printf("%.2lf\n",ans);
} 
```