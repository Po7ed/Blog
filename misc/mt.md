## 前言

> 这是一篇 $\LaTeX$ PPT 改 md。

今天的题单：[点我](https://www.luogu.com.cn/training/428639)。

~~今天是我第一次上台，PPT 可能不是那么美观，~~ 如果有错误请大胆指出。

例题不会很难，请放心食用。

## 简介

#### 莫队简介

什么是莫队？

莫队是由莫涛大神提出的一种~~暴力~~区间操作算法，它框架简单、板子好写、复杂度优秀。

然而由于莫队算法应用的毒瘤，很多莫队题难度评级都很高（蓝紫黑），令很多初学者望而却步。但如果你真正理解了莫队的算法原理，它用起来还是挺简单的。

前置知识：
- 分块思想。
- `sort` 的用法（包括重载运算符或 `cmp` 函数，多关键字排序）。

#### 使用莫队的情境

若 $m=O(n)$（即 $m$、$n$ 同阶），且 **$[l,r]$ 的答案能 $O(1)$ 地转换到 $[l-1,r],[l,r+1],[l+1,r],[l,r-1]$ 区间（即相邻区间）的答案**，那么莫队可以在 $O(n\sqrt{n})$ 的时间复杂度内**离线**求出所有询问的答案。

**注意**：莫队是离线算法。如果题目强制在线，则不以可用莫队。

什么是离线、在线？
- 如果算法需要知道所有询问才能开始算法，则称此算法为离线算法。
- 读入一个询问，回答一个询问的算法叫在线算法。
- 强制在线就是要求你读入一个询问就立马回答。

#### 莫队的基本思想

离线存下所有询问，借助分块按照一定的顺序处理这些询问，使得询问之间可以互相利用（一般情况下为了方便，只会是本次询问利用上次询问的答案），以减小时间复杂度。

## 普通莫队

### 算法基础

#### 算法流程

1. 离线存下所有询问。
2. 以二元组 $(bel[l_i],r_i)$ 为关键字升序对所有询问排序。
   其中 $i$ 表示当前询问编号，$bel[l_i]$（belong，属于）表示 $l_i$ 所在的块的编号。
3. 遍历每个询问，维护两个指针 $[l,r]$ 表示当前区间，$now$ 表示当前答案。
   初始时 $l=1,r=0$（如果 $l=0$，那么我们还需要删除 $a_0$，导致一些奇怪的错误）。
   **$l,r$ 需区别于 $l_i,r_i$，它们一对是我们维护的指针（下标），一对是数据给出的询问。**
4. 移动区间 $[l,r]\to [l_i,r_i]$。途中维护区间 $[l,r]$ 的答案 $now$。
5. 移动结束后，记录区间 $[l_i,r_i]$ 的答案 $ans_i$。（$ans_i\gets now$）。


#### 算法代码：[洛谷 P3901 数列找不同](https://www.luogu.com.cn/problem/P3901)（模板题）

```cpp
constexpr int N=114514;
int n,m,a[N],S;// S：块长
// [1,S] 区间的块编号为 1，[S+1,2S] 区间的块编号为 2，以此类推。
inline int bel(int x){return (x-1)/S+1;}
struct query// 询问结构体
{
	int l,r,id;// 分别为每个询问区间的左端点、右端点、询问的编号。
	friend inline bool operator < (query x,query y)
	{return (bel(x.l)==bel(y.l)?x.r<y.r:bel(x.l)<bel(y.l));}
};
query q[N];// 查询数组
bitset<N> ans;// 答案数组
// cnt[i]：i 这个数在当前区间 [l,r] 出现次数，cf：重复出现的数的数量。
// 如果 cf=0，[l,r] 中没有重复出现的数。
int cnt[N],cf=0;
// 移动区间
inline void add(int pos)// 添加 a[pos]
{
	cnt[a[pos]]++;// 将 a[pos] 的出现次数 +1。
	if(cnt[a[pos]]==2)cf++;// 如果已经出现两次，则重复了，cf++。
}
inline void del(int pos)// 删除 a[pos]
{
	cnt[a[pos]]--;// 将 a[pos] 的出现次数 -1。
	if(cnt[a[pos]]==1)cf--;// 如果当前只出现一次，则之前一定重复（出现两次），
	// 而现在不重复了，cf--。
}

void mt()
{
	S=int(ceil(pow(n,0.5)));// S=sqrt(n)，根号分块
	sort(q+1,q+m+1);// 结构体排序
	for(int i=1,l=1,r=0;i<=m;i++)// 遍历每个询问
	{
		#define q q[i]// 偷懒
		while(q.l<l)add(--l);// 向左扩展 l-1
		while(r<q.r)add(++r);// 向右扩展 r+1
		while(l<q.l)del(l++);// 向右删除 l
		while(q.r<r)del(r--);// 向左删除 r
		// 注意上面四句的顺序，需要先扩展在删除。
		// 同时注意自减自加运算符是前置还是后置。
		ans[q.id]=!cf;// 记录当前答案
		#undef q
	}
}

int main()
{
	// input
	mt();// 莫队
	for(int i=1;i<=m;i++)puts(ans[i]?"Yes":"No");// 输出
	return 0;
}
```


#### 算法复杂度

下面的讨论中 $m=O(n)$。

单次移动 $l,r$ 中的一个复杂度显然 $O(1)$。

考虑 $l,r$ 分别移动的次数。

- 考虑 $l$：设块 $i$ 内的询问的左端点个数为 $x_i$，则块 $i$ 中移动 $l$ 的次数顶多 $x_i\times\sqrt{n}$。一共 $\sqrt{n}$ 个块，移动 $l$ 的总时间复杂度为：
$$\begin{aligned}
	&\sum_{i=1}^{\sqrt{n}} \left(x_i\times\sqrt{n}\right)\\
	=&\left(\sqrt{n}\right)\times\sum_{i=1}^{\sqrt{n}} \left(x_i\right)\\
	=&\sqrt{n}\times m\\
	=&O(n\sqrt{n})
\end{aligned}$$
- 考虑 $r$：每块内的 $x_i$ 个 $l_j(1\le j\le x_i)$ 肯定一一对应着 $x_i$ 个 $r_j$。
  显然这 $x_i$ 个 $r_j$ 最多会使 $r$ 移动 $n$ 的长度（$l_j$ 同一块时，按 $r_j$ 升序，故不降）。
  一共 $\sqrt{n}$ 个块，移动 $r$ 的总时间复杂度为：$\sqrt{n}\times n=O(n\sqrt{n})$。
- 则总时间复杂度为 $O(1)\times [O(n\sqrt{n})+O(n\sqrt{n})]=O(n\sqrt{n})$。



### 算法优化

#### 奇偶性排序

刚才的复杂度分析中提出了一些极端情况：$x_i$ 个 $r_j$ 最多使 $r$ 移动 $n$ 的长度。

而 $\sqrt{n}$ 个块都可能使 $r$ 移动 $n$ 的长度，例如下列询问（$n=100,S=10$）：

```text
1 1
10 100
11 1
20 100
```

按原先的排序策略，$r$ 需要反复横跳，十分浪费时间。

如果将处理顺序改为以下顺序将大大加速：

```text
1 1
10 100
20 100
11 1
```

怎么修改排序策略？

**依然以 $bel[l_i]$ 为第一关键字升序排序，若 $bel[l_i]$ 为奇数，以 $r_i$ 为第二关键字升序排序，反之若 $bel[l_i]$ 为偶数，以 $r_i$ 为第二关键字降序排序。**

> 这样我们的 $r$ 指针在处理完这个奇数块的问题后，将在返回的途中处理偶数块的问题，再向 $n$ 移动处理下一个奇数块的问题，优化了 $r$ 指针的移动次数，一般情况下，这种优化能让程序快 $30\%$ 左右。
> 
> ——OI-Wiki。


实测：[$810$ ms](https://www.luogu.com.cn/record/137463623) $\to$ [$622$ ms](https://www.luogu.com.cn/record/137467226)，快约 $23.2\%$。


#### 奇偶性排序：代码

```cpp
struct query
{
	int l,r,id;
	friend inline bool operator < (query x,query y)
	{
		if(bel(x.l)!=bel(y.l))return bel(x.l)<bel(y.l);
		if(bel(x.l)&1)return x.r<y.r;
		else return x.r>y.r;
	}
};
```

坑点：注意重载运算符不能出现 $a<b,a>b$ 同时为真的情况，否则会 RE。如以下实现方式就是错误的：

```cpp
struct query
{
	int l,r,id;
	friend inline bool operator < (query x,query y)
	{
		if(bel(x.l)!=bel(y.l))return bel(x.l)<bel(y.l);
		return (bel(x.l)&1)==(x.r<y.r);// 这里会出错
	}
};
```

#### 常数级展开

发现 `add()`，`del()` 两个函数可以压行并展开到 `mt()` 中。

这看似鸡肋的优化实测 [$572$ ms](https://www.luogu.com.cn/record/137468354)——又优化了 $50$ ms。

代码：

```cpp
void mt()
{
	S=int(ceil(pow(n,0.5)));
	sort(q+1,q+m+1);
	for(int i=1,l=1,r=0;i<=m;i++)
	{
		#define q q[i]
		// 压行并展开：
		while(q.l<l)cf+=(++cnt[a[--l]]==2);// 与 add(--l) 等价
		while(r<q.r)cf+=(++cnt[a[++r]]==2);// 与 add(++r) 等价
		while(l<q.l)cf-=(--cnt[a[l++]]==1);// 与 del(l++) 等价
		while(q.r<r)cf-=(--cnt[a[r--]]==1);// 与 del(r--) 等价
		ans[q.id]=!cf;
		#undef q
	}
}
```


#### 玄学剪枝

我考虑到有时候可能转移大半天还不如暴力重新算，所以想出了一个玄学剪枝：

```cpp
// 如果转移代价大于重新算的代价
if(abs(q.l-l)+abs(q.r-r)>(r-l+1)+(q.r-q.l+1))
{
	while(l<=r)cf-=(--cnt[a[r--]]==1);// 清除
	r=(l=q.l)-1;// 直接跳转
}
```

（这段语句加在 `#define q q[i]` 后面。）

没想到只优化了 $1$ ms（悲。也许是每次都判断的代价太大，抵消了直接跳转的优化。

#### 预处理 $bel$

我写好几题才发现其他大佬都预处理了 $bel$，于是赶紧改过来。快了 [$58$ ms](https://www.luogu.com.cn/record/138365971)。

```cpp
int S;int bel[N];
inline void initbel()// 1~S: 1
{
	S=int(ceil(pow(n,0.5)));// S=sqrt(n)
	for(int i=1;i<=n;i++)bel[i]=(i-1)/S+1;
}
...
void mt()
{
	initbel();
	sort(q+1,q+m+1);
	...
}
```


### 例题

#### 例题：[DQUERY - D-query](https://www.luogu.com.cn/problem/SP3267)

简要题意：给出一个长度为 $n$ 的数列 $a$，$m$ 个询问，每次询问给出数对 $l,r$ 表示查询区间 $[l,r]$ 中有多少不同的数。

数据范围：$n\le 3\times10^5,m\le2\times10^5,a_i\le10^6$。

**板子题，难度在于如何 $O(1)$ 转移答案。**

考虑用数组 $cnt_i$ 表示 $[l,r]$ 中 $i$ 出现了几次，变量 $now$ 表示 $[l,r]$ 中有多少不同的数。

要添加 $a_{pos}$，那么 $cnt[a_{pos}]\gets cnt[a_{pos}]+1$。

此时若 $cnt[a_{pos}]=1$，即多了一个不同的数，那么 $now\gets now+1$。

同理删除 $a_{pos}$ 时 $cnt[a_{pos}]\gets cnt[a_{pos}]-1$，若 $cnt[a_{pos}]=0$（少了一个数），$now\gets now-1$。

其他的正常地跑莫队即可。*但此题似乎卡常。*


#### 例题：[P2709 小B的询问](https://www.luogu.com.cn/problem/P2709)

简要题意：给出一个长度为 $n$ 的数列 $a$（值域 $[1,k]$），$m$ 个询问，每次询问给出数对 $l,r$ 表示查询：  

$$\sum\limits_{i=1}^k c_i^2$$

其中 $c_i$ 表示数字 $i$ 在 $[l,r]$ 中的出现次数。  

数据范围：$1\le n,m,k \le 5\times 10^4$。

**难度依然在于如何 $O(1)$ 转移答案。**

$c$ 数组很好维护，但答案（设它为 $s$）就不那么好维护了。

由于每次添加或删除数时只会改变 $c_{a[pos]}$，而且只会 $\pm1$。所以：

由

$$
s=\sum\limits_{i=1}^k c_i^2=c_1^2+\cdots+c_{a[pos]}^2+\cdots+c_k^2
$$

可得

$$
\begin{aligned}
	s'&=c_1^2+\cdots+(c_{a[pos]}\pm 1)^2+\cdots+c_k^2\\
	&=c_1^2+\cdots+c_{a[pos]}^2\pm2\times c_{a[pos]}+1+\cdots+c_k^2\\
	&=s\pm2\times c_{a[pos]}+1
\end{aligned}
$$

转移时修改即可。

#### 例题：[P5268 [SNOI2017] 一个简单的询问](https://www.luogu.com.cn/problem/P5268)

简要题意：给出一个长度为 $n$ 的数列 $a$（值域 $[1,n]$），$m$ 个询问，每次询问给出数对 $l_1,r_1,l_2,r_2$ 表示查询：

$$
\sum\limits_{x=0}^\infty \text{get}(l_1,r_1,x)\times \text{get}(l_2,r_2,x)
$$

$\text{get}(l,r,x)$ 表示区间 $[l,r]$ 中 $x$ 的出现次数。

数据范围：$n,m\le 5\times 10^4,1\le a_i\le n$。

首先因为每次询问给出的是一个四元组，所以莫队不能直接做。

考虑对询问进行拆分。

可以发现，$\text{get}(l,r,x)=\text{get}(1,r,x)-\text{get}(1,l-1,x)$。展开：
（为方便，记 $\text{g}(p)=\text{get}(1,p,x)$）

$$
\begin{aligned}
	&\sum\limits_{x=0}^\infty
	\text{get}(l_1,r_1,x)\times\text{get}(l_2,r_2,x)\\
	=&\sum\limits_{x=0}^\infty
	\big(\text{g}(r_1)-\text{g}(l_1-1)\big)\times
	\big(\text{g}(r_2)-\text{g}(l_2-1)\big)\\
	=&\sum\limits_{x=0}^\infty
	\text{g}(r_1)\times\text{g}(r_2)-\text{g}(l_1-1)\times\text{g}(r_2)\\
	&-\text{g}(l_2-1)\times\text{g}(r_1)+\text{g}(l_1-1)\times\text{g}(l_2-1)
\end{aligned}
$$

若记 $\text{ask}(l,r)=\text{g}(l)\times\text{g}(r)$，

$$
\text{原式}=\text{ask}(r_1,r_2)-\text{ask}(l_1-1,r_2)-\text{ask}(l_2-1,r_1)+\text{ask}(l_1,l_2)
$$

肉眼可见，我们将原来的四元询问转换为四个二元询问。虽然 $l,r$ 并不表示区间，但我们可以用相同的思想处理询问。

如何 $O(1)$ 转移 $\text{ask}(l,r)$？
考虑若其中一个（$\text{g}(l),\text{g}(r)$）增加 1，则它们的积会增加另一个数。具体见代码。

核心代码：

```cpp
// cl[i]: [1,l] 中 i 的个数；cr[i]: [1,r] 中 i 的个数；now: sum(cl[i]*cr[i])
int cl[N],cr[N],now,ans[N];
void mt()
{
	init();
	sort(q+1,q+(m<<2)+1);// 原来的一个询问拆成四个，所以 <<2
	for(int i=1,l=0,r=0;i<=(m<<2);i++)
	{
		#define q q[i]
		while(l<q.l)++cl[a[++l]],now+=cr[a[l]];// 相应的转移
		while(r<q.r)++cr[a[++r]],now+=cl[a[r]];
		while(q.l<l)--cl[a[l]],now-=cr[a[l--]];
		while(q.r<r)--cr[a[r]],now-=cl[a[r--]];
		ans[q.id]+=(q.t*now);
		#undef q
	}
}

int main()
{
	...
	for(int i=1,l1,r1,l2,r2,p=1;i<=m;i++)
	{
		scanf("%d %d %d %d",&l1,&r1,&l2,&r2);
		q[p++]={min(l1-1,l2-1),max(l1-1,l2-1),i,1 };// 拆开
		q[p++]={min(l1-1,r2),  max(l1-1,r2),  i,-1};
		q[p++]={min(l2-1,r1),  max(l2-1,r1),  i,-1};
		q[p++]={min(r1,r2),	max(r1,r2),	i,1 };
	}
	...
	return 0;
}
```


#### 卡常：[P1972 [SDOI2009] HH的项链](https://www.luogu.com.cn/problem/P1972)

题意：同 DQUERY - D-query。但数据范围不同，还卡莫队。

我们就这么屈服了吗？没有！

其实本题莫队理论上 $n=10^6,O(n\sqrt{n})\approx 10^9$ 是可以过的，因为莫队经常跑不满。但经不住毒瘤出题人卡莫队。

考虑常数优化。注意到大部分时间花在转移 $l,r$ 指针上。

由于 $a_i$ 的波动程度可能非常大，又由于我们的转移方式是

```cpp
while(q.l<l)now+=(!(cnt[a[--l]]++));
```

访问 cnt 时下标波动程度大，导致访问慢。

如何加速？关键在于用等价但是**数组访问只与下标有关**的转移方式。因为下标经过排序后相对连续。

考虑记录两个数组 $pre_i,nxt_i$ 分别表示上一个和下一个出现 $a_i$ 的位置的下标。如添加 $l=i$ 时，$r<nxt_i$，即右边那个最近的 $a_i$ 已经不在 $[l,r]$ 中（显然左边那个更不会），那么 $[l,r]$ 中多了一个不同的数，$now\gets now+1$。其他同理。

代码：

```cpp
for(int i=1;i<=n;i++)pre[i]=lst[a[i]],lst[a[i]]=i;
fill(lst,lst+A,0x3f3f3f3f);
for(int i=n;i;i--)nxt[i]=lst[a[i]],lst[a[i]]=i;
for(int i=1,l=1,r=0;i<=m;i++)
{
	#define q q[i]
	while(q.l<l)now+=(nxt[--l]>r);
	while(r<q.r)now+=(pre[++r]<l);
	while(l<q.l)now-=(nxt[l++]>r);
	while(q.r<r)now-=(pre[r--]<l);
	ans[q.id]=now;
	#undef q
}
```


## \*带修莫队

### 简介

#### 如何实现带修莫队？

发现普通莫队不支持修改，那么如何使它支持修改操作呢？

考虑存询问时加一个变量 $t_i$ 表示**进行此询问时前面修改了几次**。同时存下每一个修改操作（无需排序）。

再新增一个指针 $t$ 表示当前区间所在的时间位置。那么移动方向就从 $4$ 个变为 $6$ 个：$[l-1,r,t],[l,r+1,t],[l+1,r,t],[l,r-1,t],[l,r,t-1],[l,r,t+1]$。新增的两个为时间轴上的移动。


### 例题

#### 例题：[P1903 \[国家集训队\] 数颜色 / 维护队列](https://www.luogu.com.cn/problem/P1903)

简要题意：给出一个长度为 $n$ 的数列，$m$ 个操作，要求支持两种操作：查询区间有多少不同的数、单点修改。

数据范围：$n,m\le 1.33333\times 10^5,a_i\le 10^6$。

板子题，直接上代码：

```cpp
constexpr int N=214514,A=1145141;// A：a 的值域
int n,m,S,qm,a[N];// qm：询问的个数
inline int bel(int x){return (x-1)/S+1;}// 分块
struct query
{
	int l,r,t,id;// 额外记录时间
	friend inline bool operator < (query x,query y)
	{//		若 l 所在块不同	按 l 的块的编号 否则 若 r 所在块不同 按 r 的块的编号 否则按时间排
		return (bel(x.l)^bel(y.l)?bel(x.l)<bel(y.l):(bel(x.r)^bel(y.r)?bel(x.r)<bel(y.r):x.t<y.t));
	}
};query q[N];
struct modify// 新增：修改操作
{int p,v;};modify mo[N];

int cnt[A],now,ans[N];// 类似于普通莫队
void mt()
{
	S=int(ceil(pow(n,0.66)));// 这里块长需要调整，具体可以可以看
	// https://oi-wiki.org/misc/modifiable-mo-algo/ 中的证明
	sort(q+1,q+qm+1);
	for(int i=1,l=1,r=0,t=0;i<=qm;i++)
	{
		#define q q[i]
		#define p mo[t].p
		#define v mo[t].v
		while(q.l<l)now+=(!(cnt[a[--l]]++));// 类似
		while(r<q.r)now+=(!(cnt[a[++r]]++));
		while(l<q.l)now-=(!(--cnt[a[l++]]));
		while(q.r<r)now-=(!(--cnt[a[r--]]));
		// 压行：先加时间 如果在当前区间内	   相应的转移	   *直接交换 v 和 a[p]
		while(t<q.t){t++;if(l<=p&&p<=r)now-=(!(--cnt[a[p]])-!(cnt[v]++));swap(a[p],v);}
		while(q.t<t){if(l<=p&&p<=r)now-=(!(--cnt[a[p]])-!(cnt[v]++));swap(a[p],v);t--;}
		ans[q.id]=now;
		#undef q
		#undef p
		#undef v
	}
}
```

## *回滚莫队

### 简介

#### 何时使用回滚莫队？

对于某些问题，普通莫队可能难以解决。原因在于删除和添加只有一个可实现。这时就需要回滚莫队了。

先看题：[AT\_joisc2014\_c 歴史の研究](https://www.luogu.com.cn/problem/AT_joisc2014_c)。

简要题意：区间询问，给出一个长度为 $n$ 的数列 $a$，$m$ 个询问，每次询问要求回答

$$
\max_{\forall x}\{x\times\operatorname{cnt}(x)\}
$$

其中 $\operatorname{cnt}(x)$，表示 $x$ 在 $[l,r]$ 区间中的出现次数。

此题添加数很方便，但删除数却很麻烦。因为当最大值改变（如变小）时，我们无法确定新的最大值。

又如求区间 $\operatorname{mex}$ 时（[P4137 Rmq Problem / mex](https://www.luogu.com.cn/problem/P4137)），删除数方便，添加数麻烦。

这时就要用到回滚莫队了。它只用删除和添加中的一个操作，剩下的就回滚解决。

### 算法实现

#### 算法流程

1. 对原序列进行分块，对询问按以 $bel[l_i]$ 升序为第一关键字，$r_i$ 升序为第二关键字排序。
2. 如果 $bel[l_i]\ne bel[l_{i-1}]$，那么将 $l$ 初始化为 $bel[l_i]$ 的右端点的后一个位置，将 $r$ 初始化为 $bel[l_i]$ 的右端点。
   此时：
   1. 若 $bel[l_i]=bel[r_i]$，直接暴力回答。
   2. 反之，
      1. 移动 $r\to r_i$。
      2. 暂存当前答案 $tmp$。
      3. 移动 $l\to l_i$。
      4. 记录答案 $ans_i\gets now$。
      5. **回滚，使 $l$ 回滚回 $bel[l_i]$ 的右端点的后一个位置，同时更新答案 $now\gets tmp$。**


#### 代码实现

```cpp
typedef long long ll;//!
constexpr int N=114514,INF=0x3f3f3f3f;
int n,m;
int a[N];
int bel[N],S;
struct query
{
	int l,r,id;
	friend inline bool operator < (query x,query y)
	{return (bel[x.l]^bel[y.l]?bel[x.l]<bel[y.l]:x.r<y.r);}
};
query q[N];
ll ans[N],now,tmp;
unordered_map<int,int> cnt,tcnt;

void rbmt()
{
	S=int(ceil(pow(n,.5)));
	for(int i=1;i<=n;i++)bel[i]=(i-1)/S+1;
	sort(q+1,q+m+1);bel[q[0].l=0]=INF;
	for(int i=1,l,r;i<=m;i++)
	{
		if(bel[q[i].l]^bel[q[i-1].l])cnt.clear(),now=-INF,r=bel[q[i].l]*S,l=r+1;
		#define q q[i]
		if(bel[q.l]==bel[q.r])
		{
			tmp=-INF,tcnt.clear();
			for(int j=q.l;j<=q.r;j++)tmp=max(tmp,1ll*(++tcnt[a[j]])*a[j]);
			ans[q.id]=tmp;
		}
		else
		{
			while(r<q.r)now=max(now,1ll*(++cnt[a[++r]])*a[r]);
			tmp=now;
			while(q.l<l)now=max(now,1ll*(++cnt[a[--l]])*a[l]);
			ans[q.id]=now;
			now=tmp;// rollback
			while(l<=bel[q.l]*S)--cnt[a[l++]];
		}
		#undef q
	}
}

int main()
{
	scanf("%d %d",&n,&m);
	for(int i=1;i<=n;i++)scanf("%d",a+i);
	for(int i=1;i<=m;i++)scanf("%d %d",&q[i].l,&q[i].r),q[i].id=i;
	rbmt();
	for(int i=1;i<=m;i++)printf("%lld\n",ans[i]);
	return 0;
}
```

## 莫队延迟更新答案

### 出处

该方法整理自：

- [「一叶知秋。」大佬的洛谷题解](https://www.luogu.com.cn/blog/wyz123321/solution-p4137)
- [「qwaszx」大佬的洛谷题解](https://www.luogu.com.cn/blog/qwaszx/solution-p5906)
- [「一扶苏一」大佬的洛谷题解](https://www.luogu.com.cn/blog/fusu2333/solution-p3834)

也许还有。我看到的就这些。

其实就是**普通**莫队的升级版。

### 算法思想 & 实现

对莫队算法进行观察，不难发现移动 $l,r$ 指针过程中的答案我们并不需要，于是就不一定要 $O(1)$ 移动指针并更新答案。只需要能 $O(1)$ 移动指针，并记录修改，移动 $l,r$ 结束后再 $\le O(\sqrt{n})$ 地更新答案即可。

这个寄巧很早就出现了，但无人详细记载，故记之。

更具体的，考虑 [P4137 Rmq Problem / mex](https://www.luogu.com.cn/problem/P4137) 这题。

此题 $n,m,a_i\le 2\times 10^5$，我们可以进行值域分块，开个桶，$cnt_i$ 表示 $i$ 在 $[l,r]$ 中出现次数（而非是否出现，方便维护）。每个块 $S$ 维护块内的数在 $[l,r]$ 中出现的个数，即

$$
exi_S=\sum_{i\in S}[cnt_i>0]
$$

当移动指针，添加/删除数时，直接修改 $cnt$，同时修改 $exi$。不急着更新答案，但移动指针结束后，扫过值域分块，$O(\sqrt{n})$ 更新答案 $now$。具体的，$O(\sqrt{n})$ 遍历每个块 $S$，若 $exi_S=|S|$，跳到下一个块，反之 $\operatorname{mex}$ 必定在块内，再次 $O(\sqrt{n})$ 遍历每个块内元素 $i\in S$，必定 $\exist i,cnt_i=0,\forall j<i,cnt_i>0$，此时 $i$ 即为所求。

本质上，该方法就是利用莫队对答案修改 | 询问次数 $O(n\sqrt{n})|O(n)$ 的特性，调整对答案的维护复杂度，将原来维护的答案修改 | 询问复杂度 $O(1)|O(1)$ 的高要求降为 $O(1)|O(\sqrt{n})$，以适应更多题目。

但有时第一时间想到的是 $O(\sqrt{n})|O(1)$ 做法，此时就需要修改策略。

### 代码

```cpp
constexpr int N=214514,B=514;
int n,m,a[N];
struct qry{int l,r,id;};
qry q[N];

int S,bel[N];
int cnt[N],exi[B];
int ans[N];
void mt()
{
	S=int(ceil(n*pow(m,-.5)));
	for(int i=0;i<=n;i++)bel[i]=i/S+1;
	std::sort(q+1,q+m+1,[](qry x,qry y)->bool
	{
		return bel[x.l]!=bel[y.l]?
		(bel[x.l]<bel[y.l]):
		(bel[x.l]&1)==(x.r<y.r);
	});
	for(int i=1,l=1,r=0,j,k;i<=m;i++)
	{
		#define q q[i]
		while(q.l<l)l--,exi[bel[a[l]]]+=!(cnt[a[l]]++);
		while(r<q.r)r++,exi[bel[a[r]]]+=!(cnt[a[r]]++);
		while(l<q.l)exi[bel[a[l]]]-=!(--cnt[a[l]]),l++;
		while(q.r<r)exi[bel[a[r]]]-=!(--cnt[a[r]]),r--;
		for(j=1;exi[j]==S;j++);
		for(k=(j-1)*S;cnt[k];k++);
		ans[q.id]=k;
	}
}

int main()
{
	scanf("%d %d",&n,&m);
	for(int i=1;i<=n;i++)scanf("%d",a+i);
	for(int i=1;i<=m;i++)scanf("%d %d",&q.l,&q.r),q.id=i;
	mt();
	for(int i=1;i<=m;i++)printf("%d\n",ans[i]);
	return 0;
}
```

### 例题：[P5906 【模板】回滚莫队&不删除莫队](https://www.luogu.com.cn/problem/P5906)

~~对不住了，回滚莫队。~~

题意：给定一个序列，多次询问一段区间 $[l,r]$，求区间中相同的数的最远间隔距离。序列中两个元素的间隔距离指的是两个元素下标差的绝对值。

数据范围：$1\leq n,m\leq 2\cdot 10^5$，$1\leq a_i\leq 2\cdot 10^9$。

考虑普通莫队。硬伤：当删除值时，若最大值改动，难以维护。

考虑离散化后对原题值域进行分块。维护每个值在 $[l,r]$ 中的最远距离，同时维护块内距离最大值和全局最大值。此时复杂度（修改 | 询问）$O(\sqrt{n})-O(1)$。

考虑转换成 $O(\sqrt{n})-O(1)$。

「qwaszx」大佬的神之一手——再次值域分块！

刚才的对数进行值域分块就不要了，只要保存每个数在区间中最边缘的两个位置，同时分块改为对距离值域分块！具体的，$cnt_i$ 表示有多少个数满足 $[l,r]$ 内最远间隔距离为 $i$，$sum_S$ 表示块 $S$ 内 $cnt$ 之和，即 $sum_S=\sum_{j\in S}cnt_j$。

若修改最远间隔距离，直接 $O(1)$ 修改 $cnt$ 和 $sum$，而查询时则从大往小扫过值域分块，具体的，块 $T$ 为最大的一个块使得满足 $sum_T>0$，则一定 $\exist k\in S,\forall l,l>k,cnt_l=0,cnt_k>0$。**即 $k$ 本身为最远间隔距离的最大值**。

实现与 P4137 Rmq Problem / mex 类似。

### 例题：[P3834 【模板】可持久化线段树 2](https://www.luogu.com.cn/problem/P3834)

~~嘿嘿没想到吧，主席树！~~

又双㕛叒叕考虑值域分块。像上一题一样维护 $cnt,sum$，$O(1)$ 修改。$cnt_i$ 表示 $[l,r]$ 内 $i$ 的数量。

从小到大扫过每个块，记录 $sum$ 的前缀和，若前缀和 $pre_{S-1}+sum_S\ge k$，则第 $k$ 大在该区间内。从小到大扫过 $S$ 内表示的每个数，累加 $cnt$ 直到找到第 $k$ 大。

## 结束

$$\Huge
\text{Thanks!}
$$

***

#### 后记

此 PPT 是本人一边学习莫队一边写的，肯定有诸多不足，还请包容。

当然还有树上莫队、二维莫队，由于难度过高，~~我自己也不会~~，就不多做介绍了。可以通过 OI-Wiki 自学。
