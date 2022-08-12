## FFT

```c++
const double PI = acos(-1);
int lena,lenb;
struct Complex{
	double x, y;
	Complex operator+ (const Complex& t) const{
		return {x + t.x, y + t.y};
	}
	Complex operator- (const Complex& t) const{
		return {x - t.x, y - t.y};
	}
	Complex operator* (const Complex& t) const{
		return {x * t.x - y * t.y, x * t.y + y * t.x};
	}
}a[N], b[N];
int rev[N], bit, tot, ans[N];
void FFT(Complex* a, int inv){
	for (int i = 0; i < tot; i ++ )
		if (i < rev[i])
			swap(a[i], a[rev[i]]);
	for (int mid = 1; mid < tot; mid <<= 1){
		auto w1 = Complex({cos(PI / mid), inv * sin(PI / mid)});
		for (int i = 0; i < tot; i += mid * 2){
			auto wk = Complex({1, 0});
			for (int j = 0; j < mid; j ++, wk = wk * w1){
				auto x = a[i + j], y = wk * a[i + j + mid];
				a[i + j] = x + y, a[i + j + mid] = x - y;
			}
		}
	}
}
void poly_mul(Complex* a, Complex* b, int len){//len是lena+lenb+1
	bit = 0;
	while ((1 << bit) < len) bit ++;
	tot = 1 << bit;
	for (int i = 0; i < tot; i ++ )
		rev[i] = (rev[i >> 1] >> 1) | ((i & 1) << (bit - 1));
	FFT(a, 1), FFT(b, 1);
	for (int i = 0; i < tot; i ++ ) a[i] = a[i] * b[i];
	FFT(a, -1);
	for (int i = 0; i < len; i ++ )
		ans[i] = (int)(a[i].x / tot + 0.5));
}
```



## NTT

```c++
const int mod=998244353,G=3,Gi=332748118;
int lena,lenb;
int limit=1;
int L;
int R[N],ans[N];
int a[N],b[N];
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
void poly_mul(int* a, int* b,int deg){//a是0~lena次的数组,b是0~lenb次的数组,deg是lena+lenb
	for(limit=1,L=0;limit<=deg;limit<<=1)L++;
	//for (int i = lena + 1; i < limit; i++) a[i] = 0;
	//for (int i = lenb + 1; i < limit; i++) b[i] = 0;
	for(int i=0;i<limit;i++)R[i]=(R[i>>1]>>1)|((i&1)<<(L-1));
	NTT(a,1);
	NTT(b,1);
	for(int i=0;i<limit;i++)a[i]=1ll*a[i]*b[i]%mod;
	NTT(a,-1);
	for(int i=0;i<=lena+lenb;i++) ans[i]=a[i];
}
```

