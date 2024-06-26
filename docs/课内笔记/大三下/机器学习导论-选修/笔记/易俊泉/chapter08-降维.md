[TOC]

# 第八章——降维

## 一、降维

### 降维定义

降维dimensionality reduction：**通过某种数学变换$z_i=g_{\theta}(x_i)$将原始高维属性空间转变为一个低维空间。**

![image-20220514151305487](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871452.png)

降维可以增加计算效率，提高计算精度。在数据压缩，噪音消除、图像识别等领域有着广泛的应用。

事实上，很多高维数据都处在一个低维超平面上，其本质维数都非常低！！！

单个人脸的近似本质维度等于4

### 降维与特征选择

:apple: **特征选择**：**直接选择特征子集**用于模型训练，保留的特征没有发生任何改变

三种技术：过滤式（与学习任务无关），包裹式（特征选择依赖于学习器），嵌入式（特征选择与模型学习融为一体，模型中暗含子集选择）

![image-20220514150016907](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871454.png)

:peach: **降维**：通过某种数学变换将原始高维属性空间转变为一个低维空间，在低维的空间表示一般是稠密的。

降维没有抛弃掉任何原始特征，**不是原始特征集的子集**

### 降维方法分类

**线性降维方法**

> **PCA(Principal Component Analysis)：主成分分析**
>
> FA(Factor Analysis) 因子分析
>
> CCA(Canonical correlation analysis)典型相关性分析

**非线性降维方法**

> Kernel PCA
>
> LLE(Locally Linear Embedding) 
>
> Isomap (Isometric mapping)
>
> t-SNE：画图降维

## 二、PCA

、![image-20220514175527182](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871455.png)

### 引入

![image-20220418162849186](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871457.png)
$$
a=\begin{bmatrix}
2\\
2
\end{bmatrix}=\begin{bmatrix}
2&2
\end{bmatrix} \begin{bmatrix}
1\\
0
\end{bmatrix} \begin{bmatrix}
1\\
0
\end{bmatrix}+\begin{bmatrix}
2&2
\end{bmatrix} \begin{bmatrix}
0\\
1
\end{bmatrix}\begin{bmatrix}
0\\
1
\end{bmatrix}\\
a=(a^Tu_1)u_1+(a^Tu_2)u_2=(a^Tv_1)v_1+(a^Tv_2)v_2\qquad \\u_1与u_2和v_1与v_2是一组正交基，表示不唯一
$$
点乘：$A\cdot B=A^TB=|A||B|\cos \theta$

假设|B|=1，则$A\cdot B=A^TB=|A|\cos\theta$

投影向量即：$|A|\cos\theta *B$

![image-20220418163337139](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871458.png)

**将数据点投影到新的低维空间**
$$
X=\begin{bmatrix}x_1,x_2,\cdots,x_m\end{bmatrix}\\
其中x_i=\begin{bmatrix}x_{i1},x_{i2},\cdots,x_{ip}\end{bmatrix}^T\\
所以X是一个p\times m的矩阵\
$$
:one: 数据居中处理：$\pmb{x_i}=\pmb{x_i-\overline{x}\qquad\overline{x}}=\frac{1}{m}\sum_{i=1}^mx_i$

> 中心化以后:$\sum_i\pmb x_i=0$

:two: 选择一组**长度为1的正交基** (𝒘𝟏, ⋯ , 𝒘𝒌)——<mark>k<p 时，就是降维处理</mark>
$$
W=\begin{bmatrix}w_1,w_2,\cdots,w_k\end{bmatrix}
$$

$$
W=\begin{bmatrix}1&0&\cdots&0\\
0&1&\cdots&0\\
\vdots&\vdots&\ddots&\vdots\\
0&0&\cdots&1
\end{bmatrix}\in R^{p\times k}
$$

:three: 点在新坐标系下(新的基向量)的坐标通过点乘表示
$$
\pmb{z_i}=(z_{1i},...,z_{ki})^T,\ z_{ji}=\pmb{w_j^Tx_i}\\
例如z_{1i}就是x_i在w_1下的坐标，z_{2i}就是x_i在w_2下的坐标\\
Z=\begin{bmatrix}z_1,\cdots,z_m\end{bmatrix}\in R^{k\times m}
$$
:four: 在新基下的数据表示（重构）
$$
\hat x_i=\overline{x}+\sum_{j=1}^kz_{ji}w_j=\overline{x}+Wz_i=\overline{x}+WW^Tx_i
$$

![image-20220514154753995](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871460.png)

举例说明：
$$
\begin{align}
x&=(x^Tu_1)u_1+(x^Tu_2)u_2+\cdots+(x^Tu_p)u_p\\
&=(x^Tv_1)v_1+(x^Tv_2)v_2+\cdots+(x^Tv_p)v_p\\
&\approx(x^Tv_1)v_1+\cdots+(x^Tv_k)v_k\\
\end{align}
$$

### PCA——主成分分析技术

