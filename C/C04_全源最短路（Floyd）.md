# 多源最短路（Floyd）
[P2910 [USACO08OPEN] Clear And Present Danger S](https://www.luogu.com.cn/problem/P2910)

**Floyd（弗洛伊德）算法**是用来求解带权图（无论正负）中的多源最短路问题。算法的原理是动态规划。

用dist(i,j,k)表示从顶点i到顶点j只经过前k个顶点的最短路的长度。那么只有如下两种情况:

1.i,j之间的最短路不经过k+1，dist(i,j,k+1)<-dist(i,j,k)

2.i,j之间的最短路经过k+1，dist(i,j,k+1)<-dist(i,k+1,k)+dist(k+1,j,k)。

所以dist(i,j,k+1)<-min{dist(i,j,k),dist(i,k+1,k)+dist(k+1,j,k)}。

在算法实现的时候可以省略掉k那一维，只需要用一个二维数组即可。
```cpp
//洛谷P2910完整代码
#include<iostream>
using namespace std;
const int maxn=110;
const int maxm=10010;

int n,m;
int order[maxm],dis[maxn][maxn];
int ans;

int main(){
    cin>>n>>m;
    for(int i=1;i<=m;i++)
        cin>>order[i];
    for(int i=1;i<=n;i++)
        for(int j=1;j<=n;j++)
            cin>>dis[i][j];

    for(int k=1;k<=n;k++)
        for(int i=1;i<=n;i++)
            for(int j=1;j<=n;j++)
                dis[i][j]=min(dis[i][j],dis[i][k]+dis[k][j]);
    for(int i=1;i<m;i++) ans+=dis[order[i]][order[i+1]];
    ans+=dis[1][order[1]];
    ans+=dis[order[m]][n];
    cout<<ans<<'\n';
}
```