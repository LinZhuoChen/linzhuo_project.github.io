---
layout: project
title: Quadrotor
permalink: /quadrotor/
---
<p align="center">
<br />
  <h3 align="center">Indoor Autonomous Flying Robot</h3>
  <p align="center">
    Jinyao Zhu* and Lin-Zhuo Chen*
    <br />
    <a href="https://arxiv.org/pdf/2004.04534.pdf" target="_black">[Code_1]</a>
    <a href="https://github.com/LinZhuoChen/Quadrotor" target="_black">[Code_2(no os) <i class="fab fa-github"></i>]</a>
    <br /> 
  </p>
</p>

<style type="text/css">
img{text-align: center; margin: 0 auto;}
</style>

## Abstract
<body>
    <p style="text-align:justify; text-justify:inter-ideograph">
    This project builds an indoor autonomous flying robot system, which can realize such as hovering, tracking and playing instruments. We use two cameras hung on the ceiling and fuse Inertial navigation sensor data to estimate the state of quadrotor based on Kalman Filter. 
    We build PID controler to control
    the position and attitude of quadrotor.

</p>
</body>

<div style="text-align:center" markdown="1">
![System overview](/images/system.png "System overview")
</div>

## Location
### 1. Homogeneous Transformation

<div style="text-align:center" markdown="1">
![homogeneous transformation](/images/homogeneous.png "homogeneous transformation")
</div>

We firstly find the homogeneous transformation matrix between ground coordinate
and camera coordinate. $P$ is the light label on quadrotor.

After we get $H$, we can calculate $M$ by $H$ and $m$ which can be expressed as $M=Hm$.

### 2. 

<div style="text-align:center" markdown="1">
![camera](/images/camera.png "camera")
</div>

### 3. 

<div style="text-align:center" markdown="1">
![position](/images/position_2.png "position")
</div>

<div style="text-align:center" markdown="1">
![position](/images/position_3.png "position")
</div>

## State Estimation
<body>
    <p style="text-align:justify; text-justify:inter-ideograph">
</p>
</body>

## Control
<body>
    <p style="text-align:justify; text-justify:inter-ideograph">
</p>
</body>

## Video