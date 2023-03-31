# [OpenCV——轮廓外接矩形](https://www.cnblogs.com/zhaobinyouth/p/11698968.html)

### 1.寻找轮廓

```
void cv::findContours( InputOutputArray image, OutputArrayOfArrays contours, OutputArray hierarchy, int mode, int method, Point offset = Point() 
```

各个参数详解如下：

- Image表示输入图像，必须是二值图像，二值图像可以threshold输出、Canny输出、inRange输出、自适应阈值输出等。
- Contours获取的轮廓，每个轮廓是一系列的点集合
- Hierarchy轮廓的层次信息，每个轮廓有四个相关信息，分别是同层下一个、前一个、第一个子节点、父节点
- mode 表示轮廓寻找时候的拓扑结构返回 -RETR_EXTERNAL表示只返回最外层轮廓 -RETR_TREE表示返回轮廓树结构
  - CV_RETR_EXTERNAL：只检测外轮廓。忽略轮廓内部的洞
  - CV_RETR_LIST：检测所有轮廓，但不建立继承(包含)关系
  - CV_RETR_TREE：检测所有轮廓，并且建立所有的继承(包含)关系。也就是说用CV_RETR_EXTERNAL和CV_RETR_LIST方法的时候hierarchy这个变量是没用的，因为前者没有包含关系，找到的都是外轮廓，后者仅仅是找到所哟的轮廓但并不把包含关系区分。用TREE这种检测方法的时候我们的hierarchy这个参数才是有意义的
  - CV_RETR_CCOMP：检测所有轮廓，但是仅仅建立两层包含关系。外轮廓放到顶层，外轮廓包含的第一层内轮廓放到底层，如果内轮廓还包含轮廓，那就把这些内轮廓放到顶层去。
- Method表示轮廓点集合取得是基于什么算法，常见的是基于CHAIN_APPROX_SIMPLE链式编码方法

注意，如果图像底色是白色，则检测最外层的轮廓为图像边框

 

### 2.绘制轮廓外接矩形

绘制外接矩形包括两种：

- 绘制最大外接矩形

```
（Rect cv::boundingRect( InputArray points )）
```

其中，输入参数points为一系列点的集合（findContours中contours中的一个元素），对轮廓来说就是该轮廓的点集 返回结果是一个矩形，x, y, w, h

- 绘制最小外接矩形

```
RotatedRect cv::minAreaRect( InputArray points )
```

 

其中，输入参数points为一系列点的集合（findContours中contours中的一个元素） ，对轮廓来说就是该轮廓的点集 返回结果是一个旋转矩形，包含下面的信息： - 矩形中心位置 - 矩形的宽高 - 旋转角度。

 

### 3.代码

EdgeDetection.h

```
 1 #pragma once
 2 #include<opencv2/opencv.hpp>
 3 #include<iostream>
 4 
 5 using namespace std;
 6 using namespace cv;
 7 
 8 
 9 class EdgeDetection
10 {
11     cv::Mat m_img;
12     cv::Mat m_canny;
13 public:
14     EdgeDetection(cv::Mat iamge);
15     bool cannyProcess(unsigned int downThreshold,unsigned int upThreshold);
16     bool getContours();
17     
18     ~EdgeDetection();
19 };
```

EdgeDetection.cpp

```
#include "EdgeDetection.h"

EdgeDetection::EdgeDetection(cv::Mat image)
{
    m_img = image;
}
  
bool EdgeDetection::cannyProcess(unsigned int downThreshold, unsigned int upThreshold)
{
    bool ret=true;

    if (m_img.empty())
    {
        ret = false;
    }
 
    cv::Canny(m_img, m_canny, downThreshold, upThreshold);
    cv::imshow("Canny", m_canny); 
 
    return ret;
}
 
bool EdgeDetection::getContours()
{
    bool ret = true;
    if (m_canny.empty())
    {
        ret = false;
    }
    
	cv::Mat k = cv::getStructuringElement(cv::MORPH_RECT, 		cv::Size(3, 3), cv::Point(-1, -1));
	cv::dilate(m_canny, m_canny, k);
	imshow("dilate", m_canny);

	// 轮廓发现与绘制
	vector<vector<cv::Point> > contours;
	vector<Vec4i> hierarchy;
	findContours(m_canny, contours, cv::RETR_EXTERNAL, 							CHAIN_APPROX_SIMPLE, Point());
 
	for (size_t i = 0; i < contours.size();++i)
	{
		// 最大外接轮廓
    	cv::Rect rect = cv::boundingRect(contours[i]);
		cv::rectangle(m_img,rect,cv::Scalar(0,255,0),2,LINE_8);

		// 最小外接轮廓
		RotatedRect rrt = minAreaRect(contours[i]);
		Point2f pts[4];
		rrt.points(pts);
		// 绘制旋转矩形与中心位置
		for (int i = 0; i < 4; i++) 
		{
     	   line(m_img, pts[i % 4], pts[(i + 1) % 4], 					Scalar(0, 0, 255), 2, 8, 0);
		}
		Point2f cpt = rrt.center;
		circle(m_img, cpt, 2, Scalar(255, 0, 0), 2, 8, 0);
	}

	imshow("contours", m_img);
    return ret;
}
 
EdgeDetection::~EdgeDetection()
{
}
```

main.cpp

```
#include"EdgeDetection.h"

using namespace std;
using namespace cv;

int main(int argc, char* argv[])
{
    Mat src = imread("rect.jpg");
    if (src.empty())
    {
        cout << "image is empty" << endl;
        return -1;
    }

    imshow("input", src);

    EdgeDetection ed(src);

    ed.cannyProcess(80,160);
    ed.getContours();

    waitKey(0);
    return 0;
}
```

原图

![img](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202204111433967.png)

 

 canny

![img](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202204111433873.png)

 

 目标图

![img](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202204111433997.png)