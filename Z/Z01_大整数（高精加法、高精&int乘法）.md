## 大整数（高精加法、高精与int乘法）
```cpp
#define maxn 500
struct Bigint{
	int a[maxn],len;
	Bigint(int x=0){
		memset(a,0,sizeof(a));
		for(len=1;x;len++){
			a[len]=x%10;
			x/=10;
		}
		len--;
	}
	int&operator[](int x){
		return a[x];
	}
	void flatten(int L){
		len=L;
		for(int i=1;i<=L;i++){
			a[i+1]+=a[i]/10;
			a[i]%=10;
		}
		while(!a[len]) len--;
	}
	void print(){
		for(int i=max(1,len);i>=1;i--) cout<<a[i];
	}
};
Bigint operator+(Bigint a,Bigint b){
	Bigint c;
	int len=max(a.len,b.len);
	for(int i=1;i<=len;i++){
		c[i]+=a[i]+b[i];
	}
	c.flatten(len+1);
	return c;
}
Bigint operator*(Bigint a,int b){
	Bigint c;
	int len=a.len;
	for(int i=1;i<=len;i++){
		c[i]=a[i]*b;
	}
	c.flatten(len+11);
	return c;
}
```