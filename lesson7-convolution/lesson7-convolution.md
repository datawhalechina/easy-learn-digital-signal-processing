# 前言
在数学和工程领域（特别是信号处理和图像处理）中，卷积有着极其重要的作用。

本章首先通过函数的平移和翻转直观地讲解卷积是如何计算的，然后讨论了系统的串联和并联的数学表达式，系统串联时，整体系统的脉冲响应等于两个系统脉冲响应的卷积，并联时，整体系统的脉冲响应等于两个系统脉冲响应之和。最后，通过平均滤波和高斯滤波两个例子说明卷积在数字信号处理中的作用。

可以看到，卷积操作就是将一个函数翻转然后平移，再和另一个函数对应值相乘再相加。适当地设计参与卷积的函数便可以实现想要的功能，比如滤波器便是信号函数与滤波函数进行卷积，达到滤波的效果，例如，平均滤波器通过加权信号周边的信号来达到使信号变得平滑的目的。
# 卷积
## 定义
卷积是一种在信号处理、图像处理等众多领域中广泛应用的重要数学运算。
对于连续时间信号，给定两个函数$x(t)$与$h(t)$，它们的卷积$y(t)$定义为：
$$y(t)=x(t)*h(t)=\int_{-\infty}^{\infty}x(\tau)h(t - \tau)d\tau$$
这个公式的含义可以这样理解：

