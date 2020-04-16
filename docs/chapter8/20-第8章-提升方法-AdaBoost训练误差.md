﻿# 第8章-提升方法-AdaBoost训练误差{docsify-ignore-all}

&emsp;&emsp;AdaBoost算法非常受欢迎，主要有3个原因：  
1. 分类效果非常好
2. 这个算法非常简单，学习的每一个基本分类器都是很简单的分类器
3. 有比较可靠的理论基础

&emsp;&emsp;通过一个例子解释一下算法中一直提到的权值以及样本的分布，假设有一个训练数据集$D_1$满足某个分布，对其进行抽样得到三个样本点$(x_1,y_1),(x_2,y_2),(x_3,y_3)$，这三个样本点是相互独立的，所以经验概率均为$\displaystyle \frac{1}{3}$，以上这些构成了一个概率分布为$P_1$，通过构建基本分类器，对于每个分类器在训练数据集上有一个分类的效果，对于分类效果差的样本，希望提高在下一轮训练模型时的权值。假设有一个新的数据集$D_2$，也有三个样本点$(x_1,y_1),(x_2,y_2),(x_3,y_3)$，这些样本点不是独立同分布的，根据$D_1$和在模型上的分类效果，构建一个新的分布$P_2$，这个新的分布对应每个样本点取到的概率就不是相同的值，假设$(x_1,y_1)$是误分类点，将权值提高到$\displaystyle \frac{2}{3}$，$(x_2,y_2)$和$(x_3,y_3)$是分类正确的，权值都为$\displaystyle \frac{1}{6}$。  

