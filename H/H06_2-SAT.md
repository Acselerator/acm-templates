# 2-SAT

SAT 问题，已被证明为 NP完全 的，只能暴力。

使用强连通分量。 对于每个变量 x，我们建立两个点：x,¬x 分别表示变量 x 取 true 和取 false。所以，图的节点个数是两倍的变量个数。在存储方式上，可以给第 i 个变量标号为 i，其对应的反值标号为 i+n。

![2-SAT](https://s1.ax1x.com/2018/08/22/PTAjy9.png)

[P4782 【模板】2-SAT](https://www.luogu.com.cn/problem/P4782)

```cpp
#include<iostream>
using namespace std;
const int maxn=1e6+5;
const int maxm=1e6+5;

struct edge{
	int to,nxt;
}e[maxm<<1];
int head[maxn<<1],tot;
void add(int x,int y){
	e[++tot].to=y;
	e[tot].nxt=head[x];
	head[x]=tot;
}

int n,m;
int dfn[maxn<<1],low[maxn<<1],tim;
int st[maxn<<1],top,inst[maxn<<1];
int color[maxn<<1],cnt;

void tarjan(int x){
	dfn[x]=low[x]=++tim;
	st[++top]=x;
	inst[x]=1;
	for(int i=head[x];i;i=e[i].nxt){
		int y=e[i].to;
		if(!dfn[y]){
			tarjan(y);
			low[x]=min(low[x],low[y]);
		}else{
			if(inst[y]) low[x]=min(low[x],dfn[y]);
		}
	}
	if(low[x]==dfn[x]){
		++cnt;
		while(st[top+1]!=x){
			inst[st[top]]=0;
			color[st[top]]=cnt;
			top--;
		}
	}
}

int f(int x){
	if(x<=n) return x+n;
	else return x-n;
}

int main(){
	scanf("%d%d",&n,&m);
	for(int i=1;i<=m;i++){
		int t1,t2,t3,t4;
		scanf("%d%d%d%d",&t1,&t2,&t3,&t4);
		if(t2){
			if(t4){ //1 1
				add(f(t1),t3);
				add(f(t3),t1);
			}else{ //1 0
				add(f(t1),f(t3));
				add(t3,t1);
			}
		}else{
			if(t4){ //0 1
				add(t1,t3);
				add(f(t3),f(t1));
			}else{ //0 0
				add(t1,f(t3));
				add(t3,f(t1));
			}
		}
	}
	
	for(int i=1;i<=(n<<1);i++){
		if(!dfn[i]) tarjan(i);
	}
	
	for(int i=1;i<=n;i++){
		if(color[i]==color[f(i)]){
			printf("IMPOSSIBLE\n");
			return 0;
		}
	}
	printf("POSSIBLE\n");
	for(int i=1;i<=n;i++){
		printf("%d ",color[i]<color[f(i)]); //注意，与dfs序有关 
	}printf("\n");
}
```