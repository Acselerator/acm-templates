# KMP

**模式串匹配**，就是给定一个需要处理的**文本串**（主串，往往长）和一个需要在文本串中搜索的**模式串**（往往短），查询在该文本串中，给出的模式串的出现有无、次数、位置等。

$KMP$的精髓在于，对于每次失配之后，我都不会从头重新开始枚举，而是根据我已经得知的数据，从某个特定的位置开始匹配；而对于模式串的每一位，都有唯一的“特定变化位置”，这个在失配之后的特定变化位置可以帮助我们利用已有的数据不用从头匹配，从而节约时间。

我们的失配数组$next$应当建立在模式串范畴下，而不是文本串范畴下。$next$数组记录模式串到它为止的前缀的真前缀和真后缀最大相同的数目。

定义一个字符串$s$的 border 为$s$的一个非$s$本身的子串$t$，满足$t$既是$s$的前缀，又是$s$的后缀。

[【neko算法课】KMP算法【7期】](https://www.bilibili.com/video/BV1234y1y7pm/?spm_id_from=333.337.search-card.all.click&vd_source=bfccbbb83751e2f1da3ff806b081a1c4)

![KMP比对](https://i0.hdslb.com/bfs/new_dyn/d25348adc4d79f1ac2f292274c5e9e74394677100.png@1052w_!web-dynamic.avif)

![KMP构造](https://i0.hdslb.com/bfs/new_dyn/44590cb5ab9a367132a2910c1a928ec6394677100.png@1052w_!web-dynamic.avif)

[P3375 【模板】KMP](https://www.luogu.com.cn/problem/P3375)

```cpp
//洛谷P3375 完整代码 
#include<iostream>
using namespace std;
typedef long long ll;
const int maxn=1e6+5;

string s1,s2;
int j,nxt[maxn];
/*
注意：在构造next数组和与文本串比对两个环节中
i+1也可以写为i，当然此时的循环范围要变化
两种写法的意义是相同的 ，取决于个人习惯，这里都写为i+1 
*/
int main(){
	cin>>s1>>s2;
	s1=' '+s1;
	s2=' '+s2;
	int len1=s1.length()-1;
	int len2=s2.length()-1;
	for(int i=1;i<len2;i++){//模式串自己匹配自己求出next数组
		while(j&&s2[i+1]!=s2[j+1]) //此处判断j是否为0的原因在于，如果回跳到串的起始就不用再回跳了
			j=nxt[j];
		if(s2[j+1]==s2[i+1]) j++; 
			nxt[i+1]=j;
	}
	j=0; //j可以看做表示当前已经匹配完的模式串的最后一位的位置 
	for(int i=0;i<len1;i++){
		while(j&&s2[j+1]!=s1[i+1]) //如果失配 ，那么就不断向回跳，直到可以继续匹配 
			j=nxt[j];
		if(s2[j+1]==s1[i+1]) j++; //如果匹配成功，那么对应的模式串位置++ 
		if(j==len2){
			printf("%d\n",i+1-len2+1);
			j=nxt[j]; //继续匹配 
		}
	}
	for(int i=1;i<=len2;i++){
		printf("%d ",nxt[i]);
	}
}
```

模块化代码：

```cpp
//洛谷P3375 完整代码 
#include<iostream>
using namespace std;
typedef long long ll;
const int maxn=1e6+5;

string s1,s2;
int len1,len2;
int j,nxt[maxn];

void build_nxt(){
	j=0;
	for(int i=1;i<len2;i++){
		while(j&&s2[i+1]!=s2[j+1])
			j=nxt[j];
		if(s2[j+1]==s2[i+1]) j++; 
			nxt[i+1]=j;
	}
}

void kmp_search(){
	j=0;
	for(int i=0;i<len1;i++){
		while(j&&s2[j+1]!=s1[i+1])
			j=nxt[j];
		if(s2[j+1]==s1[i+1]) j++;
		if(j==len2){
			printf("%d\n",i+1-len2+1);
			j=nxt[j];
		}
	}
}

int main(){
	cin>>s1>>s2;
	s1=' '+s1;
	s2=' '+s2;
	len1=s1.length()-1;
	len2=s2.length()-1;
	build_nxt();
	kmp_search();
	for(int i=1;i<=len2;i++){
		printf("%d ",nxt[i]);
	}
}
```

**最小循环子串**=n-next[n]：

[P4391 [BalticOI 2009] Radio Transmission 无线传输](https://www.luogu.com.cn/problem/P4391)