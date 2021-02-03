---
title: Transformer
categories: NLP
tags:
- NLP
---

transformer模型近日可谓风光无限，从BERT到GPT甚至到计算机视觉领域，都出现了transformer的身影。
我们可以将transformer模型视为带有**自注意力机制**的Seq2Seq模型，下面我们将详细介绍transformer模型。

## 1. Transformer相较于其它的序列处理模型(RNN, LSTM，GRU) 有什么特点和优势？

答：RNN，LSTM为常见的序列处理模型，相较于transformer模型而言，最大的缺点是无法并行计算(RNN输出的每个元素都要等待上一个元素的计算结束)，增加了模型线上部署的压力。transformer则可以并行计算，且每个输出元素都与所有的输入元素相关。transformer可以视为带有**自注意力机制**的seq2seq模型。我们可以将任何用RNN模型处理的问题用transformer来处理。

为了统一符号，我们定义transformer的输入为$\{x^1, x^2,...,x^m\}$,其为长度为m的序列。

## 2. Transformer 的 q，k，v如何计算，分别代表什么意义？

答：

- q的全称为query，是用来匹配其他元素的，其计算公式为$q^i = W^q a^i$.

- k的全称为key, 是用来被匹配的，其计算公式为$k^i = W^k a^i$.

- v的全称为value，为待提取的信息，其计算公式为$v^i = W^va^i$.

  其中$a^i = Wx^i$, 上述式子中的$i$ 代表输入序列中第$i$个元素，每个元素均有对应的$q^i, k^i, v^i$。

