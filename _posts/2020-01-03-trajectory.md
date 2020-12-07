---
title: 轨迹坐标的对齐及误差计算
categories: 计算机视觉
tags:
- 计算机视觉
---

#### 1.1 轨迹坐标的对齐

我们把两条轨迹的平移部分看成点集，然后求点集之间的ICP，得到两组轨迹之间的变换。

首先我们计算两组点的质心位置$p, p'$，然后计算每个点的去质心坐标：

$$
q_i = p_i - p \\
q_i' = p_i' - p' \\
$$

然后我们根据下列优化问题计算旋转矩阵:

$$
R^* = argmin_{R} \frac{1}{2}\sum_{i=1}^n ||q_i - R q_i'||^2
$$

根据上一步的$R$, 计算$t$:

$$
t^* = p - Rp'
$$

其中$R$的计算方式如下：

$$
W = \sum_{i=1}^n q_i q_i^T\\
W = U\Sigma V^T\\
R = UV^T
$$

本次实验用到的轨迹文件格式为：

$$
time_e, t_e, q_e, time_g, t_g. q_g,
$$

其中$t$表示平移，$q$表示单位四元数。

使用到的C++库为Eigen,pangolin,sophus. 代码实现如下：

```cpp
#include <sophus/se3.h>
#include <string>
#include <iostream>
#include <fstream>
#include <Eigen/Core>
#include <Eigen/Geometry>
#include <Eigen/SVD>
#include <time.h>
#include <unistd.h>
#include <pangolin/pangolin.h>

using namespace std;

string trajectory_file = "../compare.txt";

void DrawTrajectory(vector<Sophus::SE3, Eigen::aligned_allocator<Sophus::SE3>> poses1, vector<Sophus::SE3, Eigen::aligned_allocator<Sophus::SE3>> poses2);

int main(int argc, char** argv)
{
    vector<Sophus::SE3, Eigen::aligned_allocator<Sophus::SE3>> pose_estimated, pose_gt;
    vector<Eigen::Vector3d, Eigen::aligned_allocator<Eigen::Vector3d>> p_estimated, p_gt;
    vector<Eigen::Quaterniond, Eigen::aligned_allocator<Eigen::Quaterniond>> q_estimated, q_gt;
    ifstream pose_file;
    pose_file.open(trajectory_file, ios::in);
    if (!pose_file)
    {
        cout << "open file failure" << endl;
    }

    for (int i = 0; i < 612; i++)
    {
        double data[16] = {0};
        for(auto& d : data)
        {
            pose_file >> d;
        }
        Eigen::Vector3d t1(data[1], data[2], data[3]);
        Eigen::Quaterniond q1(data[7], data[4], data[5], data[6]);

        Eigen::Vector3d t2(data[9], data[10], data[11]);
        Eigen::Quaterniond q2(data[15], data[12], data[13], data[14]);

        Sophus::SE3 SE3_traj_estimate(q1, t1);
        Sophus::SE3 SE3_traj_gt(q2, t2);
        pose_estimated.push_back(SE3_traj_estimate);
        pose_gt.push_back(SE3_traj_gt);

        p_estimated.push_back(t1);
        p_gt.push_back(t2);

        q_estimated.push_back(q1);
        q_gt.push_back(q2);

    }
    // cout << "before aligning" << endl;
    // DrawTrajectory(pose_gt);
    // DrawTrajectory(pose_estimated);
    Eigen::Vector3d p_e_c (0, 0, 0);
    Eigen::Vector3d p_g_c (0, 0, 0);
    // calculate the center point
    for (int i = 0; i < 612; i++)
    {
        p_e_c += p_estimated[i];
        p_g_c += p_gt[i];
    }
    p_e_c = p_e_c / 612.0;
    p_g_c = p_g_c / 612.0;

    vector<Eigen::Vector3d, Eigen::aligned_allocator<Eigen::Vector3d>> p_estimated_c, p_gt_c;

    for (int i = 0; i < 612; i++)
    {
        p_estimated_c.push_back(p_estimated[i] - p_e_c);
        p_gt_c.push_back(p_gt[i] - p_g_c);
    }

    Eigen::Matrix3d W = Eigen::Matrix3d::Zero();
    for (int i = 0; i < 612; i++)
    {
        W += p_estimated_c[i] * p_gt_c[i].transpose();
    }

    cout << "W = " << endl;

    // SVD on W
    Eigen::JacobiSVD<Eigen::Matrix3d> svd (W, Eigen::ComputeFullU|Eigen::ComputeFullV);
    Eigen::Matrix3d U = svd.matrixU();
    Eigen::Matrix3d V = svd.matrixV();

    cout << "U = " << U <<endl;
    cout << "V = " << V <<endl;

    Eigen::Matrix3d R_ = U * (V.transpose());
    Eigen::Vector3d t_ = p_e_c - R_ * p_g_c;

    cout << "R = " << R_ <<endl;
    cout << "t = " << t_ <<endl;
    Sophus::SE3 T (R_, t_);

    cout << "After aligning" << endl;

    vector<Sophus::SE3, Eigen::aligned_allocator<Sophus::SE3>> pose_transformed;
    for(int i = 0; i < 612; i++)
    {
        pose_transformed.push_back(T * pose_gt[i]);
    }
    DrawTrajectory(pose_estimated, pose_transformed);
}

void DrawTrajectory(vector<Sophus::SE3, Eigen::aligned_allocator<Sophus::SE3>> poses1, vector<Sophus::SE3, Eigen::aligned_allocator<Sophus::SE3>> poses2) {
    if (poses1.empty() || poses2.empty()) {
        cerr << "Trajectory is empty!" << endl;
        return;
    }

    // create pangolin window and plot the trajectory
    pangolin::CreateWindowAndBind("Trajectory Viewer", 1024, 768);
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

        glLineWidth(2);
        for (size_t i = 0; i < poses1.size() - 1; i++) {
            glColor3f(1 - (float) i / poses1.size(), 0.0f, (float) i / poses1.size());
            glBegin(GL_LINES);
            auto p1 = poses1[i], p2 = poses1[i + 1];
            glVertex3d(p1.translation()[0], p1.translation()[1], p1.translation()[2]);
            glVertex3d(p2.translation()[0], p2.translation()[1], p2.translation()[2]);
            glEnd();
        }

        for (size_t i = 0; i < poses2.size() - 1; i++) {
            glColor3f(1 - (float) i / poses2.size(), 0.0f, (float) i / poses2.size());
            glBegin(GL_LINES);
            auto p1 = poses2[i], p2 = poses2[i + 1];
            glVertex3d(p1.translation()[0], p1.translation()[1], p1.translation()[2]);
            glVertex3d(p2.translation()[0], p2.translation()[1], p2.translation()[2]);
            glEnd();
        }
        pangolin::FinishFrame();
        usleep(5000);   // sleep 5 ms
    }
}
```

