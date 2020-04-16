﻿# 第6章-Logistic回归与最大熵模型-导读{docsify-ignore-all}

&emsp;&emsp;本章一共讲了两个模型：Logistic回归与最大熵模型，这两个模型都是用来做分类问题的，具有以下几个共同点：  
1. 它们都属于概率模型，该模型要寻找的是给定一个$x$，得到输出变量$Y$的概率分布$P(Y|x)$，如果是二分类，$Y$取值为0或1，如果是多分类，$Y$有$K$个不同的类别。
2. 它们都属于对数线性模型，对概率分布$P(Y|x)$取对数，可得$\ln P(Y|x)=w \cdot x$关于$x$的线性函数，如果$Y=c_i$，则$w=w_i$。两个模型之前的区别是Logistic回归属于判别模型，最大熵模型属于生成模型。在最大熵模型中，不仅$x$有随机性，$Y$也具有随机性，是一个随机变量。

## Logistic回归模型
### 二项Logistic回归模型
&emsp;&emsp;给定输入变量$x$，输出变量为$y \in \{0,1\}$，将$y=1$的概率记作$\pi(x)=P(Y=1|x) \in [0,1]$，上述已经介绍了，这个模型是一个线性模型，可以用$w \cdot x$线性 函数来表示，由于$w \cdot x \in (-\infty, +\infty)$，那么要如何将$\pi(x)$与$w \cdot x$对应起来呢？这就需要用到一个变换，该变换称为Logit变换。  
Logit变换：$\displaystyle \text{logit}(\pi(x))=\log \frac{\pi(x)}{1-\pi(x)} \in (-\infty, +\infty)$  
$\displaystyle \therefore \ln \frac{\pi(x)}{1-\pi(x)}=w \cdot x$，这个就是Logistic回归模型的一个形式。  
$\displaystyle \therefore \pi(x)=\frac{\exp (w \cdot x)}{1+\exp (w \cdot x)}$，其中$\pi(x)$就是给定$x$的条件下，$Y=1$的概率。  
所以可得下面两个公式：
$\displaystyle \log \frac{P(Y=1 | x)}{1-P(Y=1 | x)}=w \cdot x$  
$\displaystyle P(Y=1 | x)=\frac{\exp (w \cdot x)}{1+\exp (w \cdot x)}$  
&emsp;&emsp;有了这个模型之后，需要求解参数$w$，一旦求出$w$，那么任意给定一个输入变量$x$，就可以得到$Y=1$的概率，如果该概率值大于0.5，就将该类判定为1，如果小于0.5，将该类判定为0。  
&emsp;&emsp;求解$w$使用的方法是**极大似然估计**，给定参数$w$，求样本的联合概率密度，最大化该联合概率，从而求出参数$w$。

### 二项Logistic回归模型参数估计
已知$y_i \in \{0,1\}$，$\pi(x)$  
可得$P_w(y|x)=\pi(x)^y[1-\pi(x)]^{1-y}$  
似然函数为$\displaystyle L(w)=\prod_{i=1}^{N} \pi(x)^{y_i}[1-\pi(x)]^{1-y_i}$  
$\begin{aligned} 最大化似然函数：\max \ln L(w)
&= \sum_{i=1}^N\big\{ y_i \ln \pi(x) + (1-y_i)\ln\big[1-\pi(x)\big] \big\} \\
&= \sum_{i=1}^N\big\{y_i (w \cdot x_i)- \ln \big[1+\exp(w \cdot x_i)\big]\big\}
\end{aligned}$  
&emsp;&emsp;使用梯度下降法（见第2章-感知机模型-随机梯度下降法），其中求解过程中略有区别，在感知机模型中，求解的是极小值$\min$，给定$w$的初值，是向着$w$的$-\nabla$的方向更新的；但是在Logistic回归模型中，求解的是极大值$\max$，给定$w$的初值，需要向着$w$的$+\nabla$的方向更新。  

