## 基础概念

### 整除

#### 定义

若对于 $a,b\in\Bbb{Z},a\ne 0$，$\exist x\in\Bbb{Z},b=ax$，我们称 $b$ 能被 $a$ **整除**，记作 $a\mid b$。反之则 $b$ 不被 $a$ 整除，记作 $a{\;\mathrlap{\kern{-.11em}/}{\mid}\;}b$。

注：$\KaTeX$ 中没有不整除号，只有 $\nshortmid$，十分不美观，故笔者合成了一个 ${\;\mathrlap{\kern{-.11em}/}{\mid}\;}$，`${\;\mathrlap{\kern{-.11em}/}{\mid}\;}$`。其实可以用 $\not{\mid}$。

#### 性质

以下名字都是我乱起的。

- 与符号无关：$a\mid b\iff a\mid -b\iff -a\mid b\iff -a\mid -b\iff |a|\mid |b|$；
- 传递性：$a\mid b\land b\mid c\implies a\mid c$；
- 线性组合：$a\mid b\land a\mid c\iff \forall x,y\in\Bbb{Z},a\mid(ax+by)$；
- 互相整除：$a\mid b\land b\mid a\implies a=\pm b$
- 可约（乘）性：$\forall m\in\Bbb{Z},m\ne 0,a\mid b\iff am\mid bm$；
- 可减（加）性：$a\ne 0,q\in\Bbb{Z},b=ax+c$，$a\mid b\iff a\mid c$。

### 约数 & 倍数

#### 定义

若 $a\mid b$，则 $a$ 是 $b$ 的**约数（因数）**，$b$ 是 $a$ 的**倍数**。

$0$ 是所有非 $0$ 整数的倍数。对于 $b\ne 0$：

$\pm 1,\pm b$ 是 $b$ 的**平凡约数（平凡因数）**。其他则称为**真约数（真因数、非平凡约数、非平凡因数）**。

一般只讨论正约数。

### 带余除法

这里只讨论最小非负余数带余除法。

#### 定义

对于 $a\ne 0,b$，必然 $\exist x,r$，$b=ax+r\land 0\le r<|a|$，我们称 $r$ 为**余数**，上述过程为**带余除法**。

#### 性质

余数 $r$ 满足 $r\in\{0,1,\dots,a\}$。相邻 $a$ 个数被 $a$ 除后余数恰好组成上述集合。

### 素数 & 合数

#### 定义

设整数 $p\ne 0,\pm 1$。如果 $p$ 除了平凡约数外没有其他约数，那么称 $p$ 为**素数**，反之为**合数**。

一般只讨论正素数。

#### 性质

- $a$ 为合数等价于 $a=xy,1<x,y<a$；
- 如果素数 $p$ 有一约数 $d>1$，那么 $d=p$；
- 整数 $a>1$ 一定可以表示为素数的乘积。
- 对于合数 $a$ 一定存在素数 $p\le\sqrt{a}$ 使得 $p\mid a$。
- 素数有无穷多个。
- 所有大于 $3$ 的素数都可以表示为 $6n\pm 1$ 的形式。

### 最大公约数 & 最小公倍数

#### 定义

一组整数的**公约数**整除组中所有整数。**最大公约数**为最大的公约数，记作 $\gcd(a_1,\dots,a_n)$。

一组整数的**公倍数**被组中所有整数整除。**最小公倍数**为最小的公倍数，记作 $\operatorname{lcm}(a_1,\dots,a_n)$。

#### 性质

由算术基本定理，显然有一堆性质。

### 算术基本定理

#### 算术基本引理

设素数 $p$，$p\mid a_1a_2 \implies p\mid a_1\lor p\mid a_2$。

#### 算术基本定理

**算术基本定理**也称**唯一分解定理**，其指出对于任意正整数 $a$，必然有表示

$$
a=\prod_{i=1}^{n}p_i\tag{1}
$$

其中 $p_i$ 均为素数。它们组成的多重集对于 $a$ 唯一。

#### 标准素因数分解式

将相同的素数合并，可得

$$
a=\prod_{i=1}^{n}p_i^{\alpha(i)},\;\forall 1\le i<n,p_i<p_{i+1}\tag{2}
$$

上式称为正整数 $a$ 的**标准素因数分解式**。

### 同余

#### 定义

对于整数 $m\ne 0$，若 $m\mid (a-b)$，则 $m$ 称为**模数**，$a$ **同余于** $b$ 模 $m$，$b$ 是模 $m$ 意义下的**剩余**。记作 $a\equiv b\pmod m$

一般的，只讨论 $m$ 为正整数，$b$ 为最小非负整数的情形。

类似等于，同余为等价关系，具有自反性、对称性、传递性，并可以左右同时进行线性运算。

<!--
### 同余类 & 剩余系

#### 定义

为方便讨论，对集合 $A,B$ 和元素 $r$，我们引入如下记号：

- $r+A:=\{r+a:a\in A\}$；
- $rA:=\{ra:a\in A\}$；
- $A+B:=\{a+b:a\in A,b\in B\}$；
- $AB:=\{ab:a\in A,b\in B\}$。

对非零整数 $m$，把全体整数分成 $|m|$ 个两两不交的集合，且同一个集合中的任意两个数模 $m$ 均同余，我们把这 $|m|$ 个集合均称为模 $m$ 的**同余类**或**剩余类**。

用 $r\bmod m$ 表示含有整数 $r$ 的模 $m$ 的同余类。可以证明对任意非零整数 $m$，上述划分方案一定存在且唯一。

#### 性质

- $r\bmod m=\{r+km:k\in\mathbb{Z}\}$；
- $r\bmod m=s\bmod m\iff r\equiv s\pmod m$；
- 对于任意 $r,s\in\mathbb{Z}$，要么 $r\bmod m=s\bmod m$，要么 $(r\bmod m)\cap (s\bmod m)=\varnothing$；
- 若 $m_1\mid m$，则对任意整数 $r$ 均有 $r+m\mathbb{Z}\subseteq r+m_1\mathbb{Z}$。
 -->

## 参考

- <https://oi-wiki.org/math/number-theory/basic/>