# **MyQtFristDay——**

## 1.如何运行程序

快捷键 Ctrl+R

## 2.代码部分含义

### main.cpp

```c++
//包含窗口头文件
#include "widget.h"   

// Qt应用程序头文件
#include <QApplication>   

//之前的main函数   int main(){};
//Qt必须使用两个参数的main函数，需要通过这两个参数去创建Qt应用程序
int main(int argc, char *argv[])
{
    //应用程序实例对象，有且只能有一个，按顺序写入main函数中的参数
    QApplication a(argc, argv);
    
    
    Widget w;     //创建一个窗口对象
    w.show();     //手动显示窗口
    
    a.exec();     //让Qt程序进入消息循环，开始处理事件
    return 0;     //等价于return a.exec();
    
}
```

### widget.h

```c++
#ifndef WIDGET_H
#define WIDGET_H
/*
Qt的系统头文件是不以.h结尾
一般头文件就是类名，所以记忆方便

*/
#include <QWidget>

class Widget : public QWidget
{
    Q_OBJECT

public:
    Widget(QWidget *parent = nullptr);
    ~Widget();
};
#endif // WIDGET_H
```

### widget.cpp

```c++
#include "widget.h"
#include <QIcon>          //图标头文件
#include <QPushButton>    //按钮头文件

Widget::Widget(QWidget *parent)
    : QWidget(parent)
{
    //设置窗口标题
    this->setWindowTitle("Mawenshui");
    this->setWindowIcon(QIcon("C:/Users/mws/Desktop/证件照/001.jpg"));

    //设置窗口大小
    //1.
  //this->setMinimumSize(640,480);//窗口最小值，可拖动放大，不可缩小至小于最小值
    //2.
    this->resize(640,480);//窗口初始大小，可拖动任意放大缩小
    //3.
    this->setFixedSize(640,480);//设置窗口固定大小，用户不可改变窗口大小



/*
  存在于栈区创建的对象，构造函数结束时对象即消失
    //创建按钮
    QPushButton btn("我是按钮");
    btn.show();
  可以将按钮创建于堆区
*/
    //创建按钮
    QPushButton *btn = new QPushButton("我是按钮",this);

    //QPushButton *btn = new QPushButton("我是按钮",this);   设置父对象参数为this

//    btn->setParent(this);     //给按钮设置父对象，让按钮显示到父窗口上，若不设置默认显示到桌面上（单独窗口）
//    btn->show();              //显示按钮



    //如果定义了父对象，就不用手动调用show函数显示了
    QPushButton *btnCai = new QPushButton("我是菜鸟",this);
    btnCai->move(100,0);    //改变坐标位置到（100,0）

    //信号与槽机制
    /*
        connect(const QObject *sender,const char *signal,const QObject *receiver,const char *method)
        sender  消息的发送者
        signal  消息的类型（信号）
        receiver  消息的接受者
        method  如何处理消息（槽函数）
    */
    connect(btn,&QPushButton::clicked,this,[=]()    // [](){}  为lambda表达式形式
    {
        static bool isflag = true;
        if(isflag){
            btnCai->hide();
        }else{
            btnCai->show();
            btnCai->setText("菜鸟菜鸟");
        }
        isflag = !isflag;
    });


    connect(btnCai,&QPushButton::clicked,this,[=]()
    {
        static bool isflag = true;
        if(isflag){
            btn->hide();
        }else{
            btn->show();
            btn->setText("我就是菜鸟");
        }
        isflag = !isflag;
    });

}

Widget::~Widget()
{
}
```

### MyQtFrist.pro

```c++
#这个文件是用来配置Qt项目的
QT       += core gui   #加载Qt核心模块

#当Qt版本大于4的时候，需要加载widgets模块
greaterThan(QT_MAJOR_VERSION, 4): QT += widgets

#配置项目能够支持C++11标准  lambda表达式
CONFIG += c++11

# The following define makes your compiler emit warnings if you use
# any Qt feature that has been marked deprecated (the exact warnings
# depend on your compiler). Please consult the documentation of the
# deprecated API in order to know how to port your code away from it.
DEFINES += QT_DEPRECATED_WARNINGS

# You can also make your code fail to compile if it uses deprecated APIs.
# In order to do so, uncomment the following line.
# You can also select to disable deprecated APIs only up to a certain version of Qt.
#DEFINES += QT_DISABLE_DEPRECATED_BEFORE=0x060000    # disables all the APIs deprecated before Qt 6.0.0

#源文件
SOURCES += \
    main.cpp \
    widget.cpp

#头文件
HEADERS += \
    widget.h

# Default rules for deployment.
qnx: target.path = /tmp/$${TARGET}/bin
else: unix:!android: target.path = /opt/$${TARGET}/bin
!isEmpty(target.path): INSTALLS += target

```

