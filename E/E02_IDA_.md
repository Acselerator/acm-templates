# IDA*
[P2324 [SCOI2005] 骑士精神](https://www.luogu.com.cn/problem/P2324)

$A^*$是估价函数优化的$BFS$

$IDA^*$就是带有迭代加深和估价函数优化的$DFS$

**迭代加深：**

- 每次限定一个$maxdep$最大深度，使搜索树的深度不超过$maxdep$
- 使用范围：

1. 有一定限制条件（如洛谷P2324中“如果能在15步以内（包括15步）到达目标状态，则输出步数，否则输出−1。”）。
2. 题目要求输出所有解中的任何一组解。

**估价函数：**
$$
f(n)=g(n)+h(n)
$$
- $f(n)$估价函数
- $g(n)$目前的实际步数
- $h(n)$对接下来步数的最完美估价

```cpp
//洛谷P2324完整代码 
#include<iostream>
using namespace std;

const int goal[7][7]={
	{0,0,0,0,0,0},
	{0,1,1,1,1,1},
	{0,0,1,1,1,1},
	{0,0,0,2,1,1},
	{0,0,0,0,0,1},
	{0,0,0,0,0,0}
};
const int dx[]={0,1,2,2,1,-1,-2,-2,-1};
const int dy[]={0,2,1,-1,-2,-2,-1,1,2};

int mp[7][7];
int T;
bool ans;
int sx,sy;

int check(){ //计算h(n)
	int cnt=0;
	for(int i=1;i<=5;i++)
		for(int j=1;j<=5;j++)
			if(mp[i][j]!=goal[i][j]) cnt++;
	return cnt;
}

bool safe(int x,int y){
	if(x<1||x>5||y<1||y>5) return 0;
	else return 1;
}

void IDA_star(int dep,int x,int y,int maxdep){
	if(dep==maxdep){
		if(check()==0) ans=1;
		return;
	}
	for(int i=1;i<=8;i++){
		int nx=dx[i]+x,ny=dy[i]+y;
		if(!safe(nx,ny)) continue;
		swap(mp[nx][ny],mp[x][y]);
		int chk=check();
		if(chk+dep<=maxdep)
			IDA_star(dep+1,nx,ny,maxdep); //dep即f(n)
		swap(mp[nx][ny],mp[x][y]);
	}
}

int main(){
	scanf("%d",&T);
	while(T--){
		ans=0;
		for(int i=1;i<=5;i++){
			for(int j=1;j<=5;j++){
				char ch;
				cin>>ch;
				if(ch=='*'){
					mp[i][j]=2;
					sx=i;
					sy=j;
				}else mp[i][j]=ch-'0';
			}
		}
		if(check()==0){
			printf("%d\n",0);
			continue;
		}
		bool f=0;
		for(int maxdep=1;maxdep<=15;maxdep++){ //限制深度，以尽可能遍历更广泛的分支
			IDA_star(0,sx,sy,maxdep);
			if(ans){
				printf("%d\n",maxdep);
				f=1;
				break;
			}
		}
		if(!f) printf("%d\n",-1);
	}
} 
```