### 多项Logistic回归模型
&emsp;&emsp;在二分类Logistic回归模型中，$\displaystyle \ln \frac{\pi(x)}{1-\pi(x)} \Rightarrow \ln \frac{P(Y=1|x)}{P(Y=0|x)}$。  

&emsp;&emsp;在多分类Logistic回归模型中，$Y$取值为$1,\cdots,K$，那么Logit变换是$\displaystyle \ln \frac{p_(Y=k|x)}{P(Y=K|x)} = w_k \cdot x \in (-\infty, +\infty)$，因为$k$的取值为$1,\cdots, K-1$，所以$w$变为$w_k$，一共需要求取$K-1$个参数向量。  
所以可得下面两个公式：  
$\displaystyle P(Y=k|x)=\frac{\exp (w_k \cdot x)}{1+\sum_{k=1}^{K-1} \exp (w_k \cdot x)}, k=1,\cdots,K-1$   
$\displaystyle P(Y=K|x)=\frac{1}{1+\sum_{k=1}^{K-1} \exp (w_k \cdot x)}$  

## 最大熵模型
&emsp;&emsp;最大熵模型在自然语言处理中是一个很常用的模型。  
&emsp;&emsp;**最大熵模型原理：**在满足约束条件的模型集合中选择熵最大的模型。  
&emsp;&emsp;之前介绍过熵的概念，表示数据的混乱程度。为什么要选择熵最大？因为在不知道任何信息的情况下，假设数据在各个取值上面是比较平均的（即比较混乱的）。  

> **书上例题6.1：**假设随机变量$X$有5个取值$\{A,B,C,D,E\}$，要估计取各个值的概率$P(A),P(B),P(C),P(D),P(E)$。</br>  
**解答：**  
&emsp;&emsp;可知$P(A)+P(B)+P(C)+P(D)+P(E)=1，P(k) \geqslant 0,k \in \{A,B,C,D,E\}$  
&emsp;&emsp;根据最大熵原理，可得$\displaystyle P(A)=P(B)=P(C)=P(D)=P(E)=\frac{1}{5}$  
&emsp;&emsp;有时，能从一些先验知识中得到一些对概率值的约束条件，例如：  
$\displaystyle P(A)+P(B)=\frac{3}{10}$  
$P(A)+P(B)+P(C)+P(D)+P(E)=1$  
&emsp;&emsp;可以写成上述问题的优化问题：  
$$\begin{array}{ll}
\min & \displaystyle -H(P)=\sum_{i=1}^5 P(y_i) \log P(y_i) \\ 
\text { s.t. } & \displaystyle P(y_1)+P(y_2)=\tilde{P}(y_1)+\tilde{P}(y_2)=\frac{3}{10} \\ 
{}& \displaystyle \sum_{i=1}^5 P(y_i)=\sum_{i=1}^5 \tilde{P}(y_i)=1
\end{array}$$  

