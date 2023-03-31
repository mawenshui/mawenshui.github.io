# OpenCV切割图像与合并图像

[原文链接](https://blog.csdn.net/zhangzhe_0305/article/details/79849490?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_title~default-0.pc_relevant_antiscanv2&spm=1001.2101.3001.4242.1&utm_relevant_index=3)

**首先是图像的分割**

```cpp
Mat spliteTest(Mat image)
{
    long cols = image.cols;
    long rows = image.rows;
    Mat nullImage(Size(cols/2, rows), CV_LOAD_IMAGE_GRAYSCALE);  //取一半列数的大小
    Mat subImage = image(Range::all(), Range(0,cols/2));   //将需要的图像读取出来
    subImage.copyTo(nullImage(Rect(0, 0, cols/2, rows)));   //复制进指定的Mat里
    return nullImage;
}


int main()
{
    Mat image = imread("Red.bmp", CV_LOAD_IMAGE_GRAYSCALE);  
 
    Mat nullImage = spliteTest(image);
 
    imshow("nullImage", nullImage);  //分割后的图像
 
    imshow("redImage", image);   //分割前的图像
 
}
```



**其次是图像的合并**

```cpp
Mat spliteImageAndCon(Mat image)
 
{
    long cols = image.cols;
    long rows = image.rows;
    Mat nullImage(Size(cols, rows), CV_LOAD_IMAGE_GRAYSCALE);
    long con_cols = 0;
    long con_rows = 0;
    long cols_div = cols / width;
    long cols_remain = cols % width;
    for (long i = 0; i <cols_div; i++)
    {
    //取全部行，
    Mat subImage = image(Range::all(), Range(i*width, (i + 1)*width)); //确定目标块的位置
    subImage.copyTo(nullImage(Rect(i*width, 0, width, rows)));  //确定合并的位置
    }
    Mat subImage = image(Range::all(), Range(cols_div*width, cols_div*width +  cols_remain)); //合并剩下的部分即可
    subImage.copyTo(nullImage(Rect(cols_div*width, 0, cols_remain, rows)));
    
    return nullImage;
}
```



**下面附上全部的测试代码：**

```cpp
#include<stdio.h>
#include<cuda_runtime.h>
#include<device_launch_parameters.h>
#include <iostream>
#include <opencv/highgui.h>
#include <string>
#include <math.h>

using namespace std;
using namespace cv;
const int width = 200; //从左往右多少列

Mat spliteImageAndCon(Mat image)
{
    long cols = image.cols;
    long rows = image.rows;
    Mat nullImage(Size(cols, rows), CV_LOAD_IMAGE_GRAYSCALE);
    long con_cols = 0;
    long con_rows = 0;
    long cols_div = cols / width;
    long cols_remain = cols % width;
    for (long i = 0; i <cols_div; i++)
    {
    //取全部行，
    Mat subImage = image(Range::all(), Range(i*width, (i + 1)*width));
    subImage.copyTo(nullImage(Rect(i*width, 0, width, rows)));
    }
    Mat subImage = image(Range::all(), Range(cols_div*width, cols_div*width + cols_remain));
    subImage.copyTo(nullImage(Rect(cols_div*width, 0, cols_remain, rows)));
    
    return nullImage;
}


Mat spliteTest(Mat image)
{
    long cols = image.cols;
    long rows = image.rows;
    Mat nullImage(Size(cols/2, rows), CV_LOAD_IMAGE_GRAYSCALE);  //取一半列数的大小
    Mat subImage = image(Range::all(), Range(0,cols/2));
    subImage.copyTo(nullImage(Rect(0, 0, cols/2, rows)));
    return nullImage;
}


int main()
{
    Mat image = imread("Red.bmp", CV_LOAD_IMAGE_GRAYSCALE);

    //Mat nullImage = spliteImageAndCon(image);
    Mat nullImage = spliteTest(image);

    imshow("nullImage", nullImage);
    imshow("redImage", image);


    waitKey(0);
    return 0;
}
```

