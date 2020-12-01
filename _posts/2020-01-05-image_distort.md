---
title: 图像去畸变
categories: 计算机视觉
tags:
- 计算机视觉
---

图片畸变前后的坐标变换为:

$$
x_{distorted} = x(1 + k_1r^2+k_2r^4) + 2p_1xy + p_2 (r^2+2x^2) \\
y_{distorted} = y(1 + k_1r^2+k_2r^4) + 2p_2xy + p_1 (r^2+2y^2)
$$

其中$x,y$为去畸变后的坐标，$x_{distorted}, y_{distorted}$为去畸变前的坐标，其它均为参数。

图片去畸变的核心实现代码如下，使用的C++库为opencv：

```cpp
#include <opencv2/opencv.hpp>
#include <string>
#include <math.h>

using namespace std;

string image_file = "../test.png";   // 请确保路径正确

int main(int argc, char **argv) {

    double k1 = -0.28340811, k2 = 0.07395907, p1 = 0.00019359, p2 = 1.76187114e-05;
    // 内参
    double fx = 458.654, fy = 457.296, cx = 367.215, cy = 248.375;

    cv::Mat image = cv::imread(image_file,0);   // 图像是灰度图，CV_8UC1
    int rows = image.rows, cols = image.cols;
    cv::Mat image_undistort = cv::Mat(rows, cols, CV_8UC1);   // 去畸变以后的图

    // 计算去畸变后图像的内容
    for (int v = 0; v < rows; v++)
        for (int u = 0; u < cols; u++) {

            double u_distorted = 0, v_distorted = 0;
            double u_2d = 0, v_2d = 0;
            double r = 0;
            u_2d = (u - cx) / fx;
            v_2d = (v - cy) / fy;

            r = sqrt(pow(u_2d, 2) + pow(v_2d, 2));
            u_distorted = u_2d * (1 + k1 * pow(r, 2) + k2 * pow(r, 4)) + 
                            2 * p1 * u_2d * v_2d + p2 * (pow(r, 2) + 2 * pow(u_2d, 2));
            v_distorted = v_2d * (1 + k1 * pow(r, 2) + k2 * pow(r, 4)) + 
                             p1 * (pow(r, 2) + 2 * pow(v_2d, 2)) + 2 * p2 * u_2d * v_2d;

            u_distorted = u_distorted * fx + cx;
            v_distorted = v_distorted * fy + cy;

            // 赋值 (最近邻插值)
            if (u_distorted >= 0 && v_distorted >= 0 && u_distorted < cols && v_distorted < rows) {
                image_undistort.at<uchar>(v, u) = image.at<uchar>((int) v_distorted, (int) u_distorted);
            } else {
                image_undistort.at<uchar>(v, u) = 0;
            }
        }
    // 画图去畸变后图像
    cv::imshow("image undistorted", image_undistort);
    cv::waitKey();

    return 0;
}
```

去畸变的结果如下:

![avatar](/images/test.png)

<center>去畸变前</center>
![avatar](/images/undistorted.png)

<center>去畸变后</center>