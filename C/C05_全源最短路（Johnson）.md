# 全源最短路（Johnson）
[P5905 【模板】全源最短路（Johnson）](https://www.luogu.com.cn/problem/P5905)

起因是要跑$n$遍$Dijstra$求解全源最短路问题，但$dj$无法处理负权边

1. 首先建立虚拟节点$0$向其他所有点连一条边权为$0$的边

2. 用$Bellman\_Ford(SPFA)$求出$0$到$i$点的路径，记为$h_i$

3. 修改原图的边$(u,v,w)$边权为$w+h_u-h_v$

4. 跑$n$轮$Dijstra$

可以证明，以上修改边权的方法能保证边权非负的情况下求得正确最短路

```cpp
//洛谷P5905完整代码 
#include<iostream>
#include<queue>
#include<cstring>
using namespace std;
typedef long long ll;
const int maxn=3e3+5;
const int maxm=6e3+5;
const int INF=1e9;

struct edge{
	int to,nxt,val;
}e[maxm+maxn];
int head[maxn],tot;

struct node{
	int pos,dis;
	bool operator <(const node&x)const{
		return x.dis<dis;
	}
};

int n,m;
bool inQ[maxn];
int cnt[maxn];
int h[maxn]; //类似“势能”，不管路径如何，两点间的h差值恒定
int dis[maxn];
bool vis[maxn];
ll ans;

void add(int u,int v,int w){
	e[++tot].to=v;
	e[tot].val=w;
	e[tot].nxt=head[u];
	head[u]=tot;
}

bool spfa(int s){
	for(int i=1;i<=n;i++){
		h[i]=INF;
	}h[s]=0;
	queue<int> Q;
	Q.push(s);
	inQ[s]=1;
	while(!Q.empty()){
		int x=Q.front();
		Q.pop();
		inQ[x]=0;
		cnt[x]++;
		if(cnt[x]>n) return 0; //一个点被松弛超过n次则有负环 
		for(int i=head[x];i;i=e[i].nxt){
			int y=e[i].to,w=e[i].val;
			if(h[y]>h[x]+w){
				h[y]=h[x]+w;
				if(!inQ[y]){
					Q.push(y);
					inQ[y]=1;
				}
			}
		}
	}
	return 1;
}

void dj(int s){
	memset(vis,0,sizeof(vis));
	for(int i=1;i<=n;i++){
		dis[i]=INF;
	}dis[s]=0;
	priority_queue<node> Q;
	Q.push((node){s,0});
	while(!Q.empty()){
		int x=Q.top().pos;
		Q.pop();
		if(vis[x]) continue;
		vis[x]=1;
		for(int i=head[x];i;i=e[i].nxt){
			int y=e[i].to,w=e[i].val;
			if(dis[y]>dis[x]+w){
				dis[y]=dis[x]+w;
				if(!vis[y]) Q.push((node){y,dis[y]});
			}
		}
	}
}

int main(){
	scanf("%d %d",&n,&m);
	for(int i=1;i<=n;i++){
		add(0,i,0); //虚拟节点建边
	}
	for(int i=1;i<=m;i++){
		int u,v,w;
		scanf("%d %d %d",&u,&v,&w);
		add(u,v,w);
	}
	if(!spfa(0)){ //spfa判负环 
		printf("-1\n");
		return 0;
	}
	for(int x=1;x<=n;x++){
		for(int i=head[x];i;i=e[i].nxt){
			int y=e[i].to;
			e[i].val+=h[x]-h[y]; //修改边权，使非负
		}
	}
	for(int i=1;i<=n;i++){
		ans=0;
		dj(i);
		for(int j=1;j<=n;j++){
			if(dis[j]==INF)
				ans+=(ll)j*INF;
			else ans+=(ll)j*(dis[j]+h[j]-h[i]); //注意计算答案时要把“势能”反加回来
		}
		printf("%lld\n",ans);
	}
} 
```