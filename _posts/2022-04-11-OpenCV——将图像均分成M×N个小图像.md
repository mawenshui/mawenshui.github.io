# [Opencv——将图像均分成M×N个小图像](https://www.cnblogs.com/phoenixdsg/p/8418795.html)

```c++
c++std::vector<std::vector<Mat> > partitionImage(Mat&src,int rows,int cols)
```



函数中有三个输入参数，第1个src是原图像；第2个参数rows是网格的行数，第三个参数cols是网格的列数；返回的是二维矢量数组。

 

```c++
//图像的二维分割，即将图像分割成rows行、cols列的小格子—array[rows][cols]，
//保存在二维矢量vector中，
std::vector<std::vector<Mat> > partitionImage(Mat&src,int rows,int cols)
{
    std::vector<std::vector<Mat> > array(rows,std::vector<Mat>(cols));

    int irows=src.rows,icols=src.cols;//原图像的行数、列数
    int dr=irows/rows,dc=icols/cols;//分割后的小块图像的行数、列数
    //分割行、列数与图像的行列数不能整除时，有效图像偏移delt_x、delt_y。    /// 也就是将不能整除的余数像素丢弃，丢弃的方法就是，在图像上、下、左右    /// 各删除余数一半的像素。比如行余数为4，则在图像顶部、底部分别删除2行像素。    
    int delt_y=(irows%rows)/2,delt_x=(icols%cols)/2;
    for(int i=0;i<rows;i++)
    {
        for(int j=0;j<cols;j++)
        {
            int x=j*dc+delt_x,y=i*dr+delt_y;
            array[i][j]=src(cv::Rect(x,y,dc,dr));
        }
    }
    return array;
}
```

测试主程序如下：

```c++
int main()
{
    Mat img=imread("D:/CodeWork/MyImage/lena.jpg",0);
    vector<vector<Mat> > parts=partitionImage(img,3,4);
    imshow("parts 00",parts[0][3]);
    imshow("parts 01",parts[1][3]);
    imshow("parts 02",parts[2][3]);

    imshow("rogin img",img);
    waitKey();
    return 0;
}
```

输出结果如下：

![img](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202204111010986.png)

下面是分割后的图像阵列：

![img](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202204111010942.png) ![img](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202204111010951.png) ![img](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202204111010958.png) ![img](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202204111010924.png)

 