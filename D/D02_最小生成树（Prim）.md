# 最小生成树（Prim）

[P3366 【模板】最小生成树](https://www.luogu.com.cn/problem/P3366)

核心思想：**从点扩展**。

神似Dijstra。

```cpp
//P3366完整代码 
#include<iostream>
using namespace std;
const int inf=(1<<31)-1;
const int maxn=5e3+5;
const int maxm=2e5+5;
struct edge{
	int to,nxt,val;
}e[maxm<<1];
int head[maxn],tot,num,dis[maxn];
//已经加入最小生成树的的点到没有加入的点的最短距离
//比如说1和2号节点已经加入了最小生成树，那么dis[3]就等于min(1->3,2->3)
int n,m;
bool vis[maxn];

void add(int u,int v,int w){
	e[++tot].to=v;
	e[tot].val=w;
	e[tot].nxt=head[u];
	head[u]=tot;
}

int prim(){
	int ans=0,now=1;
	for(int i=2;i<=n;i++) dis[i]=inf; //先把dis数组附为极大的值
	for(int i=head[1];i;i=e[i].nxt)
        dis[e[i].to]=min(dis[e[i].to],e[i].val); //这里要注意重边，所以要用到min
	while(++num<n){ //最小生成树边数等于点数-1
		int minn=inf; //把minn置为极大值
		vis[now]=1; //标记点已经走过 
		//枚举每一个没有使用的点
        //找出最小值作为新边
        //注意这里不是枚举now点的所有连边，而是1~n
		for(int i=1;i<=n;i++){
			if(!vis[i]&&minn>dis[i]){
				minn=dis[i];
				now=i;
			}
		}
		if(minn==inf) return -1;
		ans+=minn;
		for(int i=head[now];i;i=e[i].nxt){ //枚举now的所有连边，更新dis数组
			int v=e[i].to;
			if(dis[v]>e[i].val&&!vis[v]) dis[v]=e[i].val;
		}
	}
	return ans;
}

int main(){
	cin>>n>>m;
	for(int i=1;i<=m;i++){
		int u,v,w;
		cin>>u>>v>>w;
		add(u,v,w);
		add(v,u,w);
	}
	int ans=prim();
	if(ans==-1) cout<<"orz\n";
	else cout<<ans<<'\n';
}
```