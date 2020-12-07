---
title: 朴素贝叶斯法-知识点总结
categories: 机器学习
tags:
- 机器学习
---

1. **朴素贝叶斯的基本方法？**

   答：我们假设数据集$T=\{(x_1, y_1), (x_2, y_2), ... , (x_n, y_n)\}$由$P(X, Y)$独立同分布产生。先验分布：

   
   $$
   P(Y=c_k),k=1,2,...,K
   $$
   

   条件概率分布：

   
   $$
   P(X=x|Y=c_k) = P(X^{(1)}=x^{(1)},...,X^{(n)}=x^{(n)}|Y=c_k)
   $$
   

   朴素贝叶斯法对条件分布概率做了条件独立性的假设：

   

   $$
   P(X^{(1)}=x^{(1)},...,X^{(n)}=x^{(n)}|Y=c_k) =                               \prod_{j=1}^nP(X^{(j)}=x^{(j)}|Y=c_k)
   $$

   后验概率为：


$$
   P(Y=c_k|X=x)
$$


   由贝叶斯公式可得：


$$
   P(Y=c_k|X=x) \propto \prod_{j=1}^nP(X^{(j)}=x^{(j)}|Y=c_k).P(Y=c_k)
$$


   所以，最大化后验概率，可以表示为：


$$
   y = \mathop{\arg\max}_{c_k}                                          \    \prod_{j=1}^nP(X^{(j)}=x^{(j)}|Y=c_k).P(Y=c_k)
$$

2. **二项分布的最大似然估计？**

   答：二项分布的概率分布公式如下：
   
   
   $$
   P(x) = \theta^x(1-\theta)^{1-x}
   $$
   
   
   其中 $\theta$ 为$x=1$ 的概率，x 的取值为$\{0,1\}$. 

   我们需要估计在多次试验中参数$\theta$的取值：我们假设每次实验都是相互独立的，则：
   
   
   $$
   L(\theta)=P(x=x_1|\theta)P(x=x_2|\theta)....P(x=x_N|\theta) \\
   =\prod_{i=1}^N \theta^{x_i}(1-\theta^{1-x_i})
   $$
   
   
   最大似然估计，就是通过优化$\theta$, 最大化当前观测成立的概率$P(x=X \|            \theta)$,即    最大化
   
   
   $$
   max_{\theta} L(\theta)= \prod_{i=1}^N \theta^{x_i}(1-\theta)^{1-x_i}
   $$
   
   
   为了方便计算，我们对$L(\theta)$ 取对数，这并不影响我们的优化目标：
   
   
   $$
   lnL(\theta)=\sum_{i=1}^N ln(\theta^{x_i}) + ln((1-\theta)^{1-x_i}) \\
   = \sum_{i=1}^N x_i ln(\theta) + (1-x_i)ln(1-\theta)
   $$
   
   
   我们对其求偏导：
   
   
   $$
   \frac{\partial L(\theta)}{\partial \theta} = \frac{\sum_{i=1}^N x_i}      {\theta} - \frac{\sum_{i=1}^N (1-x_i)}{1-\theta} = 0
   $$
   
   
   解得：
   
   
   $$
   \theta = \frac{\sum_{i=1}^{N}x_i}{N}
   $$
   
3. **多项分布的最大似然与贝叶斯估计推导？**
   
   答：我们假设标签$Y \in \{c_1, c_2, ..., c_k\}$, 其中$k$为类别数目，与之对应的    概率分别为$\theta_1, \theta_2, ..., \theta_k$.满足：
   $$
   \sum_{i=1}^k \theta_i = 1
   $$
   所以我们可以得到多项分布为：
   
   
   $$
   P(Y=y|\theta) = \theta_1^{I(y=c_1)} \theta_2^{I(y=c_2)} ...          \theta_k^{I(y=c_k)}
   $$
   
   
   其中$I$ 为指示函数，即对$I(y=c_i)$ ,当$y=c_i$ 这个条件成立，$I(y=c_i) = 1$。
   
   最大似然估计为：
   
   
   $$
   max_{\theta} \ P(y_1, y_2, ..., y_k \|\theta) = p(y_1 \|\theta)p(y_2 \|\theta)...p(y_n \|\theta) \\
   = \theta_1^{m_1} \theta_2^{m_2} ... \theta_k^{m_k}
   $$
   
   
   我们对上述公式取对数，并结合约束条件，整理如下：
   
   
   $$
   max_{(\theta_1 ... \theta_k)} m_1ln\theta_1 + m_2ln\theta_2 +...+m_kln\theta_k + \lambda(\theta_1 + ... + \theta_k -1)
   $$
   

