---
layout: page
title: Lin-Zhuo Chen's homepage
---
<style>

body { 
}


/*
a:hover,
a:focus {
    text-decoration: underline;
}
*/

.navbar,
.footer {
}

.gray-container {
    background-color: #eee;  
}

#about {
    margin-top: 50px;
    padding-top: 50px;
    padding-bottom: 50px;
    margin-bottom: 20px;
}

@keyframes me-img-hover {
    0% {
        transform: rotate(-15deg);
    }
    25% {
        transform: rotate(0deg);
    }
    50% {
        transform: rotate(15deg);
    }
    75% {
        transform: rotate(0deg);
    }
    100% {
        transform: rotate(-15deg);
    }
}

#me-img {
    width: 80%;
    /*border: 10px solid #FFFFCC;*/
    border: 8px solid #FFFFCC;
    /*transform: rotate(-15deg);*/
    box-shadow: 0px 0px 2px 3px rgba(255, 255, 255, 0.7), 0px 0px 10px 8px rgba(0, 0, 0, 0.1);
}

/*#me-img:hover {
    animation: me-img-hover 2000ms linear 100ms infinite forwards;
}*/

.long-work-img {
    width: 300px;
    height: 110px;
    /*auto !important;*/
    border: 1px solid lightgray;
    border-radius: 10px;
    /*height: 161px;*/
    -moz-box-shadow: 3px 3px 6px #888;
    -webkit-box-shadow: 3px 3px 6px #888;
    box-shadow: 3px 3px 6px #888;
}

.work-img {
    width: 399px;
    height: 220px;
    /*auto !important;*/
    border: 1px solid lightgray;
    border-radius: 10px;
    /*height: 161px;*/
    -moz-box-shadow: 3px 3px 6px #888;
    -webkit-box-shadow: 3px 3px 6px #888;
    box-shadow: 3px 3px 6px #888;
}

.detail {
    font-size: 13px;
    border: 1px solid #FFFFAA;
    background-color: #FFFFEE;
}

.prize {
    color: #B02020;
    font-weight: bold;
}

.work-block {
    padding-top: 10px;
    padding-bottom: 10px;
}

.section_title {
    /*color: #159957;*/
    color: black;
    /*font-family: "Tisa", Times, serif;*/
    font-family: "Verdana", Geneva, sans-serif;
    font-size: 20px;
    font-weight: 50;
}
#news{
    font-weight: bold;
    color: red;
}

#li-important{
    font-weight: bold;
    color: red;
}