结果如下：

![avatar](/images/trajectory_beforeicp.png)
<center>对齐前的轨迹</center>


![avatar](/images/trajectory_aftericp.png)
<center>对齐后的轨迹</center>


#### 1.2 轨迹的误差计算

我们设真实轨迹为$T_g$, 估计轨迹为$T_e$，在完成轨迹对齐之后，对于任意第$i$个位姿，误差可定义为:

$$
e_i = ||log(T_{gi}^{-1}T_{ei})^{\vee}||_2
$$

即两个位姿之差的李代数二范数，所以可以定义两条轨迹的误差为:

$$
RMSE(g,e)=\sqrt{\frac{1}{n} \sum_{i=1}^{n} e_i^2}
$$

本次实验用到的两个轨迹文件格式为：

$$
time, t, q
$$

其中$t$表示平移，$q$表示单位四元数。

使用到的C++库为Eigen,pangolin,sophus. 代码实现如下：

```cpp
#include <sophus/se3.h>
#include <string>
#include <iostream>
#include <fstream>
#include <time.h>
#include <unistd.h>
// need pangolin for plotting trajectory
#include <pangolin/pangolin.h>

using namespace std;

string trajectory_estimate = "../estimated.txt";
string trajectory_gt = "../groundtruth.txt";

void DrawTrajectory(vector<Sophus::SE3, Eigen::aligned_allocator<Sophus::SE3>> poses1, vector<Sophus::SE3, Eigen::aligned_allocator<Sophus::SE3>> poses2);

int main(int argc, char** argv)
{
    vector<Sophus::SE3, Eigen::aligned_allocator<Sophus::SE3>> pose_estimated, pose_gt;
    ifstream pose_estimated_file, pose_gt_file;

    pose_estimated_file.open(trajectory_estimate, ios::in);
    pose_gt_file.open(trajectory_gt, ios::in);
    if(!pose_estimated_file && !pose_gt_file)
    {
        cout << "Open file failure" <<endl;
    }

    // Estimate
    for (int i = 0; i < 612; i++)
    {
        double data[8] = {0};
        for(auto& d : data)
        {
            pose_estimated_file >> d;
        }
        Eigen::Vector3d t(data[1], data[2], data[3]);
        Eigen::Quaterniond q(data[7], data[4], data[5], data[6]);
        Sophus::SE3 SE3_traj(q, t);
        pose_estimated.push_back(SE3_traj);
    }
    // gt
    for (int i = 0; i < 612; i++)
    {
        double data[8] = {0};
        for(auto& d : data)
        {
            pose_gt_file >> d;
        }
        Eigen::Vector3d t(data[1], data[2], data[3]);
        Eigen::Quaterniond q(data[7], data[4], data[5], data[6]);
        Sophus::SE3 SE3_traj(q, t);
        pose_gt.push_back(SE3_traj);
    }
    // error
    double rmse_square = 0;
    for (int i = 0; i < 612; i++)
    {
        double e = Sophus::SE3::log(pose_gt[i].inverse() * pose_estimated[i]).norm();
        cout << "The error is : \n" << e << endl;
        rmse_square += e*e;
    }
    rmse_square = sqrt(rmse_square / 612.0);
    cout << "The RMSE is : \n" << rmse_square << endl;
    DrawTrajectory(pose_estimated, pose_gt);
}

/*******************************************************************************************/
void DrawTrajectory(vector<Sophus::SE3, Eigen::aligned_allocator<Sophus::SE3>> poses1, vector<Sophus::SE3, Eigen::aligned_allocator<Sophus::SE3>> poses2) {
    if (poses1.empty() || poses2.empty()) {
        cerr << "Trajectory is empty!" << endl;
        return;
    }

    // create pangolin window and plot the trajectory
    pangolin::CreateWindowAndBind("Trajectory Viewer", 1024, 768);
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

        glLineWidth(2);
        for (size_t i = 0; i < poses1.size() - 1; i++) {
            glColor3f(1 - (float) i / poses1.size(), 0.0f, (float) i / poses1.size());
            glBegin(GL_LINES);
            auto p1 = poses1[i], p2 = poses1[i + 1];
            glVertex3d(p1.translation()[0], p1.translation()[1], p1.translation()[2]);
            glVertex3d(p2.translation()[0], p2.translation()[1], p2.translation()[2]);
            glEnd();
        }

        for (size_t i = 0; i < poses2.size() - 1; i++) {
            glColor3f(1 - (float) i / poses2.size(), 0.0f, (float) i / poses2.size());
            glBegin(GL_LINES);
            auto p1 = poses2[i], p2 = poses2[i + 1];
            glVertex3d(p1.translation()[0], p1.translation()[1], p1.translation()[2]);
            glVertex3d(p2.translation()[0], p2.translation()[1], p2.translation()[2]);
            glEnd();
        }
        pangolin::FinishFrame();
        usleep(5000);   // sleep 5 ms
    }

}
```

