# Qt中cvMat与QImage的格式转换

[TOC]



## 1.cv::Mat转QIMage

MatToQImage.h

```c++
#ifndef CMATTOQIMAGE_H
#define CMATTOQIMAGE_H

#include <QImage>
#include <QDebug>
#include <opencv2/opencv.hpp>
#include "opencv2/imgproc/types_c.h"

class CMatToQImage
{
public:
    CMatToQImage();
    QImage MatToQImage(const cv::Mat &l_Mat);
};

#endif // CMATTOQIMAGE_H
```

MatToQImage.cpp

```c++
#include "MatToQImage.h"

using namespace cv;
using namespace std;

CMatToQImage::CMatToQImage()
{

}

/**********************************************************************
Function:    // MatToQImage
Description: // Mat转QImage
Input:       // Mat &l_Mat
Output:      // QImage image
Return:      // QImage image
Author:      // 马文水
Time:        // 2022-03-29
**********************************************************************/
QImage CMatToQImage::MatToQImage(const Mat &l_Mat)
{
    // 8-bits unsigned, NO. OF CHANNELS = 1
    if(l_Mat.type() == CV_8UC1)
    {
        QImage image(l_Mat.cols, l_Mat.rows, QImage::Format_Indexed8);
        // Set the color table (used to translate colour indexes to qRgb values)
        image.setColorCount(256);
        for(int i = 0; i < 256; i++)
        {
            image.setColor(i, qRgb(i, i, i));
        }
        // Copy input Mat
        uchar *pSrc = l_Mat.data;
        for(int row = 0; row < l_Mat.rows; row ++)
        {
            uchar *pDest = image.scanLine(row);
            memcpy(pDest, pSrc, l_Mat.cols);
            pSrc += l_Mat.step;
        }
        return image;
    }
    // 8-bits unsigned, NO. OF CHANNELS = 3
    else if(l_Mat.type() == CV_8UC3)
    {
        // Copy input Mat
        const uchar *pSrc = (const uchar*)l_Mat.data;
        // Create QImage with same dimensions as input Mat
        QImage image(pSrc, l_Mat.cols, l_Mat.rows, l_Mat.step, QImage::Format_RGB888);
        return image.rgbSwapped();
    }
    else if(l_Mat.type() == CV_8UC4)
    {
        qDebug() << "CV_8UC4";
        // Copy input Mat
        const uchar *pSrc = (const uchar*)l_Mat.data;
        // Create QImage with same dimensions as input Mat
        QImage image(pSrc, l_Mat.cols, l_Mat.rows, l_Mat.step, QImage::Format_ARGB32);
        return image.copy();
    }
    else
    {
        qDebug() << "ERROR: Mat could not be converted to QImage.";
        return QImage();
    }
}
```

## 2.QImage转cv::Mat

QImageToMat.h

```c++
#ifndef CQIMAGETOMAT_H
#define CQIMAGETOMAT_H

#include <QImage>
#include <QDebug>
#include <opencv2/opencv.hpp>
#include "opencv2/imgproc/types_c.h"

class CQImageToMat
{
public:
    CQImageToMat();

    cv::Mat QImageToMat(QImage &l_Image);
};

#endif // CQIMAGETOMAT_H
```

QImageToMat.cpp

```c++
#include "QImageToMat.h"

using namespace cv;
using namespace std;

CQImageToMat::CQImageToMat()
{

}

/**********************************************************************
Function:    // QImageToMat
Description: // QImage转cv::Mat
Input:       // QImage &l_Image
Output:      // cv::Mat l_Mat
Return:      // cv::Mat l_Mat
Author:      // 马文水
Time:        // 2022-03-24
**********************************************************************/
Mat CQImageToMat::QImageToMat(QImage &l_Image)
{
    Mat l_Mat;

    switch(l_Image.format())
    {
    case QImage::Format_ARGB32:
    {

    }
    case QImage::Format_RGB32:
    {

    }
    case QImage::Format_ARGB32_Premultiplied:
    {
        l_Mat = Mat(l_Image.height(), l_Image.width(), CV_8UC4, (void*)l_Image.constBits(), l_Image.bytesPerLine());
        break;
    }
    case QImage::Format_RGB888:
    {
        l_Mat = Mat(l_Image.height(), l_Image.width(), CV_8UC3, (void*)l_Image.constBits(), l_Image.bytesPerLine());
        cvtColor(l_Mat, l_Mat, CV_BGR2RGB);
        break;
    }
    case QImage::Format_Indexed8:
    {
        l_Mat = Mat(l_Image.height(), l_Image.width(), CV_8UC1, (void*)l_Image.constBits(), l_Image.bytesPerLine());
        break;
    }
    default:
    {
        qDebug() << "ERROR: QImage could not be converted to Mat.";
    }
    }
    return l_Mat;
}
```

