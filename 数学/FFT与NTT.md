## FFT

```c++
const double PI=acos(-1);
int lena,lenb;
struct Complex{
	double x,y;
	Complex operator+(const Complex& t)const{
		return {x+t.x,y+t.y};
	}
	Complex operator-(const Complex& t)const{
		return {x-t.x,y-t.y};
	}
	Complex operator*(const Complex& t)const{
		return {x*t.x-y*t.y,x*t.y+y*t.x};
	}
}a[N],b[N];
int rev[N],bit,tot,ans[N];
void FFT(Complex* a,int inv){
	for(int i=0;i<tot;i++)
		if(i<rev[i])
			swap(a[i],a[rev[i]]);
	for(int mid=1;mid<tot;mid<<=1){
		auto w1=Complex({cos(PI/mid),inv*sin(PI/mid)});
		for(int i=0;i<tot;i+=mid*2){
			auto wk=Complex({1,0});
			for(int j=0;j<mid;j++,wk=wk*w1){
				auto x=a[i+j],y=wk*a[i+j+mid];
				a[i+j]=x+y,a[i+j+mid]=x-y;
			}
		}
	}
}
void poly_mul(int* s,int* t,int len){//len是lena+lenb+1
    for(int i=0;i<=lena;i++)
        a[i].x=s[i],a[i].y=0;
    for(int i=0;i<=lenb;i++)
        b[i].x=t[i],b[i].y=0;
	bit=0;
	while((1<<bit)<len) bit++;
	tot=1<<bit;
    for(int i=lena+1;i<tot;i++)
        a[i].x=a[i].y=0;
    for(int i=lenb+1;i<tot;i++)
        b[i].x=b[i].y=0;
	for(int i=0;i<tot;i++)
		rev[i]=(rev[i>>1]>>1)|((i&1)<<(bit-1));
	FFT(a,1),FFT(b,1);
	for(int i=0;i<tot;i++) a[i]=a[i]*b[i];
	FFT(a,-1);
	for(int i=0;i<len;i++)
		ans[i]=(int)(a[i].x/tot+0.5);
}
```



## NTT

```c++
const int mod=998244353,G=3,Gi=332748118; // 1004535809 这个数的原根也是3 Gi就是G的逆元
int lena,lenb;
int limit=1;
int L;
int R[N];
int A[N],B[N];
void NTT(int* A,int type){
	for(int i=0;i<limit;i++)if(i<R[i])swap(A[i],A[R[i]]);
	for(int mid=1;mid<limit;mid<<=1){
		int wn=qmi(G,(mod-1)/(mid*2));
		if(type==-1) wn=qmi(wn,mod-2);
		for(int len=mid<<1,pos=0;pos<limit;pos+=len){
			int w=1;
			for(int k=0;k<mid;k++,w=1ll*w*wn%mod){
				int x=A[pos+k],y=1ll*w*A[pos+mid+k]%mod;
				A[pos+k]=(x+y)%mod;
				A[pos+k+mid]=(x-y+mod)%mod;	
			}
		}
	}	
	if(type==-1){
		int limit_inv=qmi(limit,mod-2);
		for(int i=0;i<limit;i++) A[i]=1ll*A[i]*limit_inv%mod;
	}
}//代码实现上和FFT相差无几
void poly_mul(int* A, int* B,int deg){//a是0~lena次的数组,b是0~lenb次的数组,deg是lena+lenb
	for(limit=1,L=0;limit<=deg;limit<<=1)L++;
	for(int i=lena+1;i<limit;i++) A[i]=0;
	for(int i=lenb+1;i<limit;i++) B[i]=0;
	for(int i=0;i<limit;i++)R[i]=(R[i>>1]>>1)|((i&1)<<(L-1));
	NTT(A,1);
	NTT(B,1);
	for(int i=0;i<limit;i++)A[i]=1ll*A[i]*B[i]%mod;
	NTT(A,-1);
	//答案存储在A数组中
}
```

## 分治NTT/FFT

![image-20230806215919000](https://cartoonwqy.oss-cn-nanjing.aliyuncs.com/2023/08/90619d39f2b2af98736fac874f118ab6.png)

```cpp
#include <bits/stdc++.h> 
using namespace std; 
const int N = 3e5 + 7, mod = 998244353, P = 998244353;

int n, m, s, t, k, a[N];
int limit, L, RR[N], G = 3;
int ans[N], f[N], g[N];

int qpow(int a, int b)
{
	int ans = 1;
	while(b) {
		if(b & 1) ans = 1ll * ans * a % mod;
		a = 1ll * a * a % mod;
		b >>= 1;
	}
	return ans % mod;
} 

void NTT_init(int limit)
{
	for (int i = 0; i < limit; ++ i)
		RR[i] = (RR[i >> 1] >> 1) | ((i & 1) ? limit >> 1 : 0);
}

void NTT(int *A, int limit, int type)
{	
	for (int i = 0; i < limit; ++ i) 
		if(i < RR[i]) 
			swap(A[i], A[RR[i]]);

	for (int mid = 1; mid < limit; mid <<= 1) {
		int wn = qpow(G, (mod - 1) / (mid * 2));
		if(type == -1) wn = qpow(wn, mod - 2);
		
		for (int len = mid << 1, pos = 0; pos < limit; pos += len) {
			int w = 1;
			for (int k = 0; k < mid; ++ k, w = (1ll * w * wn) % mod) {
				int x = A[pos + k], y = 1ll * w * A[pos + mid + k] % mod;
				A[pos + k] = (1ll * x + y) % mod;
				A[pos + k + mid] = (1ll * x - y + mod) % mod; 
			}
		}
	}
	if(type == -1) {
		int inv = qpow(limit, mod - 2);
		for (int i = 0; i < limit; ++ i) {
			A[i] = (1ll * A[i] * inv) % mod;
		}
	}
}

void cdq_fft(int l, int r)
{
	if(l + 1 >= r) {
		return ;
	}
	int mid = l + ((r - l) >> 1);
	cdq_fft(l, mid);
	int len = r - l; 

	NTT_init(len);

	for (int i = 0; i < len; ++ i) g[i] = a[i]; 
	for (int i = l; i < mid; ++ i) f[i - l] = ans[i];
	for (int i = mid; i < r; ++ i) f[i - l] = 0;
	
	NTT(f, len, 1), NTT(g, len, 1);
	for (int i = 0; i < len; ++ i)
		f[i] = 1ll * f[i] * g[i] % mod;
	NTT(f, len, -1);    
	for (int i = mid; i < r; ++ i) {
		ans[i] = (1ll * ans[i] + 1ll * f[i - l] % mod) % mod;
	}
		
	cdq_fft(mid, r); 
}

int main()
{
	scanf("%d", &n);
	for (int i = 1; i < n; ++ i) {
		scanf("%d", &a[i]);
	}
	limit = 1;
	while(limit < n) limit <<= 1, L ++ ;
	ans[0] = 1;
	cdq_fft(0, limit);

	for (int i = 0; i < n; ++ i)
		printf("%d ", ans[i]);
	return 0;
}
```

