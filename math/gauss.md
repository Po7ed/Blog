> In mathematics, **Gaussian elimination**, also known as **row reduction**, is an algorithm for solving systems of linear equations. It consists of a sequence of row-wise operations performed on the corresponding matrix of coefficients. This method can also be used to compute the rank of a matrix, the determinant of a square matrix, and the inverse of an invertible matrix. The method is named after Carl Friedrich Gauss (1777–1855). To perform row reduction on a matrix, one uses a sequence of elementary row operations to modify the matrix until the lower left-hand corner of the matrix is filled with zeros, as much as possible. There are three types of elementary row operations:
> 
> - Swapping two rows,
> - Multiplying a row by a nonzero number,
> - Adding a multiple of one row to another row.
> 
> Using these operations, a matrix can always be transformed into an upper triangular matrix (possibly bordered by rows or columns of zeros), and in fact one that is in row echelon form. Once all of the leading coefficients (the leftmost nonzero entry in each row) are 1, and every column containing a leading coefficient has zeros elsewhere, the matrix is said to be in reduced row echelon form. This final form is unique; in other words, it is independent of the sequence of row operations used. For example, in the following sequence of row operations (where two elementary operations on different rows are done at the first and third steps), the third and fourth matrices are the ones in row echelon form, and the final matrix is the unique reduced row echelon form.
> 
> $$
> {\displaystyle {\begin{bmatrix}1&3&1&9\\1&1&-1&1\\3&11&5&35\end{bmatrix}}\to {\begin{bmatrix}1&3&1&9\\0&-2&-2&-8\\0&2&2&8\end{bmatrix}}\to {\begin{bmatrix}1&3&1&9\\0&-2&-2&-8\\0&0&0&0\end{bmatrix}}\to {\begin{bmatrix}1&0&-2&-3\\0&1&1&4\\0&0&0&0\end{bmatrix}}}
> $$
> 
> Using row operations to convert a matrix into reduced row echelon form is sometimes called Gauss–Jordan elimination. In this case, the term Gaussian elimination refers to the process until it has reached its upper triangular, or (unreduced) row echelon form. For computational reasons, when solving systems of linear equations, it is sometimes preferable to stop row operations before the matrix is completely reduced.
>
> ...
> 
> Some authors use the term *Gaussian elimination* to refer only to the procedure until the matrix is in echelon form, and use the term Gauss–Jordan elimination to refer to the procedure which ends in reduced echelon form. The name is used because it is a variation of Gaussian elimination as described by Wilhelm Jordan in 1888. However, the method also appears in an article by Clasen published in the same year. Jordan and Clasen probably discovered Gauss–Jordan elimination independently.
> 
> [Gaussian elimination - Wikipedia](https://en.wikipedia.org/wiki/Gaussian_elimination)

维基百科镇楼。中文 OI 社区几乎没有文章讲到高斯（- 约旦）消元的本质，本文尝试说明。

### 基础知识

#### 线性方程组的矩阵表示

一个线性方程组可以由矩阵表示，更准确的，一个 $n$ 元线性方程组可以被表示为 $n\times (n+1)$ 的增广矩阵。

例如

$$
\begin{cases}
	3x+2y-z=1\\
	2x-2y+4z=-2\\
	-x+\frac{1}{2}y-z=0
\end{cases}
\to
\left[\begin{array}{ccc|c}
	3 & 2 & -1 & 1\\
	2 & -2 & 4 & -2\\
	-1 & \frac{1}{2} & -1 & 0\\
\end{array}\right]
$$

读者无需具体了解增广矩阵，直接理解成普通矩阵即可。

下面定义两种特殊的矩阵形态。

#### 特殊的矩阵形态

一个矩阵被称为**行阶梯形矩阵**，当且仅当

- 所有全 $0$ 行均在矩阵底部；
- 所有非全 $0$ 行的首项，即最左侧的非 $0$ 元素，在上一行首项的右侧。

例如：

$$
\begin{bmatrix}
	1 & a_0 & a_1 & a_2 & a_3\\
	0 & 0 & 2 & a_4 & a_5\\
	0 & 0 & 0 & 1 & a_6\\
	0 & 0 & 0 & 0 & 0
\end{bmatrix}
$$

可以发现，一个行阶梯形矩阵描述了一种变量之间的 DAG 关系。

一个矩阵被称为**行最简形矩阵**，当且仅当

- 其是行阶梯形矩阵；
- 所有非全 $0$ 行的首项均为 $1$，称为前导 $1$；
- 如果某列包含前导 $1$，那么该列其他位置均为 $0$。

可以发现，一个行最简形矩阵也描述了一种变量之间的 DAG 关系，但此 DAG 只有两层，由“自变量”和“因变量”组成。

所有不包含前导 $1$ 的列代表了自变量，所有包含前导 $1$ 的列代表了因变量。

#### 初等行变换

**初等行变换**，指

- 交换两行；
- 将整行乘上一个非 $0$ 常数；
- 将某一行乘以任意常数后加到另一行上；

#### 回到 Wikipedia

有了基础知识，现在可以了解一下上文百科的大意：

> 高斯消元是一种算法，其可通过若干初等行变换将一个矩阵转化成行阶梯形矩阵，甚至行最简形矩阵。
> 
> 一些作者称只变换为行阶梯形矩阵的算法为**高斯消元**，变换成行最简形矩阵的算法为**高斯 - 约旦消元**。

本文将介绍两种消元方式，并对比它们的异同。

### 高斯消元



### 高斯 - 约旦消元

