# 最短路（Dijkstra）
Dijkstra的本质思想是贪心,它只适用于**不含负权边**的图

思路：维护白点集，每次将蓝点集中距离白点集最近的蓝点加入白点集，并通过该点更新与之直接相连的蓝点到白点集的距离，直至没有新的蓝点被加入白点集。

[P4779 【模板】单源最短路径（标准版）](https://www.luogu.com.cn/problem/P4779)

堆优化：
```cpp
//洛谷P4779完整代码
#include<iostream>
#include<queue>
using namespace std;
const int maxn=100005;
const int maxm=200005;
const int INF=(1ll<<31)-1;

struct Edge{
	int to,nxt,val;
}e[maxm];
struct Node{
	int dis,pos;
	 bool operator <( const Node &x )const{
        return x.dis < dis;
    }
};
int head[maxn],tot;
int n,m,s;
void add(int x,int y,int w){
	e[++tot].to=y;
	e[tot].val=w;
	e[tot].nxt=head[x];
	head[x]=tot;
}
int dist[maxn];
bool vis[maxn];
void dj(int st){
	priority_queue<Node> q;
	q.push({0,st});
	while(!q.empty()){
		int x=q.top().pos;
        q.pop();
		if(vis[x]) continue;
		vis[x]=1;
		for(int i=head[x];i;i=e[i].nxt){
			int y=e[i].to;
			if(!vis[y]&&dist[y]>dist[x]+e[i].val){
				dist[y]=dist[x]+e[i].val;
				q.push({dist[y],y});
			}
		}
	}
}
int main(){
	cin>>n>>m>>s;
	for(int i=1;i<=m;i++){
		int t1,t2,w;
		cin>>t1>>t2>>w;
		add(t1,t2,w);
	}
	for(int i=1;i<=n;i++) if(i!=s) dist[i]=INF;
	dj(s);
	for(int i=1;i<=n;i++){
		cout<<dist[i]<<' ';
	}
}
```
[P3371 【模板】单源最短路径（弱化版）](https://www.luogu.com.cn/problem/P3371)

无堆优化：
```cpp
//P3371完整代码
#include<iostream>
#include<algorithm>
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
int dis[maxn],path[maxn];
bool vis[maxn];

void add(int u,int v,int w){
	e[++tot].to=v;
	e[tot].val=w;
	e[tot].nxt=head[u];
	head[u]=tot;
}

void dj(int x){
	int cnt=0;
	while(cnt<n){
		int mindis=INF;
		x=0;
		for(int j=1;j<=n;j++){
			if(!vis[j]&&dis[j]<mindis){
				x=j;
				mindis=dis[j];
			}
		}
		if(x==0) break;
		vis[x]=1;
		for(int j=head[x];j;j=e[j].nxt){
			int y=e[j].to,w=e[j].val;
			if(!vis[y]&&dis[y]>w+dis[x]){
				dis[y]=w+dis[x];
				path[y]=x;
			}
		}
		cnt++;
	}
}

int main(){
	cin>>n>>m>>s;
	for(int i=1;i<=m;i++){
		int u,v,w;
		cin>>u>>v>>w;
		add(u,v,w);
	}
	for(int i=1;i<=n;i++) if(i!=s)dis[i]=INF;
	dj(s);
	for(int i=1;i<=n;i++){
		cout<<dis[i]<<' ';
	}
}
```