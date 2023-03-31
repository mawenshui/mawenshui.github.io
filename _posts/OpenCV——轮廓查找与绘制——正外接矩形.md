# OpenCV——轮廓查找与绘制——正外接矩形

# 一、简介

![img](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202204111502598.png)

![img](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202204111502590.png)

# 二、外接矩形的查找绘制

```c++
#include "opencv2/opencv.hpp"

using namespace cv;

void main()
{
    //外接矩形的查找绘制
    Mat srcImg =imread("E://12.jpg");
    imshow("src",srcImg);
    Mat dstImg = srcImg.clone();  //原图备份
    cvtColor(srcImg, srcImg, CV_BGR2GRAY); //转为灰度图
    threshold(srcImg, srcImg, 100, 255, CV_THRESH_BINARY); //二值化

    vector<vector<Point>> contours;
    vector<Vec4i> hierarcy;
    findContours(srcImg, contours, hierarcy, CV_RETR_EXTERNAL, CHAIN_APPROX_NONE); //查找轮廓
    vector<Rect> boundRect(contours.size()); //定义外接矩形集合
    //drawContours(dstImg, contours, -1, Scalar(0, 0, 255), 2, 8);  //绘制轮廓
    int x0=0, y0=0, w0=0, h0=0;
    for(int i=0; i<contours.size(); i++)
    {
        boundRect[i] = boundingRect((Mat)contours[i]); //查找每个轮廓的外接矩形
        drawContours(dstImg, contours, i, Scalar(0, 0, 255), 2, 8);  //绘制轮廓
        x0 = boundRect[i].x;  //获得第i个外接矩形的左上角的x坐标
        y0 = boundRect[i].y; //获得第i个外接矩形的左上角的y坐标
        w0 = boundRect[i].width; //获得第i个外接矩形的宽度
        h0 = boundRect[i].height; //获得第i个外接矩形的高度
        rectangle(dstImg, Point(x0, y0), Point(x0+w0, y0+h0), Scalar(0, 255, 0), 2, 8); //绘制第i个外接矩形
    }
    imshow("boundRect", dstImg);
    waitKey(0);
}
```

![img](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202204111502561.png)

# 三、分割硬币轮廓并计数

```c++
#include "opencv2/opencv.hpp"
#include<iostream>
using namespace cv;
using namespace std;
void main()
{
    //分割硬币轮廓
    Mat srcImg =imread("E://33.png");
    imshow("src", srcImg);
    Mat dstImg = srcImg.clone();  //原图备份
    cvtColor(srcImg, srcImg, CV_BGR2GRAY); //转为灰度图
    threshold(srcImg, srcImg, 100, 255, CV_THRESH_BINARY); //二值化
    Mat element = getStructuringElement(MORPH_RECT, Size(3, 3), Point(-1, -1)); //获得结构元素
    dilate(srcImg, srcImg, element); //膨胀操作
    imshow("dilate",srcImg);

    vector<vector<Point>> contours;
    vector<Vec4i> hierarcy;
    findContours(srcImg, contours, hierarcy, CV_RETR_EXTERNAL, CHAIN_APPROX_NONE); //查找轮廓
    vector<Rect> boundRect(contours.size()); //定义外接矩形集合
    int x0=0, y0=0, w0=0, h0=0,num=0;
    for(int i=0; i<contours.size(); i++)
    {
        boundRect[i] = boundingRect((Mat)contours[i]); //查找每个轮廓的外接矩形
        drawContours(dstImg, contours, i, Scalar(0, 0, 255), 2, 8);  //绘制轮廓
        x0 = boundRect[i].x;
        y0 = boundRect[i].y;
        w0 = boundRect[i].width;
        h0 = boundRect[i].height;
        if(w0>30 && h0>30)//筛选
        {
            rectangle(dstImg, Point(x0, y0), Point(x0+w0, y0+h0), Scalar(0, 255, 0), 2, 8); //绘制第i个外接矩形
            num++;
        }
    }
    cout<<"硬币数量："<<num;
    imshow("boundRect", dstImg);
    waitKey(0);
}
```

![img](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202204111502591.png)

#  四、简单车牌字符分隔

```c++
#include "opencv2/opencv.hpp"
using namespace cv;
void main()
{
    //---简单车牌字符分隔
    Mat srcImg =imread("E://Car2.jpg");
    Mat dstImg = srcImg.clone();  //原图备份
    medianBlur(srcImg, srcImg, 5);  //中值滤波
    cvtColor(srcImg, srcImg, CV_BGR2GRAY); //转为灰度图
    threshold(srcImg, srcImg, 100, 255, CV_THRESH_BINARY); //二值化
    imshow("threshold", srcImg);
    imwrite("F://car0.jpg", srcImg);

    vector<vector<Point>> contours;
    vector<Vec4i> hierarcy;
    findContours(srcImg, contours, hierarcy, CV_RETR_TREE, CHAIN_APPROX_NONE); //查找所有轮廓
    vector<Rect> boundRect(contours.size()); //定义外接矩形集合
    int x0=0, y0=0, w0=0, h0=0;
    for(int i=0; i<contours.size(); i++)
    {
        boundRect[i] = boundingRect((Mat)contours[i]); //查找每个轮廓的外接矩形
        x0 = boundRect[i].x;
        y0 = boundRect[i].y;
        w0 = boundRect[i].width;
        h0 = boundRect[i].height;
        if(w0>srcImg.cols/12 && w0<srcImg.cols/5 && h0>srcImg.rows/6 && h0<srcImg.rows*5/6)
        {
            char pic_name[10];
            sprintf(pic_name, "F:\\%d.bmp", i);
            Mat ROI = dstImg(Rect(x0, y0, w0, h0));
            imwrite(pic_name, ROI);
            rectangle(dstImg, Point(x0, y0), Point(x0+w0, y0+h0), Scalar(0, 255, 0), 2, 8); //绘制第i个外接矩形
        }
    }
    imshow("boundRect", dstImg);
    waitKey(0);
}
```

![img](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202204111502535.png)