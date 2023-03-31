# Qt 获取系统时间并动态显示在状态栏statusBar

### **头文件mainwindow.h：**

```c++
#include<QDateTime>
#include<QTimer>
 
private slots:
    void TimeUpdate();
```

### **源文件mainwindow.cpp：**

```c++
MainWindow::MainWindow(QWidget *parent) :
    QMainWindow(parent),
    ui(new Ui::MainWindow)
{
    ui->setupUi(this);

    QTimer *timer=new QTimer(this);
    timer->start(1000); // 每次触发timeout信号时间间隔为1秒
 
    connect(timer,SIGNAL(timeout()),this,SLOT(TimeUpdate()));
    ShowTimeLabel = new QLabel(this);
}

void MainWindow::TimeUpdate()
{
   QDateTime CurrentTime=QDateTime::currentDateTime();
   QString strTime=CurrentTime.toString(" yyyy年MM月dd日 hh:mm:ss "); //设置显示的格式
   ShowTimeLabel->setText(strTime);
   statusBar()->addPermanentWidget(ShowTimeLabel);
   statusBar()->setSizeGripEnabled(true); //设置是否显示右边的大小控制点
}
```

### 左下角的信息输出用showMessage实现：

```c++
QString strPosXY = QString("PosXY--(%1, %2)").arg(QString::number(x_in_Label), QString::number(y_in_Label));
//ui->statusBar->setStyleSheet("color:blue");
ui->statusBar->showMessage(strPosXY, 0);
```

![img](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202203311424756.png)