PCA基本思想：**构造原始特征的一系列线性组合形成的线性无关低维特征，以去除数据的相关性，并使降维后的数据最大程度地保持原始高维数据的<mark>方差信息</mark>**。

> 右图比较好

![image-20220514160642194](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871461.png)

**最大投影方差**

> 思想：给定高维空间数据，找到低维子空间使得数据在此 空间投影后，信息（方差）能尽可能大的保持
>
> 表现度量：最大投影方差

**最小投影距离**

> 思想：给定高维空间数据，找到低维子空间使得数据在此空间投影后，投影数据与原数据之间的最小平方距离尽可 能小（尽可能重建原数据）
>
> 表现度量：最小投影距离

### 最大投影方差

第一主方向：最大方差投影方向$\pmb w_1$

第一主成分：数据在第一主方向上的投影$\pmb {w_1^Tx}$

![image-20220514161004030](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871463.png)

方差$Var(X)=E\{|X-E(x)|^2\}=E(X^2),因为进行了中心化处理$

目标函数即：
$$
w_1=\underset{||W||=1}{\text{argmax}}\frac{1}{m}\sum_{i=1}^m\{(w^Tx_i)^2\}
$$

$$
\frac{1}{m}\sum_{i=1}^m (w^T x_i)^T(w^Tx_i)=\frac{1}{m}\sum_{i=1}^m (x_i^T w)^T(x_i^Tw)=\frac{1}{m}\sum_{i=1}^m w^Tx_ix_i^Tw=w^T(\frac{1}{m}\sum_{i=1}^mx_ix_i^T)w
$$

$$
化简为w^TAw，其中A=\frac{1}{m}\sum_{i=1}^Mx_ix_i^T\in R^{p\times p}
$$

利用了$a^Tb=b^Ta$

最后有：
$$
\underset{||W||=1}{\text{argmax}}\ w^TAw\quad w^Tw=1
$$
可以构造朗格朗日函数：
$$
L(w,\alpha)=w^TAw+\alpha(1-w^Tw)\\
\frac{\partial L}{\partial w}=2Aw-2\alpha w=0\Rightarrow Aw=\alpha w
\\w^TAw=w^T\alpha w=\alpha w^Tw=\alpha
$$
所以w是A的特征向量,当w为**协方差矩阵的最大特征值对应的特征向量**时，方差α取到最大值

已经求出第一个主方向，之后的主方向：**依次与前面的主方向正交，且方差尽可能地大**

第二主成分的目标函数如下：
$$
w_2=\underset{||W||=1}{\text{argmax}}\frac{1}{m}\sum_{i=1}^m\{(w^T(x_i-w_1w_1^Tx_i))^2\}
$$

$$
\frac{1}{m}\sum_{i=1}^m(w^Tx_i-w^Tw_1w_1^Tx_i)^2\\
因为与主方向正交：w^Tw=0\Rightarrow\frac{1}{m}\sum_{i=1}^m(w^Tx_i)^2=w^T[\frac{1}{m}\sum_{i=1}^m(x_ix_i^T)]w
$$

$w_2为第二大特征值对应的特征向量$

可以推导出：第k个主成分的目标函数$w_k$的最大值为第k大特征值对应的特征向量

所以综合k个主成分，可以得到最终的<mark>目标函数</mark>：
$$
\underset{w\in R^{p\times k}}{\max}tr(W^T(\frac{1}{m}XX^T)W),W^TW=I_k
$$

> 矩阵的迹，数学、线性代数名词，在线性代数中，一个n×n矩阵A的**主对角线（从左上方至右下方的对角线）上各个元素的总和**被称为矩阵A的迹（或迹数），一般记作tr(A)。

PCA主方向 = 数据协方差矩阵的特征向量

更大特征值对应的特征向量更加重要

**降维的结果**为：$Z=W^TX\Rightarrow R^{k\times p}\times R^{p\times m}\Rightarrow R^{k\times m}$

**重构的结果**为：$\hat X=WZ=WW^TX\Rightarrow R^{p\times k}\times R^{k\times m}\Rightarrow R^{p\times m}$

#### <mark>PCA算法步骤</mark>

目标：计算数组k个主方向

:one: 数据居中

:two: 计算居中数据的协方差矩阵

:three: 计算协方差矩阵最大k个特征值对应的特征向量，组成矩阵$W\in R^{p\times k}$

输出降维结果：$Z=W^TX$

降维后，信息有所损失。但是如果特征值非常小，损 失的信息很少

#### <mark>举例说明</mark>

![image-20220514165533119](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871466.png)

$c_1$归一化后得到W即$[1/\sqrt2,1/\sqrt2]^T$

重构后：
$$
X=WY=\begin{bmatrix}-\frac{3}{2}&-\frac{1}{2}&0&\frac{3}{2}&-\frac{1}{2}\\-\frac{3}{2}&-\frac{3}{2}&0&\frac{3}{2}&-\frac{1}{2}\end{bmatrix}
$$

