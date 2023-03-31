# Qt 2D绘图(5)：绘制图像基础

[原文链接](https://blog.csdn.net/hyongilfmmm/article/details/83233589)

本文出自本人原创著作《Qt5.10 GUI完全参考手册》网盘地址：
https://pan.baidu.com/s/1iqagt4SEC8PUYx6t3ku39Q

《C++语法详解》网盘地址：https://pan.baidu.com/s/1dIxLMN5b91zpJN2sZv1MNg

若对C++语法不熟悉，建议参阅本人所著《C++语法详解》一书，电子工业出版社出版，该书语法示例短小精悍，对查阅C++知识点相当方便，并对语法原理进行了透彻、深入详细的讲解，可确保读者彻底弄懂C++的原理，彻底解惑C++，使其知其然更知其所以然。此书是一本全面了解C++不可多得的案头必备图书。

12.13.1 图像基础知识

1、分辨率(DPI和PPI)

DPI和PPI：DPI全称是Dots Per Inch(点每英寸)，PPI全称是Pixel Per Inch([像素](https://so.csdn.net/so/search?q=像素&spm=1001.2101.3001.7020)每英寸)，即指的是每一英寸的长度上有多少个点(或像素)。DPI和PPI越高，显示就越清楚细腻，DPI通常用于描述打印机或者扫描仪等的分辨率，而PPI主要用于描述显示器的分辨率，平常使用中经常混用PPI和DPI的概念，但他们还是有一些区别，因为像素和点代表的长度有可能并不是一样长的，对于显示器通常使用PPI，比如1024PPI，是指每英寸有1024个像素，当然，也可对显示器使用DPI，比如1024DPI这时表示一英寸有1024个点，这里一个像素和一个点具体实际代表多少长度(比如多少毫米)是需要通过计算的，具体见下文。

像素(pixel)不是长度单位，二者不能等同，像素表示的是一个图像的最小单位，比如室外大屏幕一个像素可能是1到2厘米长度，而电脑显示屏一像素大约是0.2~0.4毫米长度。比如1024X768分辨率(表示长度方向有1024个像素，宽度方向有768个像素)，显示屏为17英寸的显示器(不含边框)，其PPI为[(1024 * 1024+768*768)1/2]/17=75.2PPI，其一个像素的长度大约为25.4/75.2=0.3377毫米。注：1英寸=25.4毫米，显示器的尺寸指的是显示器对角线的长度(注意，通常是包括显示器的不可显示边框的)，所以本例对分辨率使用勾股定理求出斜边像素数，再除以显示器尺寸就是显示器的PPI。

点(磅)：在计算机中，一点的大小大约是1/72英寸，1磅就是指的1点的大小。

2、以下概念来自移动设备编程

设备像素(物理像素)：就是显示设备的真实像素，这是屏幕的固有属性，不会改变。
设备独立像素DIP(Device Independent Pixels)：又称设备无关像素或密度独立性，在移动设备(比如手机)中，图形被显示在不同密度(分辨率)的屏幕上，这时因不同移动设备的密度并不一致，会导致在低密度的屏幕上使图形看起来更大，而在高度密的屏幕上看起来更小，为解决这个问题，引入了设备独立像素，或使用设备独立像素为单位绘制图形，则可以保证图形在不同密度的屏幕上其大小看起来差不多一致。DIP是由系统设置的，通常系统设置有多个DIP值，比如有120 ppi 、160ppi、240ppi等，通常使用160ppi。

设备像素比(dpr)：就是设备像素与设备独立像素的比值，dpr通常也由系统设置。
理解DIP：DIP的本质就是单位换算的问题，下面以示例说明

示例：若系统设置的DIP为M 像素/英寸(ppi)，设备像素为N 像素/英寸，假设我们绘制X像素长度的图形，若使用DIP绘制图形，求显示屏实际显示的像素Y为多少。
解：
![在这里插入图片描述](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202204011702940.png)

3、颜色基础知识

alpha通道用于记录图像的透明度信息，通常使用8位(共有256种级别)表示alpha通道，0表示透明，255表示不透明。

RGB颜色模型：自然界中的所有颜色都可由红®、绿(G)、蓝(B)三种颜色组成。计算机中，通常把红®、绿(G)、蓝(B)三种颜色中的每一种使用8位进行存储，RGB三种颜色就需要使用24位(3字节)进行存储，这样每种颜色就有0~255共256种状态，不同的红绿蓝就可以组合出1600多万(2的24次方)种颜色，若在24位的基础上再增加一个表示图像透明度的alpha通道，假设alpha使用8位来存储，那么ARGB共32位就可以组合出更多的颜色。除了RGB颜色模型外还有CMY(青色、洋红、黄色)、CMYK、HSI(色调、饱和度、亮度)、HSV等颜色模型。

颜色深度(简称深度)就是指的支持多少种颜色，比如8位深度的图像，则表示该图像支持256种(2的8次方)不同的颜色，4位深度的图像，表示该图像支持16种颜色。

位平面：在计算机中设有专门用于存储图像信息的帧缓存存储器，该存储器中的每一位对应于屏幕上的一个点，当一个位上的点被设置为1时，屏幕上对应位置就出现一个亮点，当一个位的值为零时，则出现一个暗点。当显示器的分辨率为640 * 480 时，为显示一个单色图像(深度为1)需要640 * 480位的帧缓存存储器容量，这个容量被称为一个位平面，同理，若需要显示一个8位深度的图像，则需要有640 * 480 * 8位帧缓存的容量，即需要8个位平面，从此处可见位平面通常与颜色深度的值相同。对于更高的分辨率和显示更高深度的图像，则需要更多的帧缓存容量。

通道是用于描述每个图像颜色的信息的，比如对于RGB模式的图像，共有3个通道，即红色通道、绿色通道、蓝色通道，若该图像还包含alpha通道信息，则该图像就有4个通道。对于CMYK模式的图像，有4个通道，分别为青色、洋红、黄色、黑色通道。

灰度是指黑白图像中的颜色深度，其范围通常使用0~255来表示，其中白色为255，黑色为0。对于RGB模式的彩色图像则RGB三种颜色的值相等时就是灰度色彩模式。

4、图像抖动(dithering)技术

该技术主要用于把高深度的图像转换为低深度图像。比如，老式的针式打印机只有黑点和白点，那么怎样打印具有灰度级别的黑白图片呢？抖动技术就是用于解决这个问题的，抖动的基本原理是当人们以足够远的距离观察一个很小的区域时，眼睛会对区域内的细节进行平均，从而只能看到区域的总体宽度。在一个区域内黑点越多看起来就越黑，黑点越少看起来就越亮，因此通过适当的控制区域内的黑点数量及分布就能使图片呈现出不同的灰度级别，当然，这有一个专门的抖动算法(本文不讨论)。使用抖动可把灰度图像或彩色图像处理成二值图像。在彩色图像与彩色图像之间的转换(比如把24位的图像转换为8位等)也可以使用抖动技术。

5、图像的格式
像素格式是指像素数据存储时所使用的格式，定义了像素在内存中的编码方式。注意：在实际存储图像时，对于RGB模式或其他颜色模式并不一定会为每种颜色都分配8位的存储空间，比如对于32位含有alpha通道的RGB模式，有可能会为红、绿、蓝各分配10位，而为alpha通道分配2位，当然也有为alpha和红、绿、蓝各分配8位的存储方式。

6、查色表、颜色表、调色板、索引像素格式(简称索引格式)
对于一些包含很少几种颜色的图像，可以为每种所出现的颜色构造一张表(称为查色表或颜色表，也被称为调色板)，在存储图像时只需为每种颜色存储查色表的索引即可，这样可以压缩图像的大小，使用这种方法存储图像的文件称为索引像索格式文件，常见的文件有GIF和PNG。但是颜色种类太多的图像，则不适合使用这种方法存储，比如对于具有24位深度的图像使用颜色表的方法存储，不但节约不了空间，反而还会使空间增大。

12.13.2 绘制图像基础

Qt提供了4个类来处理图像：QImage、QPixmap、QBitmap、QPicture，为了对这几个类加以区分，分别称QImage为图像、QPixmap为像素图、QBitmap为位图、QPicture为图片，这些类的区别及作用如下

①、QImage类提供了一个与硬件无关的图像表示方法，可以直接访问和操控像素，也就是说该类可修改或编辑图像的像素。该类还可以用于进行I/O处理，并对I/O处理操作进行了优化

②、QPixmap类主要用于在屏幕上显示图像，QPixmap中的像素数据是由底层窗口系统进行管理的，该类不能直接访问和操控像素，只能通过QPainter的相应函数或把QPixmap转换为QImage来访问和操控像素。QPixmap可通过标签(QLabel类)或QAbstractButton的子类(icon属性)显示在屏幕上。

③、QBitmap是QPixmap的子类，用于处理颜色深度为1的图像，即只能显示黑白两种颜色。

④、QPicture用来记录并重演QPainter命令，QPicture与分辨率无关，可在不同设备上显示。该类使用一个与平台无关的格式(.pic格式)把绘图命令序列化到I/O设备，所有可绘制在QWidget部件或QPixmap上的内容都可以保存在QPicture中，该类的主要作用是把一个绘制设备上使用QPainter绘制的所有图形保存在QPicture之中，然后再把这些图形重新绘制在其他绘图设备上。

通常，可以使用QImage类来加载并操作图像数据，然后把QImage对象转换为QPixmap再显示到屏幕上，若不需对图像进行操作，也可直接使用QPixmap来加载并显示图像。
QImage、QPixmap、QBitmap、QPicture都是QPaintDevice类的子类(直接或间接)，因此他们都是绘制设备，可以直接在其上进行图形绘制。

```c++
示例12.26：图像绘制(见图12-65)
#ifndef M_H
#define M_H
#include<QtWidgets>
class B:public QWidget{    Q_OBJECT
public:     QPushButton *pb1;    QPicture pc;    QPixmap pm1;    QImage pi;
B(QWidget *p1=0):QWidget(p1){
    		pb1=new QPushButton("AAA",this);  pb1->move(22,111);
    		pb1->setShortcut(QKeySequence("Ctrl+F"));
    		QObject::connect(pb1,&QPushButton::clicked,this,&B::f1);}
void paintEvent(QPaintEvent *e){
    		QPainter pr;
    //把图像绘制到QWidget部件上。函数f1()只是创建了图像中的内容，但图像需要被显示出来才会可见，
//比如设置为标签或按钮的图标，或者像本示例这样直接绘制出来
   		pr.begin(this);
    		pr.drawPixmap(11,11,pm1);  pr.drawImage(133,11,pi);   pr.drawPicture(244,11,pc);}
public slots:
void f1(){	QPainter pr;    QBrush bs(QColor(1,111,1));	//绿色
/* QPixmap、QImage、QPicture都是绘制设备，可在其上直接绘制图形，因为这些绘制设备都不是QWidget部件，因此可以不在paintEvent()函数中绘制。*/
    		pm1.load("F:/1s.png");			//加载一幅图片
    		pr.begin(&pm1);				//开始在pm1上绘制图形
    		//pm1.load("F:/1s.png");  		//注意：load()最好位于begin()之前。
    		pr.drawLine(0,0,111,111);		//在图片1s.png上绘制一条直线
    		pr.end();						//绘制结束

    		pi=QImage(QSize(111,111),QImage::Format_RGB32);	//创建一个QImage对象，并在其上绘制图形
    		pr.begin(&pi);    pr.setBrush(bs);    pr.drawRect(11,11,122,122);    pr.end();

    		pr.begin(&pc);			//开始在QPicture对象上绘制图形
    		pr.setBrush(bs); 			//调用begin()会重置QPainter为默认状态，因此需重新设置画刷。
    		pr.drawRect(11,11,99,99);    pr.drawArc(11,11,111,111,33*16,144*16);    pr.end();
    		repaint();  }}; 			//立即重绘界面
#endif // M_H

//m.cpp文件内容
#include "m.h"
int main(int argc, char *argv[]){    QApplication app(argc,argv);
   B w;    w.resize(444,333);    w.show();    return app.exec();  }
```

![在这里插入图片描述](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202204011702916.png)

12.13.3 使用QPainter类中绘制图像的函数

需要使用到的QPainter类中的函数原型如下：
![在这里插入图片描述](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202204011702098.png)
![在这里插入图片描述](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202204011702950.png)
![在这里插入图片描述](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202204011702978.png)
![在这里插入图片描述](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202204011702957.png)
![在这里插入图片描述](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202204011702695.png)

```c++
示例12.27：读取文件的一部分到目标矩形与平铺图像(结果见图12-66)
void paintEvent(QPaintEvent *e){
QPainter pr(this);    QPixmap pm1("F:/1m.png");
//把原图像1m.png的(55,55,33,33)部分图形绘制到目标矩形。
    pr.drawPixmap(QRect(444,11,111,111),pm1,QRect(55,55,33,33));
    pr.drawPixmap(QRect(577,11,111,111),pm1,QRect(55,55,33,33));
    pr.drawPixmap(QRect(700,11,111,111),pm1,QRect(55,55,33,33));
    pr.drawTiledPixmap(QRect(11,11,400,300),pm1,QPointF(111,111)); } //图像平铺到矩形
```

![在这里插入图片描述](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202204011702823.png)

```c++
示例12.28：使用drawPixmapFragments()函数以不同形式显示原始图像的不同部分
void paintEvent(QPaintEvent *e){
    QPainter pr(this);
    QPixmap pm1("F:/1m.png");
QRectF r(11,11,55,55);
//①、图像片段旋转60度
    QPainter::PixmapFragment fg1=QPainter::PixmapFragment::create(QPointF(111,111),r);
fg1.rotation=60;    //使fg1旋转60度
//②、图像片段放大2倍(X方向)
QPainter::PixmapFragment fg2=QPainter::PixmapFragment::create(QPointF(222,111),r,2);
//③、图像片段放大2倍(Y方向)并旋转60度
QPainter::PixmapFragment fg3=QPainter::PixmapFragment::create(QPointF(333,111),r,1,2,60);
//④、图像片段放大2倍(Y方向)
    QPainter::PixmapFragment fg4=QPainter::PixmapFragment::create(QPointF(411,111),r,1,2);
    QPainter::PixmapFragment pf[4]={fg1,fg2,fg3,fg4};
//以不同形式显示原始图像1m.png的各个部分(本例只裁剪了范围(11,11,55,55)的部分)
pr.drawPixmapFragments(pf,4,pm1,QPainter::OpaqueHint);   //绘制4个图像片段
//绘制的以下直线用于验证目标矩形的中心位置。
    pr.drawLine(0,111,444,111);   pr.drawLine(111,0,111,444);   pr.drawLine(222,0,222,444);}
```

运行结果及说明见图12-67
![在这里插入图片描述](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202204011702721.png)