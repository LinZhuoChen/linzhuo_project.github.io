---
title: 四轴飞行器
categories: project
tags:
- project
---

本项目完成了对四轴飞行器的室内定位，控制以及路径规划等任务。

## 1. 硬件参数

- **机架**：轴距为250mm的X型， 塑料四轴飞行器机架。

- **MCU** : STM32F405RGT6。

- **传感器**:   MPU6000, 超声波传感器，PX4FLOW。

- **动力系统**: MN1806电机，DALRC BL12A电子调速器，半径60mm，螺距为45mm的塑料螺旋桨，11.1V，1300mAh的锂聚合物动力电池。

- **室内定位系统**：两个京航JHSM36BF-R近红外工业相机。

## 2.动力学模型与控制

### 2.1 电机模型

令每个螺旋桨的升力为$F_i$, 电子调速器的输入为$u_i$, 螺旋桨的反扭距为$M_i$, 则有

$$
F_i = k_F u_i \\  
M_i = k_m u_i  \tag1
$$

### 2.2 四轴飞行器的运动方程

我们设飞行器的机体坐标系为$\Gamma_b$, 参考坐标系为$\Gamma_w$。飞行器具有六个自由度，包括旋转和位移。本项目以$Z-X-Y$次序的欧拉角来描述四轴飞行器的姿态：


$$
\Omega = 
\left(
 \begin{matrix}
   \varphi	\\
   \theta	\\
   \psi 
  \end{matrix} 
\right) \tag2
$$


其中$\varphi, \theta, \psi$分别表示四轴飞行器的横滚角，俯仰角和航偏角。

因此我们可以得到$\Gamma_b$到$\Gamma_w$的旋转矩阵$R_b^w$:


$$
R_w^b = 
\left(
\begin{matrix}
   c\psi c\theta & c\psi s\theta s\varphi - s\psi c\varphi& c\psi s\theta c\varphi + s\psi s\varphi	\\
   s\psi c\theta & s\psi s\theta s\varphi + c\psi c\varphi& s\psi s\theta c\varphi - c\psi s\varphi	\\
   -s\theta & c\theta s\varphi &  c\theta c\varphi
  \end{matrix}
  \right) \tag3
$$


飞行器的位移以$r$来表示：


$$
r = 
\left(
 \begin{matrix}
   r_x	\\
   r_y	\\
   r_z
  \end{matrix} 
\right) \tag4
$$


分别表示其参考坐标系下的$x,y,z$坐标。

飞行器位移的运动方程可以通过牛顿方程来描述：


$$
\ddot{r} =R_b^w \left(
 \begin{matrix}
   0	\\
   0	\\
   F 
  \end{matrix} 
\right) - \left(
 \begin{matrix}
   0	\\
   0	\\
   g 
  \end{matrix} 
\right) \tag5
$$


欧拉方程为:


$$
I\dot{\omega} = M - \omega \times I\omega  \tag6
$$


即:


$$
\dot{\omega} = I^{-1}(M - \omega \times I\omega) \tag7
$$


其中$I$ 为四轴飞行器的转动惯量，再由欧拉角的定义，可得欧拉角微分$\dot{\Omega}$与机体角速度的关系:


$$
\dot{\Omega} =\left(
 \begin{matrix}
   \dot{\varphi}	\\
   \dot{\theta}	\\
   \dot{\psi}
  \end{matrix} 
\right)   =\left(
 \begin{matrix}
   1 & tan(\theta)sin(\varphi) & tan(\theta)cos(\varphi)	\\
   \dot{\theta}	& cos(\varphi) & -sin(\varphi)\\
   \dot{\psi} & sec(\theta)sin(\varphi) & sec(\theta)cos(\varphi)
  \end{matrix} 
\right) \omega = W\omega \tag8
$$


 因此，飞行器的非线性状态方程总结如下:


$$
x =\left(
 \begin{matrix}
   r	\\
   \dot{r}	\\
   \Omega \\
   \omega \\
  \end{matrix} 
\right), 
\dot{x}=\left(
 \begin{matrix}
   \dot{r}	\\
   R_b^w\left(
 \begin{matrix}
   0	\\
   0	\\
   F 
  \end{matrix} 
\right) - \left(
 \begin{matrix}
   0	\\
   0	\\
   g 
  \end{matrix} 
\right) 	\\
   W\omega \\
   I^{-1}(M - \omega \times I\omega) 
  \end{matrix} 
\right) \tag9
$$


### 2.3 控制律

飞行器的控制部分主要分为：姿态控制与位移控制。位移控制器根据目标位移，速度以及加速度计算出飞行器的目标态，通过改变飞行器的姿态来产生相应的线加速度，从而实现位移控制。姿态控制器作为飞行器飞行控制
的底层控制器。其中控制器使用PID算法。四轴飞行器的控制系统如下：

![](/images/control.png)

通过简单分析，可得每个螺旋桨的升力$F_i$, 反力矩$M_i$与绕机体坐标轴的力矩$M$的关系如下:



$$
M =\left(
 \begin{matrix}
   M_x	\\
   M_y	\\
   M_z
  \end{matrix} 
\right) = 
\left(
 \begin{matrix}
   (-F_1-F_2+F_3+F_4)L	\\
   (-F_1+F_2+F_3-F_4)L	\\
   (-M_1+M_2-M_3+M_4)\sqrt 2L
  \end{matrix} 
\right) \tag{10}
$$



由公式(1)可得到$F_i$ 与 $M_i$之间的关系:



$$
M_i = \frac{k_M}{k_F} F_i = k_mF_i \tag{11}
$$



因此可得:


$$
M =\left(
 \begin{matrix}
   -L & -L & L & L	\\
   -L & L  & L & -L \\
   -k_m\sqrt 2L & k_m\sqrt 2L & -k_m\sqrt 2L & k_m\sqrt 2L
  \end{matrix} 
\right) \left(
 \begin{matrix}
   F_1	\\
   F_2	\\
   F_3 \\
   F_4
  \end{matrix} 
\right) \tag{12}
$$


我们令:


$$
X = \left(
 \begin{matrix}
   -L & -L & L & L	\\
   -L & L  & L & -L \\
   -k_m\sqrt 2L & k_m\sqrt 2L & -k_m\sqrt 2L & k_m\sqrt 2L
  \end{matrix} 
\right) , F =  \left(
 \begin{matrix}
   F_1	\\
   F_2	\\
   F_3 \\
   F_4
  \end{matrix} 
\right)
$$



飞行器的误差欧拉角$\Omega_{error}$为:


$$
\Omega_{error} = \Omega_{des} - \Omega
$$


飞行器的姿态控制器采用角速度PD控制， 结构如下:

![](/images/attitude.png)

飞行器的位移控制主要分为两步，一是位移的PID控制，二是目标加速度到角速度的转换，位移控制器结构如下:



![](/images/position.png)

其中:


$$
\varphi_{des}=asin(-\frac{\ddot{r_{des,y}}}{g}) \\
\theta_{des}=asin(\frac{\ddot{r_{des,x}}}{gcos(\varphi)})
$$

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