</style>
<!-- <div style="float:left;border:solid 1px 000;margin:2px;"><img src="./images/linzhuo.jpg"  width="250" height="240" ></div> -->
<!-- # **About Me**
Hi~ 😄我是陈林卓，目前南开大学计算机学院硕士在读，导师为[程明明](https://mmcheng.net/cmm/)教授。

我的研究兴趣包括[计算机视觉](https://baike.baidu.com/item/%E8%AE%A1%E7%AE%97%E6%9C%BA%E8%A7%86%E8%A7%89/2803351?fr=aladdin) <i class="fas fa-eye"></i>  和[推荐系统](https://baike.baidu.com/item/%E6%8E%A8%E8%8D%90%E7%B3%BB%E7%BB%9F/10267357?fr=aladdin) <i class="fas fa-brain"></i> ，可以查看我的[简历](https://linzhuo.xyz/file/main.pdf)和[博客](https://linzhuo.xyz/tags/) <i class="fas fa-book"></i> 。

欢迎给我[留言](https://linzhuo.xyz/comments/) <i class="far fa-comments"></i>  和 [打赏 ](https://linzhuo.xyz/merger/) <i class="far fa-grimace"></i> 。 -->

## **About Me**
My name is Lin-Zhuo Chen. I am a graduate student at College of Computer Science, Nankai University.

My research interests are [Computer Vision](https://en.wikipedia.org/wiki/Computer_vision) <i class="fas fa-eye"></i>  and [Recommender System](https://en.wikipedia.org/wiki/Recommender_system) <i class="fas fa-brain"></i> . My [CV](https://linzhuo.xyz/file/main.pdf) and  [Blog ](https://linzhuo.xyz/tags/)<i class="fas fa-book"></i>  are avaliable .


## **Contact**

- <i class="fas fa-home"></i> : **<font color="#4590a3 ">lin</font><font color="#008000">zhuo</font>.<font color="#0000FF">xyz</font>**
- <i class="fas fa-envelope"></i> : **<font color="#4590a3">lin</font><font color="#008000">zhuo</font><font color="#0000FF">chen</font>@<font color="#FF0000 ">foxmail</font>.com**
- <i class="fab fa-zhihu"></i>  : **[<font color="#4590a3">you</font><font color="#008000">62580</font> ](https://www.zhihu.com/people/you62580)**
- <i class="fab fa-github"></i> : **[<font color="#4590a3">Lin</font><font color="#008000">Zhuo</font><font color="#0000FF">Chen</font>](https://github.com/LinZhuoChen)**

Feel free to contact me.

## **Education**

**Xidian University** Sept. 2014 - Jul. 2018 

**Nankai University** Sept. 2018 - Jul. 2021(expected) 

<!-- # **Update**
* 2020-12-20: One paper on spatial information guided convolution for RGBD semantic segmentation is accpeted by IEEE TIP.
* 2020-12-09: 更新博文：[policy gradient](https://linzhuo.xyz/_posts/%E5%BC%BA%E5%8C%96%E5%AD%A6%E4%B9%A0/2020-12-04-policy_gradient/)。
* 2020-12-05: 主页重构，修改了project页面，修改背景，留言和打赏页。 -->

## **Project**
<head>
    <div id="publication">
        <div class="row work-block">
            <div class="project col-xs-3">
                <img class="work-img" src="./images/s_conv1.png">
                <!-- <img class="work-img" src="/images/s_conv2.png"> -->
            </div>
            <div class="col-xs-8">
                <u>Lin-Zhuo Chen</u>, Zheng Lin, Ziqin Wang, Yong-Liang Yang, Ming-Ming Cheng. 
                <br>
                <strong>Spatial Information Guided Convolution for Real-Time RGBD Semantic Segmentation</strong>
                <br>
                <strong> IEEE TIP, 2021. (SCI-1, CCF-A)</strong>
                <br>
                [<a href="https://arxiv.org/abs/2004.04534" target="_blank">Paper</a>] [<a href="https://github.com/LinZhuoChen/SGNet" target="_blank">Code</a>][<a href="https://linzhuo.xyz/sgnet" target="_blank">Project</a>] 
                <br>
            </div>
        </div>
                <div class="row work-block">
            <div class="project col-xs-3">
                <img class="work-img" src="./images/fclick.jpg">
                <!-- <img class="work-img" src="/images/s_conv2.png"> -->
            </div>
            <div class="col-xs-8">
                Zheng Lin, Zhao Zhang , <u>Lin-Zhuo Chen</u> , Ming-Ming Cheng, Shao-Ping Lu
                <br>
                <strong>Interactive Image Segmentation with First Click Attention</strong>
                <br>
                <strong>IEEE CVPR, 2020</strong>
                <br>
                [<a href="http://openaccess.thecvf.com/content_CVPR_2020/papers/Lin_Interactive_Image_Segmentation_With_First_Click_Attention_CVPR_2020_paper.pdf" target="_blank">Paper</a>][<a href="https://www.lin-zheng.com/fclick/" target="_blank">Project</a>] [<a href="https://github.com/frazerlin/fcanet" target="_blank">Code</a>]   
                <br>
            </div>
        </div>
        <div class="row work-block">
            <div class="project col-xs-3">
                <img class="work-img" src="./images/LSA_module.png">
                <!-- <img class="work-img" src="/images/s_conv2.png"> -->
            </div>
            <div class="col-xs-8">
                <u>Lin-Zhuo Chen*</u>, Xuan-Yi Li*, Deng-Ping Fan, Kai Wang, Shao-Ping Lu, Ming-Ming Cheng
                <br>
                <strong>LSANet: Feature Learning on Point Sets by Local Spatial Aware Layer</strong>
                <br>
                <strong>preprint</strong>
                <br>
                [<a href="https://arxiv.org/pdf/1905.05442.pdf" target="_blank">Paper</a>] [<a href="https://github.com/LinZhuoChen/LSANet" target="_blank">Code</a>] 
                <br>
            </div>
        </div>
        <div class="row work-block">
            <div class="project col-xs-3">
                <img class="work-img" src="./images/system.png">
                <!-- <img class="work-img" src="/images/system.png"> -->
            </div>
            Jin-Yao Zhu, Lin-Zhuo Chen 
            <div class="col-xs-8">
                <strong>基于室内定位系统的四轴飞行器表演</strong>
                <br>
                [<a href="https://linzhuo.xyz/_posts/project/2019-11-29-quadrotor/" target="_blank">Project</a>] [<a href="https://github.com/JinyaoZhu/STM32F4-Quad" target="_blank">Code</a>]
                <br>
            </div>
        </div>
        <div class="row work-block">
            <div class="project col-xs-3">
                <img class="work-img" src="./images/hardware2.jpg">
                <!-- <img class="work-img" src="/images/fangdaqi.jpg"> -->
            </div>
            杨猛，陈林卓，曾羡霖 
            <div class="col-xs-8">
                <strong>自动增益控制宽带放大器</strong>
                <br>
                [<a href="https://linzhuo.xyz/_posts/project/2019-11-29-quadrotor/" target="_blank">Project</a>] [<a href="https://github.com/JinyaoZhu/STM32F4-Quad" target="_blank">Code</a>] 
                <br>
            </div>
        </div>
    </div>
</head>

<!DOCTYPE html>
<html>
<head>
<style>
    *{transition:all 1s ease 0s;}

    #showSlide{width:300px;height:200px;overflow:hidden;    border:20px solid #ccc;box-shadow:1px 1px 3px rgba(0,0,0,1);    }
    #inner{}
    .slide{width:300px;height:200px;display:inline-block;}
    label{border-radius: 5px;display: inline-block;width: 10px;height: 10px;background: #ccc;cursor:pointer;}
    input{display:none}
    #slide1:hover ~ #showSlide #inner{margin-top:0;}
    #slide2:hover ~ #showSlide #inner{margin-top:-200px;}
    #slide3:hover ~ #showSlide #inner{margin-top:-400px;}
    #slide4:hover ~ #showSlide #inner{margin-top:-600px;}
    label:hover{background:#000;}

</style>
</head>
<body>

    <input checked="checked" name="slider" id="slide1" type="radio">
    <input name="slider" id="slide2" type="radio">
    <input name="slider" id="slide3" type="radio">
    <input name="slider" id="slide4" type="radio">
    <input name="slider" id="slide5" type="radio">

<div id="showSlide">
    <div id="inner">
    <artical>
    <div class="slide"><img src="./images/s_conv1.png"></div>
    </artical>
    <artical>
    <div  class="slide"><img src="./images/s_conv1.png"></div>
    </artical>
    <artical>
    <div class="slide"><img src="./images/s_conv1.png"></div>
    </artical>
    <artical>
    <div  class="slide"><img src="./images/s_conv1.png"></div>
    </artical>
    </div>
</div>
<div id="controller">
    <label for="slide1"></label>
    <label for="slide2"></label>
    <label for="slide3"></label>
    <label for="slide4"></label>
</div>
</body>
</html>