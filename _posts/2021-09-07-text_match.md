---
title: MatchPyramid文本匹配方法介绍
categories: graph embedding
tags:
- NLP
---

今天给大家带来一篇经典的文本匹配文章解读：Text Matching as Image Recognition。该文章于2016年发表于AAAI。该文章的核心思想是：提取出文本之间的相似性矩阵，并利用多层卷积与池化的方法来理解矩阵，最后输出文本间的相似性评分。

### 1. Motivation

首先，判断两个句子的相似性，我们以"今天天气很好"，和 “今天窗外阳光明媚”为例。
我们可以从以下三个层面来判断句子的相似性：

1. 字级别的匹配：我们可以通过判断两个句子之间相似的字来进行相似性判断，例如“今”，“天”
2. 词语级别的匹配：我们可以通过判断两个句子之间相似的词语来进行相似性判断。例如 “天气很好”，“阳光明媚”
3. 句子级别的匹配：通过判断两个句子语义的相似性来进行整体判断。例如这两个句子都是描述天气的，而且整体意思都是天气好。

可以看出，这三种级别的匹配语义由low-level到high-level，high-level的匹配也是由多个low-level级别的匹配叠加而成。
这与卷积神经网络图像识别的思想很相似。因此这篇文章提出，用卷积网络的方法来理解文本之间的相似性。


### 2. MatchPyramid
为了方便卷积神经网络处理，MatchPyramid首先得到两个文本之间的相似矩阵$M$, 其中$M_{ij}$衡量了两句话中第$i$个字和第$j$个字的语义相似性：


$$
M_{ij} = w_i \otimes v_j.
$$


其中$\otimes$为一种衡量相似性的广泛操作符。文章中通过两个字之间的embedding 向量点积操作获得相似性度量。

在获得相似性矩阵$M$后，即可通过卷积网络来提取相似性矩阵的特征。最后输出两个句子的相似性得分，整体的网络结构如图1. 所示：


![](https://files.mdnice.com/user/5787/c8d7b90c-2a0c-4855-b1b0-42c4b2745097.png)

<center>图1. MatchPyramid的网络结构</center>

MatchPyramid网络的核心思想keras实现如下所示，说明在代码注释中给出：
```python 

from tensorflow.keras.models import Sequential
import tensorflow as tf

# embedding_text_1, embedding_text_2 为两个句子中每个词的embedding向量
# 维度为[B, N ,16], 其中B为batch size，N为句子中词的个数，16为embedding的维度
# 输出为[B, N ,N](expand dim 之前)
text_match_metrics =  tf.expand_dims(tf.matmul(embedding_text_1, embedding_text_2], transpose_b=True), axis=-1)


# 网络实现不严格遵循论文，仅展示核心思想。
text_model = Sequential()
text_model.add(tf.keras.layers.Conv2D(64, 3, strides=(1,1), padding='same', input_shape=(10, 10, 1), data_format='channels_last'))
text_model.add(tf.keras.layers.Conv2D(64, 1, strides=(1,1), padding='same', data_format='channels_last'))
text_model.add(tf.keras.layers.Conv2D(64, 3, strides=(1,1), padding='same', data_format='channels_last'))
text_model.add(tf.keras.layers.GlobalMaxPooling2D(data_format='channels_last'))
text_model.add(tf.keras.layers.Dense(1, activation='sigmoid'))

text_match_output = text_model(text_match_metrics)
```

### 总结
总的来说，MatchPyramid的思路简单，实现容易，整体的流程如下：

- 首先通过Word2Vec，或者glove等方法得到每个词的embedding。
- 接着通过每个词的embedding，构建两句文本之间的相似性矩阵。
- 最后将相似性矩阵通过卷积网络和多层感知机，输出0-1之间的相似性得分。

### 参考资料
[1] Liang P ,  Lan Y ,  Guo J , et al. Text Matching as Image Recognition.  2016.