&emsp;&emsp;**条件熵**是给定一个随机变量$x$，在已知$x$的信息下，求随机变量$y_i$的混乱程度。可得$\displaystyle H(y | x)=-\sum_y P(y | x) \ln P(y | x)$，由于在所有训练数据集中$x$的取值是不一样的，只需要求解$x$关于$y$的期望，可得$\displaystyle H(P)=E_{x} H(y | x)=-\sum_{x,y} P(x) P(y | x) \ln P(y | x)$，其中$P(x)$可以用样本上的分布$\tilde{P}(x)$进行替换，所以可得如下公式：
$$\displaystyle H(P)=-\sum_{x, y} \tilde{P}(x) P(y | x) \log P(y | x)$$  
**特征函数：**
$$f(x, y)=\left\{\begin{array}{l}{1, x\text{与}y\text{满足某一事实}} \\ {0,\text{否则}}\end{array}\right.$$  

> 下面举例说明：  
以英汉翻译为例，对于英语中的“take”，它对应汉语的翻译有：  
- “抓住”: The mother takes her child by the hand. 母亲抓住孩子的手。
- “拿走”：Take the book home. 把书拿回家。
- “乘坐”：to take a bus to work. 乘坐公共汽车上班。
- “量”: Take your temperature. 量一量你的体温。
- “装”：The suitcase wouldn't take another thing. 这个衣箱不能装别的东西了。
- “花费”：It takes a lot of money to buy a house. 买一所房子要花一大笔钱。
- “理解、领会”：How do you take this package? 你怎么理解这段话？  

> &emsp;&emsp;以上可知这是$Y$的不同类别，训练集中的实例$x$就是说的话（英语），对应的$y$是对应汉语的翻译，这些实例对应的翻译$y$是不一样的，从已经获得的样本中发现，如果take后面有一个单词bus，那就将take翻译为“乘坐”，也就是说$y=\text{乘坐}$，$x$中的take后面有一个单词bus。这就是观察到的一个特征，然后用特征函数来描述这个信息。  
&emsp;&emsp;可得到特征函数：
$$f(x, y)=\left\{\begin{array}{l}{1, \text{if} \quad y=\text{“乘坐” and next(x)}=\text{“bus”}} \\ {0}\end{array}\right.$$  
&emsp;&emsp;会观察到很多这样的信息，一般用$f_i(x,y)$来表示，其中$i=1,\cdots,n$，将上述函数加到最大熵的约束条件中，让该特征在训练集实例（即样本）上出现的概率等于该特征在总体中出现的概率，那要如何用数学语言描述呢？因为$f_i(x,y)$是一个二值函数，在总体中出现的概率可以用期望$E_{P(x,y)}(f_i(x,y))$表示，可得：
$$E_{P(x,y)}(f_i(x,y))=E_{\tilde{P}(x,y)}(f_i(x,y))$$  

最大熵模型对应的最优化问题：  
$$\begin{array}{ll}
{\displaystyle \max_{P \in C}} & {\displaystyle H(P)=-\sum_{x, y} \tilde{P}(x) P(y | x) \log P(y | x)} \\ 
{\text { s.t. }} &{\displaystyle E_P(f_i)=E_{\tilde{P}}(f_i), \quad i=1,2, \cdots, n} \\  
&{\displaystyle \sum_{y} P(y | x)=1}
\end{array}$$  
求出的结果是$\displaystyle P_{w}(y | x)=\frac{1}{z_w(x)} \exp \big(\sum_{i=1}^n w_i f_i(x, y)\big)$，其中$\displaystyle w=\arg \max L_{\tilde{P}}(P_w)=\log \prod_{(x,y)} P(y | x)^{\tilde{P}(x, y)}$  
**直观理解：**给定输入变量$x$，分别求解$y$取不同值时，对应的概率分布，$\displaystyle \sum_{i=1}^n w_i f_i(x, y)$表示$n$个特征在给定的$(x,y)$上出现的次数，$(x,y)$满足几个特征，$w_i$表示该特征的重要程度，当满足的特征越多，并且这些特征越重要，$P_{w}(y | x)$的概率越大。

## 模型学习的最优化算法
**改进的迭代尺度法：**求最大化对数似然函数，该对数似然函数为$\displaystyle L(w)=\sum_{x, y} \tilde{P}(x, y) \sum_{i=1}^n w_i f_i(x, y)-\sum_x \tilde{P}(x) \log Z_w(x)$，其中$Z_w(x)$是归一化的系数，为了保证给定$x$的条件下$y$的概率和等于1。  
**拟牛顿法：**$$\min _{w \in R^n} f(w)=\sum_x \tilde{P}(x) \log \sum_y \exp \big(\sum_{i=1}^n w_i f_i(x, y)\big)-\sum_{x, y} \tilde{P}(x, y) \sum_{i=1}^n w_i f_i(x, y)$$

## 总结
&emsp;&emsp;最大熵模型，和之前介绍的模型有一个很明显的区别：在之前的模型中，直接用输入变量$X$向量中每一个值的信息，但是在最大熵模型中，采用的是$x$和$y$之间的特征关系，并不是直接用$x$的取值。