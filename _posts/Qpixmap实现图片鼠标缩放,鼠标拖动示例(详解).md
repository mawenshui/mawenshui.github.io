# QT-Qpixmap实现图片鼠标缩放,鼠标拖动示例(详解)

[原文链接](https://www.cnblogs.com/lifexy/p/9057046.html)

**通过 QPainter 绘画实现,以本地图片985\*740为例**

**如下图所示:**

![img](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202204011624114.png)

**效果如下所示:**

![img](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202204011624249.gif)

 

**实现原理**

主要通过以下函数实现:

```
void QPainter::drawTiledPixmap ( int x, int y, int w int h const QPixmap & pixmap, int sx = 0, int sy = 0 );
　　　　　　//平铺显示pixmap
　　　　　　//x y w h :表示绘画区域
　　　　　　//sx  sy  :表示Qpixmap绘画起始位置
```

 

**只要算出x y w h sx sy就能实现超出窗口不显示的效果**

**举个例子,如下图所示,居中显示1200\*1200时：**

![img](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202204011625327.png)

**当图片左偏移600时,也就是offset=-600时,则只能在窗口上显示一半的图片：**

 

![img](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202204011625900.png)

 

**2020-11-25更新**

由于本笔记是当时才学QT时写的,所以图片都是指定的某资源,而不是通过QFileDialog来打开文件,如下图所示:

 ![img](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202204011625750.png)

本用来记笔记给自己看的,结果很多参考代码的人,直接复制代码,然后**忘记改图片资源,**效果展示不出来,以为是代码问题.

所以更新,界面如下所示,添加了两个button,还原100%显示大小、打开文件按钮:

![img](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202204011625994.png)

 

**代码实现（直接复制粘贴到widget界面类即可）**



**widget.h:**

```c++
#ifndef WIDGET_H
#define WIDGET_H

#include <QWidget>
#include <QtGui>
#include <QLabel>
#include <QPushButton>
#include <QComboBox>
#include <QApplication>
#include <QFileDialog>
#include <QImage>
class Widget : public QWidget
{
    Q_OBJECT

private :
    QPixmap  pix;
    QPixmap  crtPix;
    int action;          //动作(放大,缩小,移动...)
    int pixW;            //图片宽
    int pixH;            //图片高

    QRect Paint;         //绘画区域

    QImage image;        //打开的图片


    float ratio;              //缩放比例
    QPoint offset;           //一次的图片偏移值
    QPoint Alloffset;          //总偏移
    QLabel label;

    QPushButton  BigButton;
    QPushButton  LittleButton;
    QPushButton  LiftButton;
    QPushButton  RightButton;
    QPushButton  UpButton;
    QPushButton  DownButton;
    QPushButton  ResetButton;
    QPushButton  OpenButton;

    void AddComboItem(QComboBox* cmbo);
    bool event(QEvent * event);
    void wheelEvent(QWheelEvent* e);     //鼠标滑轮事件
private slots:
    void    onUpClicked();
    void    onDownClicked();
    void    onResetClicked();
    void    OnLiftClicked();
    void    OnRightClicked();
    void    onLittleClicked();
    void    onBigClicked();

    void    onOpenClicked();

   void paintEvent(QPaintEvent *event);
public:
    explicit Widget();

    enum  Type {
        None          = 0,
        Amplification ,
        Shrink,
        Lift,
        Right,
        Up,
        Down,
        Move,
        Reset
    };

};
#endif // WIDGET_H
```

 

**widget.cpp:**

```c++
#include "widget.h"

Widget::Widget():
    Paint(10,10,810,810),
    BigButton("放大",this),
    LittleButton("缩小",this),
    LiftButton("向左",this),
    RightButton("向右",this),
    UpButton("向上",this),
    DownButton("向下",this),
    ResetButton("还原",this),
    OpenButton("打开文件",this),
    Alloffset(0,0),
    label("100%",this)
{
    ratio= 1.0;//初始化图片缩放比例
    action = Widget::None;


    BigButton.setGeometry(822,10,60,25);
    connect(&BigButton,SIGNAL(clicked()),this,SLOT(onBigClicked()));

    LittleButton.setGeometry(822,40,60,25);
    connect(&LittleButton,SIGNAL(clicked()),this,SLOT(onLittleClicked()));

    LiftButton.setGeometry(822,70,60,25);
    connect(&LiftButton,SIGNAL(clicked()),this,SLOT(OnLiftClicked()));
    RightButton.setGeometry(822,100,60,25);
    connect(&RightButton,SIGNAL(clicked()),this,SLOT(OnRightClicked()));
    UpButton.setGeometry(822,130,60,25);
    connect(&UpButton,SIGNAL(clicked()),this,SLOT(onUpClicked()));
    DownButton.setGeometry(822,160,60,25);
    connect(&DownButton,SIGNAL(clicked()),this,SLOT(onDownClicked()));


    ResetButton.setGeometry(822,190,60,25);
    connect(&ResetButton,SIGNAL(clicked()),this,SLOT(onResetClicked()));

    OpenButton.setGeometry(822,220,60,25);
    connect(&OpenButton,SIGNAL(clicked()),this,SLOT(onOpenClicked()));

    label.move(840,260);
    resize(890,850);

    this->setWindowTitle("图片浏览器(请打开文件)");
}

bool Widget::event(QEvent * event)
{
    static bool press=false;
    static QPoint PreDot;

    if(event->type() == QEvent::MouseButtonPress )
    {
           QMouseEvent *mouse = dynamic_cast<QMouseEvent* >(event);

           //判断鼠标是否是左键按下,且鼠标位置是否在绘画区域
           if(mouse->button()==Qt::LeftButton &&Paint.contains(mouse->pos()))
           {
               press=true;
               QApplication::setOverrideCursor(Qt::OpenHandCursor); //设置鼠标样式

               PreDot = mouse->pos();
           }
    }
    else if(event->type() == QEvent::MouseButtonRelease)
    {
        QMouseEvent *mouse = dynamic_cast<QMouseEvent* >(event);

        //判断鼠标是否是左键释放,且之前是在绘画区域
        if(mouse->button()==Qt::LeftButton && press )
        {
            QApplication::setOverrideCursor(Qt::ArrowCursor); //改回鼠标样式
            press=false;
        }
    }

     if(event->type() == QEvent::MouseMove)              //移动图片
     {
          if(press)
         {
            QMouseEvent *mouse = dynamic_cast<QMouseEvent* >(event);

            offset.setX(mouse->x() - PreDot.x());
            offset.setY(mouse->y() - PreDot.y());
            PreDot = mouse->pos();
            action = Widget::Move;

            this->update();
         }
     }
    return QWidget::event(event);
}

void Widget::wheelEvent(QWheelEvent* event)     //鼠标滑轮事件
{
 if (event->delta()>0) //上滑,缩小
 {      
    action=Widget::Shrink;
    this->update();
 } 
    else //下滑,放大
 {                    
     action=Widget::Amplification;
     this->update();
 }

 event->accept();
}

void Widget::paintEvent(QPaintEvent *event)
{
    QPainter painter(this);
    painter.setRenderHints(QPainter::SmoothPixmapTransform|QPainter::Antialiasing|QPainter::TextAntialiasing);
    painter.drawRect(Paint.x()-1,Paint.y()-1,Paint.width()+1,Paint.height()+1); //画框

    if(image.isNull())
    {
     return;
    }

    int NowW = ratio *pixW;
    int NowH = ratio *pixH;

    if(action==Widget::Shrink)           //缩小
    {
          ratio-=0.05*ratio;
        if(ratio<0.018)
          ratio = 0.01;

        /*显示比例*/
        QString str;
        str.sprintf("%.0f%",ratio*100);
        label.setText(str) ;
        qDebug()<<"缩小:"<<ratio;
    }
    else  if(action==Widget::Amplification)           //放大
    {

         ratio+=0.05*ratio;
       if(ratio>4.5)
         ratio = 5.000;

        /*显示比例*/
        QString str;
        str.sprintf("%.0f%",ratio*100);
        label.setText(str);
        qDebug()<<"放大:"<<ratio;
    }


    if(action==Widget::Amplification || action==Widget::Shrink || action==Widget::Reset)      //更新图片
    {
      NowW = ratio *pixW;
      NowH = ratio *pixH;


      crtPix= pix.scaled(NowW, NowH,Qt::KeepAspectRatio,Qt::SmoothTransformation); //重新装载

      action=Widget::None;
    }

    if(action==Widget::Move)                    //移动
    {
        int offsetx=Alloffset.x()+offset.x();
        Alloffset.setX(offsetx);

        int offsety=Alloffset.y()+offset.y();
        Alloffset.setY(offsety);
        action=Widget::None;
    }

    if(abs(Alloffset.x())>=(Paint.width()/2 + NowW/2 -10))    //限制X偏移值
    {
        if(Alloffset.x()>0)
            Alloffset.setX(Paint.width()/2 + NowW/2 -10);
        else
         Alloffset.setX(-Paint.width()/2 + -NowW/2 +10);

    }
    if(abs(Alloffset.y())>=(Paint.height()/2 + NowH/2 -10))    //限制Y偏移值
    {
        if(Alloffset.y()>0)
            Alloffset.setY(Paint.height()/2 + NowH/2 -10);
        else
         Alloffset.setY(-Paint.height()/2 + -NowH/2 +10);

    }

    int x = Paint.width()/2 + Alloffset.x() -NowW/2;
    if(x<0)
        x=0;

    int y = Paint.height()/2 + Alloffset.y() -NowH/2;
    if(y<0)
        y=0;

    int  sx = NowW/2 - Paint.width()/2 - Alloffset.x();
    if(sx<0)
        sx=0;

    int  sy = NowH/2 - Paint.height()/2 - Alloffset.y();
    if(sy<0)
        sy=0;

    int w =(NowW - sx)>Paint.width()? Paint.width() : (NowW - sx);
    if(w>(Paint.width()-x))
        w = Paint.width()-x;

    int h =(NowH - sy)>Paint.height()? Paint.height() : (NowH - sy);
    if(h>(Paint.height()-y))
        h = Paint.height()-y;


    painter.drawTiledPixmap(x+Paint.x(),y+Paint.y(),w,h,crtPix,sx,sy);             //绘画图形
}

void  Widget::onLittleClicked()
{
  action=Widget::Amplification;
  this->update();
}

void Widget::onOpenClicked()
{
	QString str = QFileDialog::getOpenFileName(this,
                                 "open",
                                 "D:",
                                 "img (*.png *.jpg)");

    if(!str.isNull())
    {
       image.load(str);
       pix = pix.fromImage(image);

       crtPix = pix;
       pixW = image.width();            //图片宽
       pixH = image.height();           //图片高
       qDebug()<<str<<pixW<<pixH;
       this->setWindowTitle("图片浏览器("+str+")");
       onResetClicked();
    }
}

void  Widget::onBigClicked()
{
  action=Widget::Shrink;
  this->update();
}
void Widget::onUpClicked()
{
  action=Widget::Move;
  offset.setX(0);
  offset.setY(-20);

  this->update();
}
void Widget::onDownClicked()
{
  action=Widget::Move;
  offset.setX(0);
  offset.setY(20);
  this->update();
}
void Widget::onResetClicked()
{
  action=Widget::Reset;
  Alloffset.setX(0);
  Alloffset.setY(0);
  ratio = 1.000;
  label.setText("100%");
  this->update();
}
void Widget::OnLiftClicked()
{
  action=Widget::Move;
  offset.setX(-20);
  offset.setY(0);

  this->update();
}
void Widget::OnRightClicked()
{
  action=Widget::Move;
  offset.setX(20) ;
  offset.setY(0) ;

  this->update();
}
```