---
layout: page
title: Lin-Zhuo Chen's homepage
---
# **关于我**

我是陈林卓，目前南开大学计算机学院硕士在读，我将作为算法工程师加入[字节跳动](https://bytedance.com/) 。

我的研究兴趣包括计算机视觉和推荐系统，可以在这查看我的[简历](https://linzhuo.xyz/file/main.pdf) 。你可以在这查看我的[博客](https://linzhuo.xyz/tags/)。

# **About Me**
My name is Lin-Zhuo Chen. I am a graduate student at College of Computer Science, Nankai University. I will join  [ByteDance](https://bytedance.com/) worked as an algorithm engineer .
My research interests are Computer Vision and Recommend System. My [CV](https://linzhuo.xyz/file/main.pdf) is avaliable .
You can read my blog [here](https://linzhuo.xyz/tags/). 

# **Contact**

Feel free to contact me by [email](linzhuochen@foxmail.com) or [zhihu](https://www.zhihu.com/people/you62580/).

# **Education**

School of Electronic Engineering, XiDian University, Xi'an, Sept. 2014 - Jul. 2018 

College of Computer Science, NanKai University, TianJin, Sept. 2018 - Jul. 2021(expected) 

# **Project**

### 1. [室内飞行机器人的定位，控制及路径规划](https://linzhuo.xyz/_posts/project/2019-11-29-quadrotor/)

本项目完成了对四轴飞行器的室内定位，控制以及路径规划等任务。其项目框图如下：

![](/images/control.png)



演示视频如下(youtube):

<div class="embed-container">
  <iframe
      src="https://www.youtube.com/embed/XSEBEPnCcRU"
      width="700"
      height="480"
      frameborder="0"
      allowfullscreen="">
  </iframe>
</div>

### 2. [Spatial Information Guided Convolution for Real-Time RGBD Semantic Segmentation](https://arxiv.org/abs/2004.04534v1)
3D spatial information is known to be beneficial to the semantic segmentation task. Most existing methods take 3D spatial data as an additional input, leading to a two-stream segmentation network that processes RGB and 3D spatial information separately. This solution greatly increases the inference time and severely limits its scope for real-time applications. To solve this problem, we propose **Spatial information guided Convolution (S-Conv)**, which allows efficient RGB feature and 3D spatial information integration. **S-Conv is competent to infer the sampling offset of the convolution kernel guided by the 3D spatial information, helping the convolutional layer adjust the receptive field and adapt to geometric transformations. S-Conv also incorporates geometric information into the feature learning process by generating spatially adaptive convolutional weights**. The capability of perceiving geometry is largely enhanced without much affecting the amount of parameters and computational cost. We further embed S-Conv into a semantic segmentation network, called Spatial information Guided convolutional Network (SGNet), resulting in real-time inference and state-of-the-art performance on NYUDv2 and SUNRGBD datasets.

The structure of S-Conv and SGNet are shown as bellow:

![img](https://img-blog.csdnimg.cn/20200422165201255.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpanVlemh1ODEyOA==,size_16,color_FFFFFF,t_70)

<center>S-Conv的结构</center>
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200422165851298.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpanVlemh1ODEyOA==,size_16,color_FFFFFF,t_70)

<center>SGNet的结构</center>
### 3. [LSANet: Feature Learning on Point Sets by Local Spatial Aware Layer](https://github.com/LinZhuoChen/LSANet)

We propose a new network layer, named **Local Spatial Aware** (LSA) Layer, to model geometric structure in local region accurately and robustly. Each feature extracting operation in LSA layer is related to **Spatial Distribution Weights** (SDWs), which are learned based on the spatial distribution in local region, to establish a strong link with inherent geometric shape. The experiments show that our LSA-based network, named **LSANet**, can achieve on par or better performance than the state-of-the-art methods when evaluating on the challenging benchmark datasets. The network architecture of LSANet and LSA module are shown below.
![LSANet1](/images/LSA_module.png)

![LSANet2](/images/LSANet.png)

|   Method   | ModelNet40(Acc) | ShapeNet(MIoU) | S3DIS(MIoU) |
| :--------: | :-------------: | :------------: | :---------: |
|  PointNet  |      89.2%      |     83.7%      |      -      |
| PointNet++ |      90.7%      |     85.1%      |    47.6%    |
|   LSANet   |      93.2%      |     85.6%      |    62.2%    |



# **Publication**

Interactive Image Segmentation with First Click Attention, Zheng Lin, Zhao Zhang, **Lin-Zhuo Chen**, Ming-Ming Cheng, Shao-Ping Lu, **IEEE CVPR**, 2020

Spatial Information Guided Convolution for Real-Time RGBD Semantic Segmentation, **Lin-Zhuo Chen**, Zheng Lin, Ziqin Wang, Yong-Liang Yang,Ming-Ming Cheng, under TIP review

LSANet: Feature Learning on Point Sets by Local Spatial Aware Layer, **Lin-Zhuo Chen**, Xuan-Yi Li, Deng-Ping Fan, Kai Wang, Shao-Ping Lu, Ming-Ming Cheng, arXiv preprint arXiv:1905.05442

# **Skill**

TBD

# **Leadership**

TBD