我们要计算在时刻$t$的卷积结果$y(t)$，需要对所有可能的$\tau$值，将$x(\tau)$与$h(t - \tau)$相乘，然后在整个实数轴上($-\infty$到$\infty$）对这些乘积进行积分。从物理意义上讲，它可以看作是一个函数（比如$x(t)$）对另一个函数（比如$h(t)$）在时间轴上的一种“加权叠加”效果。

对于数字信号，给定$x[n]$与$h[n]$，卷积$y[n]$的计算公式为：
$$y[n]=\sum_{k = -\infty}^{\infty}x[k]h[n - k]$$
这里是对所有可能的离散样本索引$k$，将$x[k]$与$h[n - k]$相乘，然后对这些乘积进行求和。同样，它也可以理解为是对数字信号$x[n]$在离散时间点上按照$h[n]$所规定的“权重”进行叠加。
# 图形和公式详细解释卷积的概念和计算过程
## 图形解释（以数字信号为例）：
假设我们有两个简单的数字信号$x[n]$和$h[n]$。
1. 首先，我们固定一个时刻$n$，对于$$y[n]=\sum_{k = -\infty}^{\infty}x[k]h[n - k]$$这个公式，我们要计算$y[n]$的值。
2. 当$n$确定时，我们来看$h[n - k]$这一项。随着$k$的变化，$h[n - k]$实际上是将$h[n]$在时间轴上进行了反转和平移。例如，如果$h[n]$原本是一个随着$n$增加而有某种变化趋势的序列，那么$h[n - k]$当$k$从$-\infty$变化到$\infty$时，就相当于把$h[n]$先反转（比如原来是从左到右上升的，反转后就是从左到右下降的），然后再根据$k$的值在时间轴上左右平移。
3. 然后，对于每一个$k$值，我们将$x[k]$与此时的$h[n - k]$相乘。这就相当于在每一个平移后的位置上，根据$x[k]$的值给$h[n - k]$赋予了一个“权重”。
4. 最后，将所有这些乘积按照$k$从$-\infty$到$\infty$进行求和，得到的结果就是$y[n]$在这个特定时刻的值。
**公式计算过程示例：**
假设$$x[n]=\begin{cases}1, & n = 0,1,2 \\ 0, & \text{otherwise}\end{cases}$$
和$$h[n]=\begin{cases}2, & n = 0 \\ 1, & n = 1 \\ 0, & \text{otherwise}\end{cases}$$
我们来计算$$y[n]=x[n]*h[n]$$
对于$n = 0$：
$$
\begin{align*}
y[0]&=\sum_{k = -\infty}^{\infty}x[k]h[0 - k]\\
&=x[0]h[0]+x[1]h[-1]+x[2]h[-2]+\cdots\\
&=1\times2 + 0\times\text{(undefined)} + 0\times\text{(undefined)}+\cdots\\
&=2
\end{align*}
$$
因为$h[-1]$和$h[-2]$等在$h[n]$的定义中是未定义的（这里假设$n$为整数且我们只考虑定义范围内的值），所以它们对应的乘积为$0$。
对于$n = 1$：
$$
\begin{align*}
y[1]&=\sum_{k = -\infty}^{\infty}x[k]h[1 - k]\\
&=x[0]h[1]+x[1]h[0]+x[2]h[-1]+\cdots\\
&=1\times1 + 1\times2 + 0\times\text{(undefined)}+\cdots\\
&=3
\end{align*}
$$
对于$n = 2$：
$$
\begin{align*}
y[2]&=\sum_{k = -\infty}^{\infty}x[k]h[2 - k]\\
&=x[0]h[2]+x[1]h[1]+x[2]h[0]+\cdots\\
&=1\times0 + 1\times1 + 1\times2+\cdots\\
&=3
\end{align*}
$$
对于$n > 2$或$n < 0$，按照同样的方法计算，会发现$y[n]=0$（因为在这些情况下，$x[k]$和$h[n - k]$的乘积要么是$0$要么是未定义的，求和后结果为$0$）。
所以，卷积结果$y[n]$为：
$$y[n]=\begin{cases}2, & n = 0 \\ 3, & n = 1,2 \\ 0, & \text{otherwise}\end{cases}$$
## 示例：计算给定数字信号和脉冲响应的卷积结果
假设我们有一个数字信号$$x[n]=\begin{cases}3, & n = 0 \\ 2, & n = 1 \\ 1, & n = 2 \\ 0, & \text{otherwise}\end{cases}$$
和一个脉冲响应$$h[n]=\begin{cases}1, & n = 0 \\ -1, & n = 1 \\ 0, & \text{otherwise}\end{cases}$$
我们来计算它们的卷积$$y[n]=x[n]*h[n]$$
对于$n = 0$：
$$
\begin{align*}
y[0]&=\sum_{k = -\infty}^{\infty}x[k]h[0 - k]\\
&=x[0]h[0]+x[1]h[-1]+x[2]h[-2]+\cdots\\
&=3\times1 + 2\times\text{(undefined)} + 1\times\text{(undefined)}+\cdots\\
&=3
\end{align*}
$$
对于$n = 1$：
$$
\begin{align*}
y[1]&=\sum_{k = -\infty}^{\infty}x[k]h[1 - k]\\
&=x[0]h[1]+x[1]h[0]+x[2]h[-1]+\cdots\\
&=3\times(-1) + 2\times1 + 1\times\text{(undefined)}+\cdots\\
&=-1
\end{align*}
$$
对于$n = 2$：
$$
\begin{align*}
y[2]&=\sum_{k = -\infty}^{\infty}x[k]h[2 - k]\\
&=x[0]h[2]+x[1]h[1]+x[2]h[0]+\cdots\\
&=3\times0 + 2\times(-1) + 1\times1+\cdots\\
&=-1
\end{align*}
$$
对于$n > 2$或$n < 0$，同样按照上述方法计算可得$y[n]=0$。
所以，卷积结果$y[n]$为：
$$y[n]=\begin{cases}3, & n = 0 \\ -1, & n = 1 \\ -1, & n = 2 \\ 0, & \text{otherwise}\end{cases}$$
# LTI系统的连接方式
## 串联（$h_1[n]*h_2[n]$）
在LTI（线性时不变）系统中，当两个系统串联时，整体系统的脉冲响应等于两个系统脉冲响应的卷积。
假设我们有两个LTI系统，其脉冲响应分别为$h_1[n]$和$h_2[n]$。当输入信号$x[n]$依次经过这两个系统时，首先经过系统1，输出为$$y_1[n]=x[n]*h_1[n]$$
然后这个输出$y_1[n]$作为系统2的输入，最终输出为$$y[n]=y_1[n]*h_2[n]=(x[n]*h_1[n])*h_2[n]=x[n]*(h_1[n]*h_2[n])$$

从系统特性来看，串联后的系统依然保持线性时不变特性。例如，如果输入信号$x[n]$发生了某种变化（比如幅度改变或时间延迟等），按照线性时不变系统的性质，经过第一个系统后的输出$y_1[n]$会相应地改变，然后这个改变后的$y_1[n]$作为输入进入第二个系统，最终输出$y[n]$也会按照线性时不变的规律发生改变。而且，串联后的系统对于不同频率成分的信号处理效果也会发生变化。一般来说，串联系统会对信号进行更复杂的滤波等处理，使得信号的频谱特性等进一步改变。

## 并联（$h_1[n]+h_2[n]$）
当两个LTI系统并联时，整体系统的脉冲响应等于两个系统脉冲响应的和，即$h[n]=h_1[n]+h_2[n]$。
当输入信号$x[n]$同时进入这两个并联的系统时，系统1的输出为$$y_1[n]=x[n]*h_1[n]$$
系统2的输出为$$y_2[n]=x[n]*h_2[n]$$
最终整体系统的输出为$$y[n]=y_1[n]+y_2[n]=x[n]*h_1[n]+x[n]*h_2[n]=x[n]*(h_1[n]+h_2[n])$$
从系统特性来看，并联后的系统同样保持线性时不变特性。与串联系统不同的是，并联系统对于输入信号的处理效果相对简单一些。它相当于对输入信号同时进行了两种不同的处理（由两个系统分别进行），然后将这两种处理结果相加。例如，如果一个系统主要用于对信号进行低频滤波，另一个系统主要用于对信号进行高频滤波，那么并联后的系统会同时保留这两种滤波效果，对信号的频谱特性进行一种综合的调整。
# 卷积与滤波
## 平均滤波器
平均滤波器的定义为$$b[n]=\frac{1}{M}\{1,1,\cdots,1\},n = 0,1,\cdots,M - 1$$
它的作用是对输入信号进行平滑处理。当输入信号$x[n]$经过平均滤波器时，输出信号$y[n]$的计算方式为：
$$y[n]=\sum_{k = 0}^{M - 1}x[n - k]b[k]$$
这实际上就是$x[n]$与$b[n]$的卷积运算。
## 示例展示平均滤波器对输入信号的滤波效果：
假设我们有一个输入信号$$x[n]=\begin{cases}5, & n = 0 \\ 3, & n = 1 \\ 7, & n = 2 \\ 2, & n = 3 \\ 0, & \text{otherwise}\end{cases}$$
和一个平均滤波器$$b[n]=\frac{1}{3}\{1,1,1\},n = 0,1,2$$
对于$n = 0$：
$$
\begin{align*}
y[0]&=\sum_{k = 0}^{2}x[0 - k]b[k]\\
&=x[0]b[0]+x[-1]b[1]+x[-2]b[2]\\
&=5\times\frac{1}{3}+0\times\frac{1}{3}+0\times\frac{1}{3}\\
&=\frac{5}{3}
\end{align*}
$$
对于$n = 1$：
$$
\begin{align*}
y[1]&=\sum_{k = 0}^{2}x[1 - k]b[k]\\
&=x[1]b[0]+x[0]b[1]+x[-1]b[2]\\
&=3\times\frac{1}{3}+5\times\frac{1}{3}+0\times\frac{1}{3}\\
&=\frac{8}{3}
\end{align*}
$$
对于$n = 2$：
$$
\begin{align*}
y[2]&=\sum_{k = 0}^{2}x[2 - k]b[k]\\
&=x[2]b[0]+x[1]b[1]+x[0]b[2]\\
&=7\times\frac{1}{3}+3\times\frac{1}{3}+5\times\frac{1}{3}\\
&=\frac{15}{3}=5
\end{align*}
$$
对于$n = 3$：
$$
\begin{align*}
y[3]&=\sum_{k = 0}^{2}x[3 - k]b[k]\\
&=x[3]b[0]+x[2]b[1]+x[1]b[2]\\
&=2\times\frac{1}{3}+7\times\frac{1}{3}+3\times\frac{1}{3}\\
&=\frac{12}{3}=4
\end{align*}
$$
对于$n > 3$或$n < 0$，同样按照上述方法计算可得$y[n]=0$（因为在这些情况下，$x[k]$和$b[n - k]$的乘积要么是0要么是未定义的，求和后结果为0）。
所以，经过平均滤波器后的输出信号$y[n]$为：
$$y[n]=\begin{cases}\frac{5}{3}, & n = 0 \\ \frac{8}{3}, & n = 1 \\ 5, & n = 2 \\ 4, & n = 3 \\ 0, & \text{otherwise}\end{cases}$$
可以看出，原始信号中的一些尖锐的变化（比如从5到3，从7到2等）在经过平均滤波器后变得相对平滑了，这就是平均滤波器在平滑信号方面的应用。
## 高斯滤波器
高斯滤波器的表达式为$$g[n]=e^{-\frac{n^2}{2\sigma^2}}$$（\sigma为标准差）。

在信号处理中，高斯滤波器有着重要的作用，比如去除噪声等。它的原理是基于高斯函数的特性，高斯函数在数学上具有很好的平滑性和对称性。当输入信号$x[n]$经过高斯滤波器时，输出信号$y[n]$同样是通过卷积运算得到的，即：
$$y[n]=\sum_{k = -\infty}^{\infty}x[k]g[n - k]$$
一般来说，在滤波前，信号可能存在一些噪声或者尖锐的波动等情况。经过高斯滤波器处理后，信号会变得更加平滑，一些噪声成分会被有效地去除，同时信号的整体特征仍然得以保留，这就是高斯滤波器在信号处理中的重要作用。