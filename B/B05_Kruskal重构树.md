# Kruskal重构树

*性质*
1. 是一个小/大根堆(由建树时边权的排序方式决定)
2. **LCA(u,v)** 的权值是 原图 u到v路径上**最大/小边权**的**最小/大值**(由建树时边权的排序方式决定)

*核心建树过程*
- 遍历排序后的边，对每条边(u, v, w)：
- 若u和v属于不同连通分量，则创建一个**新节点**，权值为w。
- 将u和v所在连通分量的根节点设为新节点的**子节点**。
- 用新节点作为合并后连通分量的**根**（更新并查集）。
- 重复上述过程，直到所有边处理完毕。

[P1967 [NOIP 2013 提高组] 货车运输](https://www.luogu.com.cn/problem/P1967)

求图中任意两点间所有路径中最小边权的最大值

```cpp
//洛谷P1967 完整代码
#include<iostream>
#include<algorithm>
using namespace std;
const int maxm=5e5+5;
const int maxn=1e5+5+maxm;

struct edge{ //输入的边
    int x,y,w;
}e[maxm];
struct edge1{ //Kruskal重构树的边
    int nxt,to;
}e1[maxm<<2];
int head[maxn],tot;
void add(int x,int y){
    e1[++tot].to=y;
    e1[tot].nxt=head[x];
    head[x]=tot;
}

int n,m,q;
int cnt;
int ff[maxn],val[maxn]; //并查集的祖先，点权
int siz[maxn],fa[maxn],dep[maxn]; //子树大小，父结点，深度
int son[maxn],top[maxn]; //重儿子，链顶
bool vis[maxn];

int find(int x){
    if(ff[x]==x) return x;
    else return ff[x]=find(ff[x]);
}
void join(int x,int y,int w){ //代码使用了join函数，那么函数要作较多改变
    int f1=find(x),f2=find(y);
    if(f1!=f2){ //若两点不在同一集合
        val[++cnt]=w; //新建结点，点权为原本的边权
        ff[cnt]=ff[f1]=ff[f2]=cnt; //合并并查集祖先为新点
        add(f1,cnt); add(cnt,f1);
        add(f2,cnt); add(cnt,f2);
    }
}

void dfs1(int x,int fath){ //树剖dfs1
    siz[x]=1;
    vis[x]=1;
    fa[x]=fath;
    dep[x]=dep[fath]+1;
    for(int i=head[x];i;i=e1[i].nxt){
        int y=e1[i].to;
        if(y==fath) continue;
        dfs1(y,x);
        siz[x]+=siz[y];
        if(siz[y]>siz[son[x]]){
            son[x]=y;
        }
    }
}
void dfs2(int x,int tp){ //树剖dfs2
    top[x]=tp;
    if(!son[x]) return;
    dfs2(son[x],tp);
    for(int i=head[x];i;i=e1[i].nxt){
        int y=e1[i].to;
        if(y==fa[x]||y==son[x]) continue;
        dfs2(y,y);
    }
}

bool cmp(edge x,edge y){
    return x.w>y.w;
}
void kruskal(){
    sort(e+1,e+1+m,cmp);
    for(int i=1;i<=n;i++){
        ff[i]=i;
    }
    for(int i=1;i<=m;i++){
        int x=e[i].x,y=e[i].y,w=e[i].w;
        join(x,y,w); //构建Kruskal重构树
    }
    for(int i=1;i<=cnt;i++){ //重构树图可能是森林
        if(!vis[i]){
            int f=find(i); //指定每个集合的根作为所在森林的根
            dfs1(f,0);
            dfs2(f,f);
        }
    }
}

int LCA(int x,int y){ //树剖求LCA
    while(top[x]!=top[y]){
        if(dep[top[x]]>dep[top[y]]){
            x=fa[top[x]];
        }else y=fa[top[y]];
    }
    return dep[x]<dep[y]?x:y;
}

int main(){
    ios::sync_with_stdio(false);
    cin.tie(0); cout.tie(0);
    cin>>n>>m;
    cnt=n;
    for(int i=1;i<=m;i++){
        cin>>e[i].x>>e[i].y>>e[i].w;
    }
    kruskal();
    cin>>q;
    for(int i=1;i<=q;i++){
        int x,y;
        cin>>x>>y;
        if(find(x)!=find(y)){ //不可达
            cout<<"-1\n";
        }else{
            cout<<val[LCA(x,y)]<<"\n"; //两点路径上最小边权的最大值就是LCA的点权
        }
    }
}
```