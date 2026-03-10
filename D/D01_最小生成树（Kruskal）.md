# 最小生成树（Kruskal）

[P3366 【模板】最小生成树](https://www.luogu.com.cn/problem/P3366)

核心思想：**找短边**

```cpp
//P3366完整代码 
#include<iostream>
#include<algorithm>
using namespace std;
const int maxn=5e3+5;
const int maxm=2e5+5;

struct edge{
	int u,v,w;
}e[maxm];
int fa[maxn],cnt;
int n,m;
int ans;

bool cmp(edge a,edge b){//快排的依据（按边权排序）
	return a.w<b.w;
}
int find(int x){ //并查集找祖先 
	if(x==fa[x]) return x;
	else return fa[x]=find(fa[x]);
}
void kruskal(){
	sort(e,e+m,cmp); //将边的权值排序
	for(int i=0;i<m;i++){
		int fu=find(e[i].u),fv=find(e[i].v);
		if(fu==fv) continue; //若出现两个点已经联通了，则说明这一条边不需要了
		ans+=e[i].w; //将此边权计入答案
		fa[fu]=fv; //合并祖先 
		if(++cnt==n-1) break; //循环结束条件，即边数为点数减一时
	}
}

int main(){
	cin>>n>>m;
	for(int i=1;i<=n;i++){
		fa[i]=i; //初始化并查集
	}
	for(int i=0;i<m;i++){
		cin>>e[i].u>>e[i].v>>e[i].w;
	}
	kruskal();
	if(cnt==n-1) cout<<ans<<'\n';
	else cout<<"orz\n";
} 
```