### 最小投影距离

目标函数：最小化$x_i$与$\hat x_i$之间的距离
$$
\frac{1}{m}\sum_{i=1}^m||x_i-\sum_{j=1}^kz_{ji}w_j||^2
$$
最小原数据与投影数据间距离与最大投影方差等价

## 三、PCA应用：人脸识别

人脸识别：对所有人脸计算其子空间，然后在其计算 子空间坐标投影，利用低维坐标进行识别

训练数据X：图片尺寸*图片个数

![image-20220514171546796](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871468.png)

人脸重构：使用特征脸（主成分）重构的效果

每一个人脸都是特征脸（主成分）的线性组合

### 算法步骤

给定一张新的图片x：

将其投影到前k个主成分张成的低维空间中

![image-20220514172047067](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871471.png)

计算重构图片和重构误差

![image-20220514172128998](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871472.png)

如果重构误差大于某个阈值，认为图片不是人脸图片

若重构误差小于阈值，认为该图片是人脸图片。在k-维空 间中，利用最近邻对新图片进行分类

### SVD分解

$XX^T$非常大，通常大于10000

通过使用SVD进行加速

> 加速原理：通过计算$X^TX$的特征值和特征向量，得到$XX^T$的特征向量，即特征脸。

奇异值分解：

对于任意一个p×m的矩阵A，不妨假设p>m（维度大于个数）， 它可以被分解为
$$
A=U\Sigma V^T
$$
<img src="https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871480.png" alt="image-20220514172533278" style="zoom:80%;float:left" />

![image-20220514172633306](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871474.png)

奇异值分解得到的矩阵U,Σ,V 满足如下性质:

> U是$AA^T$所有特征向量组成的矩阵
>
> ∑中对角线元素的值是矩阵$A^TA$特征的平方根 
>
> V是$A^TA$所有特征向量组成的矩阵

<img src="https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871481.png" alt="image-20220514172954571" style="zoom:80%;float:left" />

奇异值分解可以理解为在空间内找到一组正交基𝑣𝑖 ，通 过矩阵乘法将这组正交基映射到像空间中，其中奇异值对应伸缩系数。

![image-20220514172932220](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871475.png)

#### 基于SVD——奇异值分解

$X^TX$是$m\times m$维的，比$XX^T$小得多

:one: 输入数据矩阵X (p行，m列, p > m);

:two: 对𝑿进行居中

:three: 计算$X^TX$的前k个特征值𝝀𝟏, ⋯ , 𝝀𝒌和特征向量矩阵𝑽

:four: 计算投影矩阵(𝑼𝟏, 𝑼𝟐, … 𝑼𝒌 ), 其中$U_i=XV_i/\sqrt \lambda_i$

:five: 降维结果为$Z=U^TX$

![image-20220514174403312](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871477.png)

所以，当p<m时使用原本的PCA算法就行，当p>m(维度大于样本数)时使用基于SVD的PCA算法比较好

## 四、PCA应用：数据预处理

使用PCA，可以同时去除变量之间的线性关系以及对数据进行归一化

:one: 假设数据$\{x_1,x_2,...,x_m\}$的协方差矩阵为$S=\frac{1}{m}\sum_{i=1}^m(x_i-\overline x)(x_i-\overline x)^T$

:two: 利用$W^TSW=\Lambda$，定义一个变换
$$
y_i=\Lambda^{-\frac{1}{2}}W^T(x_i-\overline x)
$$

$$
\Lambda=\begin{bmatrix}\lambda_1&\cdots &0
\\\vdots&\ddots&\vdots\\\
0 &\dots& \lambda_p\end{bmatrix}
$$

:three:则$\{y_1,y_2,...,y_m\}$的均值为0，y的协方差为单位矩阵。

该操作称为数据白化（whitening）操作

再用一下之前的例子：

#### <mark>举例说明</mark>

![image-20220514165533119](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img14/1695871466.png)
$$
y=\Lambda^{-\frac{1}{2}}W^TX=
\begin{bmatrix}
\frac{1}{\sqrt 2} & 0
\\0&\frac{1}{\sqrt{2/5}}
\end{bmatrix}
\begin{bmatrix}
\frac{1}{\sqrt 2} & -\frac{1}{\sqrt 2}
\\\frac{1}{\sqrt 2}&\frac{1}{\sqrt 2}
\end{bmatrix}^TX
$$
<mark>矩阵的能量是指</mark>
$$
\frac{\lambda_1+...+\lambda_k}{\lambda_1+...+\lambda_p}
$$
通常保留矩阵 80% ～ 90% 的能量，就可以得到重要的特征并去除噪声。

## 五、PCA缺点

通过最大投影方差找到的方向可能不利于分类，第二方向才适合分类

## 参考资料

[1]庞善民.西安交通大学机器学习导论2022春PPT

[2]周志华.机器学习.北京:清华大学出版社,2016