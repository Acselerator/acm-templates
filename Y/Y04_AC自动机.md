# AC自动机

Aho-Corasick automaton，著名的多模匹配算法

用于求解多个模式串在文本串中出现的次数

fail[i]（失配指针）为与以i节点为结尾的串的后缀有最大公共长度的前缀的结尾编号

![Fail指针](https://cdn.luogu.com.cn/upload/pic/26392.png)

---

求出现了的不同编号的模式串数：

[P3808 AC 自动机（简单版）](https://www.luogu.com.cn/problem/P3808)

```cpp
//洛谷P3808完整代码
#include<iostream>
#include<queue>
using namespace std;

const int maxn=1e6+10;

struct Node{
	int son[26],flag,fail;	
	/*
	Fail指针： 
	如果一个点i的Fail指针指向j，
	那么root到j的字符串是root到i的字符串的一个后缀 
	(Fail指的j的深度要尽量大)
	*/
}trie[maxn];	//字典树
int n;
string s;
int cnt=1;	//编号从1开始，1为root，不存字符
queue<int> q;

void insert(string s){
	int u=1,len=s.size();
	for(int i=0;i<len;i++){
		int v=s[i]-'a';
		if(!trie[u].son[v]) trie[u].son[v]=++cnt;	//若无此儿子，加儿子，编号cnt 
		u=trie[u].son[v];	//u到下一层 
	}
	trie[u].flag++;	//到达字符串末尾时flag自加 
}
void getFail(){
	for(int i=0;i<26;i++) trie[0].son[i]=1;	//初始化0的所有儿子都是1
	q.push(1);trie[1].fail=0;	//1的Fail为0，入队列 
	while(!q.empty()){
		int u=q.front();q.pop();
		for(int i=0;i<26;i++){	//遍历所有儿子 
			int v=trie[u].son[i];	//处理u的i儿子的fail
			int Fail=trie[u].fail;	//就是fafail，trie[Fail].son[i]就是和v值相同的点
			//无论fafail存不存在和i值相同的儿子j，我们都将i的fail指向j 
			if(!v){trie[u].son[i]=trie[Fail].son[i];continue;}
			/*
			如果不存在节点i，
			那么我们可以将那个节点设为fafail的值和i相同的儿子，
			以此保证存在性。
			即使是0也可以成功返回到root，
			因为0的所有儿子都是root 
			*/ 
			trie[v].fail=trie[Fail].son[i];	//存在节点i 
			q.push(v);	//存在实节点才压入队列 
		}
	}
}
int query(string s){
	int ans=0;
	int u=1,len=s.size();
	for(int i=0;i<len;i++){
		int v=s[i]-'a';
		int k=trie[u].son[v];	//跳Fail
		while(k>1&&trie[k].flag!=-1){	//经过就不统计了 
			ans+=trie[k].flag;	//累加上这个位置的模式串个数 
			trie[k].flag=-1;
			k=trie[k].fail;	//继续跳Fail，即使无Fail也能返回到根 
		}
		u=trie[u].son[v];	//到儿子，得益于getFail中保证的儿子存在性，可使此过程反复遍历字典树
	}
	return ans;
}

int main(){
	cin>>n;
	for(int i=0;i<n;i++){ 
		cin>>s;
		insert(s);	//构造字典树 
	}
	getFail();	//计算所有节点的Fail 
	cin>>s;
	cout<<query(s)<<endl;
}
```

求出现次数最多的复数个模式串：

[P3796 AC 自动机（简单版 II）](https://www.luogu.com.cn/problem/P3796)

```cpp
//洛谷P3796完整代码 
#include<iostream>
#include<cstring>
#include<queue>
using namespace std;
const int maxn=155;
const int maxs=150*70+5;

struct Node{
	int son[26],flag,fail;
}trie[maxs];

int n,cnt=1;
int ans[maxn],mx;
queue<int> q;

void insert(string s,int id){
	int u=1,len=s.length();
	for(int i=0;i<len;i++){
		int v=s[i]-'a';
		if(!trie[u].son[v]) trie[u].son[v]=++cnt;
		u=trie[u].son[v];
	}
	trie[u].flag=id; //题目前提：保证不存在两个相同的模式串
}

void getFail(){
	for(int i=0;i<26;i++) trie[0].son[i]=1;
	q.push(1);
	trie[1].fail=0;
	while(!q.empty()){
		int u=q.front();
		q.pop();
		for(int i=0;i<26;i++){
			int v=trie[u].son[i];
			int Fail=trie[u].fail;
			if(!v){
				trie[u].son[i]=trie[Fail].son[i];
				continue;
			}
			trie[v].fail=trie[Fail].son[i];
			q.push(v);
		}
	}
}

void query(string s){
	int u=1,len=s.length();
	for(int i=0;i<len;i++){
		int v=s[i]-'a';
		int k=trie[u].son[v];
		while(k>1){ //可重复遍历 
			if(trie[k].flag){
				ans[trie[k].flag]++; //记录出现次数 
				mx=max(mx,ans[trie[k].flag]);	
			}
			k=trie[k].fail;
		}
		u=trie[u].son[v];
	}
}

int main(){
	string s[maxn];
	scanf("%d",&n);
	while(n!=0){
		for(int i=1;i<=cnt;i++){
			for(int j=0;j<26;j++){
				trie[i].son[j]=0;
			}
			trie[i].flag=0;
			trie[i].fail=0;
		}cnt=1;
		mx=0;
		for(int i=1;i<=n;i++){
			ans[i]=0;
		}
		
		for(int i=1;i<=n;i++){
			cin>>s[i];
			insert(s[i],i);
		}
		getFail();
		string T;
		cin>>T;
		query(T);
		
		printf("%d\n",mx);
		for(int i=1;i<=n;i++){
			if(ans[i]==mx) cout<<s[i]<<'\n';
		}
		
		scanf("%d",&n);
	} 
}
```

求每个模式串在文本串中出现的次数：

[P5357 【模板】AC 自动机](https://www.luogu.com.cn/problem/P5357)

上一题的思路在该题会超时，所以使用了拓扑排序优化，不再暴力跳Fail，让程序的Trie上每个点只经过一次。

```cpp
//洛谷P5357完整代码 
#include<iostream>
#include<cstring>
#include<queue>
using namespace std;
const int maxn=2e5+5;
const int maxs=2e5+5;

struct Node{
	int son[26],ans,fail;
}trie[maxs];

int n,cnt=1;
int flag[maxn],in[maxn],vis[maxn]; 
queue<int> q;

int insert(string s){
	int u=1,len=s.length();
	for(int i=0;i<len;i++){
		int v=s[i]-'a';
		if(!trie[u].son[v]) trie[u].son[v]=++cnt;
		u=trie[u].son[v];
	}
	return u; //题目前提：不保证任意两个模式串不相同
}

void getFail(){
	for(int i=0;i<26;i++) trie[0].son[i]=1;
	q.push(1);
	trie[1].fail=0;
	while(!q.empty()){
		int u=q.front();
		q.pop();
		for(int i=0;i<26;i++){
			int v=trie[u].son[i];
			int Fail=trie[u].fail;
			if(!v){
				trie[u].son[i]=trie[Fail].son[i];
				continue;
			}
			trie[v].fail=trie[Fail].son[i]; //将fail指针视作有向边，构成DAG 
			in[trie[v].fail]++; //更新入度 
			q.push(v);
		}
	}
}

void query(string s){
	int u=1,len=s.length();
	for(int i=0;i<len;i++){
		int v=s[i]-'a';
		u=trie[u].son[v];
		trie[u].ans++; //标记 
	}
}

void topu(){
	for(int i=1;i<=cnt;i++){
		if(in[i]==0) q.push(i);
	}
	while(!q.empty()){
		int u=q.front();
		q.pop();
		vis[u]=trie[u].ans; //记录答案 
		trie[trie[u].fail].ans+=vis[u]; //标记向上传 
		in[trie[u].fail]--; //更新入度 
		if(in[trie[u].fail]==0) q.push(trie[u].fail);
	}
}

int main(){
	scanf("%d",&n);
	string s;
	for(int i=1;i<=n;i++){
		cin>>s;
		flag[i]=insert(s); //多个串可能映射到同一个点 
	}
	getFail();
	cin>>s;
	query(s);
	topu(); //拓扑排序优化 
	
	for(int i=1;i<=n;i++){
		printf("%d\n",vis[flag[i]]);
	}
}
```