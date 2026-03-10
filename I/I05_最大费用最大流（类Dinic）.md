# 最大费用最大流（类Dinic）

这题运用了拆边

[P4012 深海机器人问题](https://www.luogu.com.cn/problem/P4012)

```cpp
//洛谷P4012 完整代码
#include<iostream>
#include<queue>
using namespace std;
const int maxs=20;
const int maxn=maxs*maxs;
const int maxm=16*maxn+maxn*4;
const int INF=0x3f3f3f3f;

struct edge{
	int to,nxt;
	int flow,cap; 
}e[maxm];
int head[maxn],tot=1;
void add(int x,int y,int f,int c){
	e[++tot].to=y;
	e[tot].flow=f;
	e[tot].cap=c;
	e[tot].nxt=head[x];
	head[x]=tot;
	
	e[++tot].to=x;
	e[tot].flow=0;
	e[tot].cap=-c;
	e[tot].nxt=head[y];
	head[y]=tot;
}

int aa,bb,P,Q;
int idx=-1;
int mp[maxs][maxs];
int S,T;
int mxflow,mxcost;
bool vis[maxn];
int now[maxn],dis[maxn];

bool bfs(){
	for(int i=0;i<=T;i++){
		dis[i]=-INF;
	}dis[S]=0;
	queue<int> q;
	q.push(S);
	vis[S]=1;
	now[S]=head[S];
	while(!q.empty()){
		int x=q.front();
		q.pop();
		vis[x]=0;
		for(int i=head[x];i;i=e[i].nxt){
			int y=e[i].to;
			if(dis[y]<dis[x]+e[i].cap&&e[i].flow){
				dis[y]=dis[x]+e[i].cap;
				now[y]=head[y];
				if(!vis[y]){
					q.push(y);
					vis[y]=1;
				} 
			}
		}
	}
	return dis[T]!=-INF;
}

int dfs(int x,int sum){
	if(x==T) return sum;
	vis[x]=1;
	int res=0;
	for(int i=now[x];i&&sum;i=e[i].nxt){
		now[x]=i;
		int y=e[i].to;
		if(!vis[y]&&e[i].flow&&(dis[y]==dis[x]+e[i].cap)){
			int tmp=dfs(y,min(sum,e[i].flow));
			if(tmp==0) continue;
			mxcost+=tmp*e[i].cap;
			sum-=tmp;
			res+=tmp;
			e[i].flow-=tmp;
			e[i^1].flow+=tmp; 
		}
	}
	vis[x]=0;
	return res;
}

int main(){
	scanf("%d%d",&aa,&bb);
	scanf("%d%d",&P,&Q);
	for(int i=0;i<=Q;i++){
		for(int j=0;j<=P;j++){
			mp[i][j]=++idx; 
		}
	}
	S=idx+1;
	T=S+1;
	for(int j=0;j<=P;j++){
		for(int i=0;i<Q;i++){
			int tmp;
			scanf("%d",&tmp);
			add(mp[i][j],mp[i+1][j],1,tmp);
			add(mp[i][j],mp[i+1][j],INF,0);
		}
	}
	for(int i=0;i<=Q;i++){
		for(int j=0;j<P;j++){
			int tmp;
			scanf("%d",&tmp);
			add(mp[i][j],mp[i][j+1],1,tmp);
			add(mp[i][j],mp[i][j+1],INF,0);
		}
	}
	for(int i=1;i<=aa;i++){
		int k,x,y;
		scanf("%d%d%d",&k,&x,&y);
		add(S,mp[y][x],k,0);
	}
	for(int i=1;i<=bb;i++){
		int r,x,y;
		scanf("%d%d%d",&r,&x,&y);
		add(mp[y][x],T,r,0);
	}
	while(bfs()){
		mxflow+=dfs(S,INF);
	}
	printf("%d\n",mxcost);
}
```