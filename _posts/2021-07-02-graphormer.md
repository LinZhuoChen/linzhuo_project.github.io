---
title: Graphormer详解
categories: graph embedding
tags:
- 推荐系统
---

大家好，这里是Linzhuo。Transformer自从问世以来，在各个领域取得了显著的成绩。例如自然语言处理与计算机视觉。今天，Linzhuo为大家介绍一种将Transformer应用到图表示学习中，并在OGB graph level 比赛中取得第一名的方法：Graphormer。

本文将从以下几个方面开始介绍Graphormer：
- 背景简介
- Transformer 介绍
- Graphormer 编码方式介绍
  - Centrality Encoding
  - Spatial Encoding
  - Edge Encoding
- Graphormer 实现细节
  - Graphormer Layer 的实现
  - Special Node
- 结论

### 背景简介
Transformer是建模序列信息($x_1, x_2,..., x_n$)的有力工具。对于图结构来说，Transformer可以通过自注意力机制(Self-Attention) 来计算每个独立节点与图中其它节点的语意相似性，建立起独立单元与整体结构之间的联系。然而对于图来说，同时需要模型感知图的空间结构以及节点之间的联系。Graphormer结合了几种有效的空间编码方式来利用这些信息，我们将在下面详细介绍。

### Transformer 介绍
Transformer由一系列的Transformer Layer组成。每个Transformer Layer由以下两个部分组成：一个自注意力模块和一个逐位置的前向网络(feed-forward network)组成。我们令$H = [h_1^T,...,h_n^T] \in R^{n \times d}$代表 Transformer Layer的输入。其中$d$为隐藏层的维度，$h_i \in R^{1 \times d}$为在位置$i$的隐层表示。输入$H$通过三个矩阵$W_Q \in R^{d \times d_Q}, W_k \in R^{d \times d_K}$ 和 $W_v \in R^{d \times d_V}$来投影到对应的表示$Q, K, V$。上述过程可以表示为：


$$
Q = HW_Q, K = HW_K, V = HW_V, \\
A = \frac{QK^T}{\sqrt{d_K}}, Attn(H) = softmax(A)V.
$$


其中$A$为捕捉queries 与 keys相似性的矩阵。为了方便说明，我们仅考虑single-head self-attention 并假设$d_K = d_V = d$。将上述过程扩展到multi-head很直接。为了简便，我们同时忽略偏置项。
### Graphormer 编码方式介绍
接下来，我们将介绍在Graphormer中，用到的三种简单又高效的空间编码方式，具体如图1所示：


![](/images/graphormer.png)

<center>图1 Graphormer中 centrality encoding， spatial encoding，edge encoding的介绍。</center>

#### Centrality Encoding
上述公式通过节点之间的语意相似度来计算注意力的分布，然而节点的向心性 (centrality)也是图理解中的重要信号。例如在社交网络中，拥有大量粉丝的核心用户是预测社交网络趋势的重要因素。这些信息在目前的注意力计算中往往被忽视。我们认为这些信息对Transformer模型来说是有价值的信号。

我们使用图的出度和入度来表示节点的向心性，并使用下列式子引入：


$$
h_i^{(0)} = x_i + z^-_{deg^-(v_i)} + z^+_{deg^+(v_i)},
$$


其中$z^-, z^+ \in R^d$ 为 入度 $deg^-(v_i)$和出度 $deg^+(v_i)$的可学习embedding向量。对于无向图来说，$deg^-(v_i)$和$deg^+(v_i)$可以统一归到$deg(v_i)$。通过在输入中引入Centrality Encoding，softmax 注意力机制可以同时捕捉到语义关联性与节点向心性。

#### Spatial Encoding
Transformer的一大优势就是具有全局感受野。对于序列信息来说，为了获得序列中的空间信息，通常Transformer会引入Position embedding来编码序列中的空间信息。然而对于图结构来说，节点并没有组织成序列。节点可能存在于多维空间，并通过边相连。为了在模型中编码图的空间信息，Graphormer提出了Spatial Encoding。具体来说，对于一个图$G$，我们考虑一个函数$\phi(v_i, v_j) : V \times V ->R$，衡量了图G中节点$v_i$与$v_j$的空间关系。Graphormer使用shortest path (SPD) 来衡量两个相连节点之间的距离($\phi(v_i, v_j$))。如果两个节点不相连，则$\phi$的输出设置为常数，例如-1。在引入Spatial Encoding后，对于$A_{ij}$(Query-Key 相似矩阵的第$i$行$j$列)我们有：


$$
A_{i,j} = \frac{(h_iW_Q)(h_jW_K)}{\sqrt{d}} + b_{\phi(v_i, v_j)}
$$



其中$b_{\phi(v_i, v_j)}$为$\phi(v_i, v_j)$索引的可学习的标量，在所有Layer上共享。

#### Edge Encoding

之前的方法仅仅在相应的节点之间传递edge information，不利于在整个图上充分利用edge信息。Graphormer提出了一种新的edge encoding方法。对于每个有序的节点对$(v_i, v_j)$，我们找到$(v_i, v_j)$之间的最短路径：$SP_{ij} = (e_1, e_2, ..., e_N)$，然后计算沿路径的edge feature 与可学习的embedding的点积的平均值，如下式所示：


$$
A_{i,j} = \frac{(h_iW_Q)(h_jW_K)}{\sqrt{d}} + b_{\phi(v_i, v_j)} + c_{ij} \quad 
c_{ij} = \frac{1}{N} \sum_{n=1}^N x_{e_n}(w^E_n)^T.
$$


其中$x_{e_n}$是在路径$SP_{ij}$中的第$n$个edge，$w^E_n \in R^{d_E}$为第$n$个可学习的embedding。

### Graphormer 实现细节

#### Graphormer Layer 的实现
Graphormer在经典的Transformer的编码层基础上实现。另外，Graphormer调整了LN(layer normalization)的位置，将LN放在了multi-head self-attention (MHA) 和 feed-forward blocks (FFN)之前。对于FFN sub-layer，我们设置输入，输出和中间层的维度为$d$。最后Graphormer Layer的实现如下：


$$
h'^{(l)} = MHA(LN(h^{(l-1)})) + h^{(l-1)}
h^{(l)} = FFN(LN(h'^{(l)}) + h'^{(l)}
$$


#### Special Node
为了更好的表示图结构的整体信息，我们添加了一个节点，名为$[VNode]$，随即将$[VNode]$与其他节点独立连接，这类似于BERT模型中的$[CLS]$。由于$[VNode]$与所有节点相连，因此$\phi([VNode], v_j)$与$\phi(v_j, [VNode])$都为1。这个连接不是物理上的连接。为了区别虚拟连接和物理上的连接，Graphormer将$b_{\phi([VNode], v_j)}$与$b_{\phi(v_j,[VNode])}$设置为独立可学习的标量。

### 结论
Graphormer利用Transformer强大的序列建模能力，带有全局感受野的自注意力结构，并引入三种空间编码方式来弥补Transformer感知图结构能力的不足。在OGB graph level 比赛中取得了第一名的好成绩。

### 参考资料

\[1\]:Do Transformers Really Perform Bad for Graph Representation? https://arxiv.org/abs/2106.05234

