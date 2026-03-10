# 割边

[P1656 炸铁路](https://www.luogu.com.cn/problem/P1656)

```cpp
//洛谷P1656 完整代码 
#include<iostream>
#include<algorithm>
using namespace std;
const int maxn=155;
const int maxm=5005;

struct edge{
	int to,nxt;
}e[maxm<<1];
int head[maxn],tot;
void add(int x,int y){
	e[++tot].to=y;
	e[tot].nxt=head[x];
	head[x]=tot;
}

int n,m;

int dfn[maxn],low[maxn],tim;
struct edge1{
	int frm,to;
}e1[maxm<<1];
int cnt;

bool cmp(edge1 x,edge1 y){
	if(x.frm!=y.frm) return x.frm<y.frm;
	return x.to<y.to;
}

void tarjan(int x,int lst){
	dfn[x]=low[x]=++tim;
	for(int i=head[x];i;i=e[i].nxt){
		int y=e[i].to;
		if(y==lst) continue; //注意 
		if(!dfn[y]){
			tarjan(y,x);
			if(low[y]>dfn[x]){ //关键 
				e1[++cnt]=(edge1){x,y};
			}
			low[x]=min(low[x],low[y]);
		}else low[x]=min(low[x],dfn[y]);
	}
}

int main(){
	scanf("%d%d",&n,&m);
	for(int i=1;i<=m;i++){
		int x,y;
		scanf("%d%d",&x,&y);
		add(x,y);
		add(y,x);
	}
	tarjan(1,0);
	sort(e1+1,e1+1+cnt,cmp);
	for(int i=1;i<=cnt;i++){
		printf("%d %d\n",e1[i].frm,e1[i].to);
	}
}
```