## 定理8.1的证明
**定理8.1** （AdaBoost的训练误差界）AdaBoost算法最终分类器的训练误差界为$$\frac{1}{N}\sum_{i=1}^N I(G(x_i) \neq y_i) \leqslant \frac{1}{N} \sum_i \exp(-y_i f(x_i)) = \prod_m Z_m$$其中，$G(x_i) = \text{sign}(f(x))=\text{sign}(\sum \alpha_m G_m(x))$  
**证明：**对于正确分类点，$I(G(x_i) \neq y_i) = 0 \leqslant \exp (-y_i f(x_i)) = e^{-1}$，对于误分类点，$I(G(x_i) \neq y_i) = 1 \leqslant \exp (-y_i f(x_i)) = e^1$，所以可得$I(G(x_i) \neq y_i) \leqslant \exp (-y_i f(x_i))$，故$\displaystyle \frac{1}{N}\sum_{i=1}^N I(G(x_i) \neq y_i) \leqslant \frac{1}{N} \sum_i \exp(-y_i f(x_i))$成立。  
现证明$\displaystyle \frac{1}{N} \sum_i \exp(-y_i f(x_i)) = \prod_m Z_m$：  
$\displaystyle \because Z_m=\sum_i w_{mi} \exp(-\alpha_m y_i G_m(x_i))  \\ \displaystyle \quad w_{m+1,i}=\frac{w_{mi}}{Z_m} \exp(-\alpha_m y_i G_m(x_i))$  
$\therefore Z_m \cdot w_{m+1,i} = w_{mi} \exp(-\alpha_m y_i G_m(x_i))$  
$\because \left\{ \begin{array}{rl}   
Z_1 \cdot w_{2,i} = & w_{1,i} \exp(-\alpha_1 y_i G_1(x_i)) \\
Z_2 \cdot w_{3,i} = & w_{2,i} \exp(-\alpha_2 y_i G_2(x_i)) \\
\vdots & \\
Z_{M-1} \cdot w_{M,i} = & w_{M-1,i} \exp(-\alpha_{M-1} y_i G_{M-1}(x_i)) \\
\end{array} \right.$  
将上述各式都相乘，相同的项可以约去，得到：  
$\displaystyle \prod_{m=1}^{M-1} Z_m w_{M,i} = w_{1,i} \exp(-y_i \sum_{m=1}^{M-1} \alpha_m G_m{x_i})$  
对比要证明的等式$\displaystyle \frac{1}{N} \sum_i \exp(-y_i f(x_i)) = \prod_{m=1}^M Z_m$，其中$f(x_i)=\sum_{m=1}^M \alpha_m G_m(x_i)$  
左右两边都乘以$\exp(-\alpha_M y_i G_M(x_i))$  
$\displaystyle \because w_{1,i}=\frac{1}{N}$  
$\displaystyle \therefore \prod_{m=1}^{M-1} Z_m w_{M,i} \cdot \exp(-\alpha_M y_i G_M(x_i)) = \frac{1}{N} \exp(-y_i f(x_i)) $  
由于最终得到的是关于整个数据集的，所以需要两边求和。  
$\displaystyle \therefore \prod_{m=1}^{M-1} Z_m \sum_i w_{M,i} \exp(-\alpha_M y_i G_M(x_i)) = \frac{1}{N} \sum_i \exp(-y_i f(x_i))$  
$\because Z_M=\sum_i w_{M,i} \exp(-\alpha_M y_i G_M(x_i))$  
$\displaystyle \therefore \prod_{m=1}^{M-1} Z_m \cdot Z_M = \frac{1}{N} \sum_i \exp(-y_i f(x_i)) $  
$\displaystyle \therefore \prod_{m=1}^{M} Z_m = \frac{1}{N} \sum_i \exp(-y_i f(x_i))$，得证.  
&emsp;&emsp;定理的直观理解：训练误差可以被$Z_m$的连乘控制住，如果要训练误差小，就只要最小化每一个$Z_m$，
$\displaystyle Z_m=\sum_i w_{mi} \exp(-\alpha_m y_i G_m(x_i))$，$w_{mi}$是通过上一轮训练的模型得到的，$G_m$是本轮已经训练得到的，所以已知$w_{mi},G_m$，求解的是$\alpha_m$，通过最小化$Z_m$求解$\alpha_m= \mathop{\arg \min} \limits_{\alpha_m} Z_m$。  
$\displaystyle \frac{\partial Z_m}{\partial \alpha_m} = \sum_i \Big( -w_{mi} y_i G_m(x_i) \exp(-\alpha_m y_i G_m(x_i)) \Big)$  
书中给出的$\displaystyle \alpha_m=\frac{1}{2} \ln \frac{1-e_m}{e_m}$  

## 定理8.2的证明
**定理8.2** （二类分类问题AdaBoost的训练误差界）$$\prod_{m=1}^M Z_m = \prod_{m=1}^M [2 \sqrt{e_m(1-e_m)}] = \prod_{m=1}^M \sqrt{(1-4\gamma_m^2)} \leqslant \exp \big( -2 \sum_{m=1}^M \gamma_m^2 \big)$$，其中，$\displaystyle \gamma_m = \frac{1}{2}-e_m$  
**证明：**  
$\begin{aligned} Z_m
=& \sum_{i=1}^N w_{mi} \exp(-\alpha_m y_i G_m(x_i)) \\
=& \sum_{y_i=G_m(x_i) w_{mi} e^{-\alpha_m}} + \sum_{y_i \neq G_m(x_i) w_{mi} e^{\alpha_m}} 
\end{aligned}$  
$\displaystyle \because e_m=\sum_{G_m(x_i) \neq y_i} w_{mi}, \alpha_m=\frac{1}{2} \ln \frac{1-e_m}{e_m}$  
$\displaystyle \therefore \sum_{y_i=G_m(x_i) w_{mi} e^{-\alpha_m}} + \sum_{y_i \neq G_m(x_i) w_{mi} e^{\alpha_m}} = (1-e_m)e^{-\alpha_m} + e_m e^{\alpha_m} $  
$\displaystyle \because e^{-\alpha_m} = \sqrt{\frac{e_m}{1-e_m}}, e^{\alpha_m} = \sqrt{\frac{1-e_m}{e_m}}$  
$\displaystyle \therefore (1-e_m)e^{-\alpha_m} + e_m e^{\alpha_m} = 2 \sqrt{e_m (1- e_m)}$  
$\therefore Z_m = 2 \sqrt{e_m (1- e_m)}$  
$\displaystyle \because \gamma_m = \frac{1}{2} - e_m$  
$\therefore 2 \sqrt{e_m (1- e_m)} = \sqrt{1-4 \gamma_m^2}$  
$\displaystyle \therefore \prod_{m=1}^M Z_m = \prod_{m=1}^M [2 \sqrt{e_m(1-e_m)}] = \prod_{m=1}^M \sqrt{(1-4\gamma_m^2)}$  

再考虑$\displaystyle \prod_{m=1}^M \sqrt{(1-4 \gamma_m^2)} \leqslant \exp \big( -2 \sum_{m=1}^M \gamma_m^2 \big)$  
书中介绍的是由$e^x$和$\sqrt{1-x}$在点$x=0$的泰勒展开式推导，泰勒展开是用一个多项式逼近任意一个函数。  
考察$\sqrt{(1-4 \gamma^2)} \leqslant \exp( -2 \gamma^2)$  

> $\displaystyle f(x)=\sqrt{1-x}=(1-x)^{\frac{1}{2}} \\ 
\displaystyle f'(x) = -\frac{1}{2}(1-x)^{-\frac{1}{2}} \\  
\displaystyle f''(x) = -\frac{1}{4}(1-x)^{-\frac{3}{2}}$  
$f(x)$在$x=0$处的二阶泰勒展开为：$$\begin{aligned} f(x) 
&= f(0) + xf'(0) + \frac{1}{2} xf''(0) + \cdots \\
&= 1 - \frac{1}{2} x - \frac{1}{8} x^2 + \cdots
\end{aligned} $$  
$\therefore f(4 \gamma^2) \approx 1 - 2 \gamma^2 - 2 \gamma^4$

> $g(x) = e^x \\
g'(x) = e^x \\ g''(x) = e^x$  
$g(x)$在$x=0$处的二阶泰勒展开为：$$\begin{aligned} g(x) 
&= g(0) + x g'(0) + \frac{1}{2} x^2 g''(0) + \cdots \\
&= 1 + x + \frac{1}{2} x^2 + \cdots
\end{aligned} $$
$\therefore g(-2 \gamma^2) \approx 1 - 2 \gamma^2 + 2 \gamma^4 $  

$\therefore \begin{array}{l} 
f(4 \gamma^2) = \sqrt{(1-4 \gamma^2)} \approx 1 - 2 \gamma^2 - 2 \gamma^4 \\ 
g(-2 \gamma^2) = \exp( -2 \gamma^2) \approx 1 - 2 \gamma^2 + 2 \gamma^4 
\end{array} $  
$\displaystyle \because \gamma_m = \frac{1}{2} - e_m $，$\gamma_m$的取值范围是$\displaystyle [0,\frac{1}{2}]$  
&emsp;&emsp;所以当更高阶次方出现的时候，后面的高阶项趋近于0，那么影响$g(-2 \gamma^2)$和$f(4 \gamma^2)$关系的只有前面的项，判断$1 - 2 \gamma^2 - 2 \gamma^4$和$1 - 2 \gamma^2 + 2 \gamma^4 $的大小，等同于判断$f(4 \gamma^2)$和$g(-2 \gamma^2)$的大小。  
$\because 1 - 2 \gamma^2 - 2 \gamma^4 \leqslant 1 - 2 \gamma^2 + 2 \gamma^4$  
$\therefore f(4 \gamma^2) \leqslant g(-2 \gamma^2) $  
$\displaystyle \therefore \prod_{m=1}^M \sqrt{(1-4 \gamma_m^2)} \leqslant \exp \big( -2 \sum_{m=1}^M \gamma_m^2 \big) $，得证.