# 割点（Tarjan）
[P3388 【模板】割点（割顶）](https://www.luogu.com.cn/problem/P3388)

无向图 DFS 树的性质（非常重要）：

1. 祖先后代性：任意非树边两端具有祖先后代关系。
2. 子树独立性：结点的每个儿子的子树之间没有边（和上一条性质等价）。
3. 时间戳区间性：子树时间戳为一段区间。
4. 时间戳单调性：结点的时间戳小于其子树内结点的时间戳。

```cpp
//P3388完整代码
#include<iostream>
#include<vector>
using namespace std;
const int maxn=1e5+5;
int n,m,R; //R是DFS根结点
int dn,dfn[maxn],low[maxn],cnt,buc[maxn];
// dn 是时间戳计数器，dfn 数组记录每个节点的时间戳，low 数组记录每个节点能回溯到的最早时间戳
// cnt 记录割点的数量，buc 数组标记每个节点是否为割点
vector<int> e[maxn];
void dfs(int id){
    low[id]=dfn[id]=++dn; // 初始化当前节点的时间戳和能回溯到的最早时间戳
    int son=0;
    for(int it:e[id]){
        if(!dfn[it]){
            son++;
            dfs(it);
            low[id]=min(low[id],low[it]);
            if(low[it]>=dfn[id]&&id!=R){
                if(!buc[id]) cnt++;
                buc[id]=1;
            }
        }else low[id]=min(low[id],dfn[it]);
    }
    if(son>=2&&id==R){
        if(!buc[id]) cnt++;
        buc[id]=1;
    }
}
int main(){
    cin>>n>>m;
    for(int i=1;i<=m;i++){
        int u,v;
        cin>>u>>v;
        e[u].push_back(v);
        e[v].push_back(u);
    }
    for(int i=1;i<=n;i++)
        if(!dfn[i]){
            R=i;
            dfs(i);
        }
    cout<<cnt<<endl;
    for(int i=1;i<=n;i++)
        if(buc[i]) cout<<i<<' ';
}
```