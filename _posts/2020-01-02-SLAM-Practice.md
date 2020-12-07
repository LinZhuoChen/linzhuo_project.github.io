---
title: 利用视差图恢复图片3D结构
categories: 计算机视觉
tags:
- 计算机视觉
---

深度的计算公式如下:

$$
z = \frac{f \cdot b}{d}
$$

其中$f$ 为相机的焦距，$b$为相机的基线，$d$为左右视图的视差。得到像素的深度后，我们通过相机投影的逆过程得到像素的三维坐标，其中:

$$
x = \frac{u - c_x}{f_x} \cdot z 
y = \frac{v - c_y}{f_y} \cdot z 
z = z
$$

最后，我们用pangolin将结果可视化，使用的c++库有：Eigen，pangolin，opencv, 核心实现代码如下:

```cpp
#include <opencv2/opencv.hpp>
#include <string>
#include <Eigen/Core>
#include <pangolin/pangolin.h>
#include <unistd.h>

using namespace std;
using namespace Eigen;

// 文件路径，如果不对，请调整
string left_file = "../left.png";
string right_file = "../right.png";
string disparity_file = "../disparity.png";

// 在panglin中画图，已写好，无需调整
void showPointCloud(const vector<Vector4d, Eigen::aligned_allocator<Vector4d>> &pointcloud);

int main(int argc, char **argv) {

    // 内参
    double fx = 718.856, fy = 718.856, cx = 607.1928, cy = 185.2157;
    // 间距
    double d = 0.573;

    // 读取图像
    cv::Mat left = cv::imread(left_file, 0);
    cv::Mat right = cv::imread(right_file, 0);
    cv::Mat disparity = cv::imread(disparity_file, 0); // disparty 为CV_8U,单位为像素

    if(left.data == nullptr && right.data == nullptr && disparity.data == nullptr)
    {
        cerr << "文件"  << "不存在" << endl;
        return 1;
    }
    // 生成点云
    vector<Vector4d, Eigen::aligned_allocator<Vector4d>> pointcloud;
    double z = 0;
    // TODO 根据双目模型计算点云
    // 如果你的机器慢，请把后面的v++和u++改成v+=2, u+=2
    for (int v = 0; v < left.rows; v++)
        for (int u = 0; u < left.cols; u++) {
            Vector4d point(0, 0, 0, left.at<uchar>(v, u) / 255.0); // 前三维为xyz,第四维为颜色
            // start your code here (~6 lines)
            // 根据双目模型计算 point 的位置
            unsigned int disp = disparity.ptr<unsigned short >(v)[u];
            if (disp == 0)
            {
                cout << "disp == 0" << endl;
            }
            z = (fx * d * 1000) / disp;
            point[0] = (u - cx) / fx * z;
            point[1] = (v - cy) / fy * z; 
            point[2] = z; 
            // end your code here
            pointcloud.push_back(point);
        }

    // 画出点云
    showPointCloud(pointcloud);
    return 0;
}

void showPointCloud(const vector<Vector4d, Eigen::aligned_allocator<Vector4d>> &pointcloud) {

    if (pointcloud.empty()) {
        cerr << "Point cloud is empty!" << endl;
        return;
    }

    pangolin::CreateWindowAndBind("Point Cloud Viewer", 1024, 768);
    glEnable(GL_DEPTH_TEST);
    glEnable(GL_BLEND);
    glBlendFunc(GL_SRC_ALPHA, GL_ONE_MINUS_SRC_ALPHA);

    pangolin::OpenGlRenderState s_cam(
            pangolin::ProjectionMatrix(1024, 768, 500, 500, 512, 389, 0.1, 1000),
            pangolin::ModelViewLookAt(0, -0.1, -1.8, 0, 0, 0, 0.0, -1.0, 0.0)
    );

    pangolin::View &d_cam = pangolin::CreateDisplay()
            .SetBounds(0.0, 1.0, pangolin::Attach::Pix(175), 1.0, -1024.0f / 768.0f)
            .SetHandler(new pangolin::Handler3D(s_cam));

    while (pangolin::ShouldQuit() == false) {
        glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);

        d_cam.Activate(s_cam);
        glClearColor(1.0f, 1.0f, 1.0f, 1.0f);

        glPointSize(2);
        glBegin(GL_POINTS);
        for (auto &p: pointcloud) {
            glColor3f(p[3], p[3], p[3]);
            glVertex3d(p[0], p[1], p[2]);
        }
        glEnd();
        pangolin::FinishFrame();
        usleep(5000);   // sleep 5 ms
    }
    return;
}

```

结果如下:

![avatar](/images/left.png)
<center> 左图 </center>


![avatar](/images/disparity.png)

<center> 生成的点云图 </center>

