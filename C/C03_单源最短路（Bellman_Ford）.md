# 单源最短路（Bellman_Ford）
[P3371 【模板】单源最短路径（弱化版）](https://www.luogu.com.cn/problem/P3371)

相比$Floyd$以$k$点为跳板，$Bellman_Ford$的松弛操作与$Dijstra$等的松弛方式相同

```cpp
//洛谷P3371 70分代码
#include<iostream>
using namespace std;
typedef long long ll;
const ll maxn=1e4+5;
const ll maxm=5e5+5;
const ll INF=(1ll<<31)-1;

struct edge{
	ll u,v,w; //直接存边
}e[maxm];
ll n,m,s;
ll dis[maxn];

void BellmanFord(){
	for(int k=1;k<=n;k++){
		for(int i=1;i<=m;i++){
			ll u=e[i].u,v=e[i].v,w=e[i].w;
			dis[v]=min(dis[v],dis[u]+w);
		}
	}
}

int main(){
	scanf("%lld %lld %lld",&n,&m,&s);
	for(int i=1;i<=m;i++){
		ll u,v,w;
		scanf("%d %d %d",&u,&v,&w);
		e[i].u=u;
		e[i].v=v;
		e[i].w=w;
	}
	for(int i=1;i<=n;i++){
		if(i!=s) dis[i]=INF;
	}
	BellmanFord();
	for(int i=1;i<=n;i++){
		printf("%lld ",dis[i]);
	}
}
```
