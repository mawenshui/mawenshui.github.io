# QCustomPlotDemo绘图控件代码

## 1.Demo01

![image-20210710092657222](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202203311420656.png)

```cpp
// 生成一些数据:
QVector<double> x(101), y(101); // initialize with entries 0..100
for (int i=0; i<101; ++i)
{
  x[i] = i/50.0 - 1; // x goes from -1 to 1
  y[i] = x[i]*x[i]; // let's plot a quadratic function
}
            // 创建图形并为其分配数据:
customPlot->addGraph();
customPlot->graph(0)->setData(x, y);
// 给坐标轴一些标签:
customPlot->xAxis->setLabel("x");
customPlot->yAxis->setLabel("y");
    //线结尾装饰
customPlot->xAxis->setUpperEnding(QCPLineEnding::esSpikeArrow);//x轴终点箭头图案
customPlot->yAxis->setUpperEnding(QCPLineEnding::esSpikeArrow);//y轴终点箭头图案
// 设置坐标轴范围，这样我们就可以看到所有的数据:
customPlot->xAxis->setRange(-1, 1);
customPlot->yAxis->setRange(0, 1);
customPlot->replot();
```

## 2.Demo02

![image-20210710093253525](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202203311422169.png)

```cpp
// 添加两个新图形并设置它们的外观:
customPlot->addGraph();
customPlot->graph(0)->setPen(QPen(Qt::blue)); // 蓝色线表示第一个图形
customPlot->graph(0)->setBrush(QBrush(QColor(0, 0, 255, 20))); // 第一个图形将被半透明的蓝色填充
customPlot->addGraph();
customPlot->graph(1)->setPen(QPen(Qt::red)); // 红色线表示第二个图形
// 生成一些数据点(第一个图为y0，第二个图为y1):
QVector<double> x(251), y0(251), y1(251);
for (int i=0; i<251; ++i)
{
  x[i] = i;
  y0[i] = qExp(-i/150.0)*qCos(i/10.0); // exponentially decaying cosine指数衰减的余弦
  y1[i] = qExp(-i/150.0);              // exponential envelope
}
// 配置右轴和顶轴以显示刻度，但不显示标签:
// (参见 QCPAxisRect::setupFullAxesBox 来获得一个更快的方法)
customPlot->xAxis2->setVisible(true);
customPlot->xAxis2->setTickLabels(false);
customPlot->yAxis2->setVisible(true);
customPlot->yAxis2->setTickLabels(false);
// 使左轴和下轴总是将其范围转移到右轴和上轴:
connect(customPlot->xAxis, SIGNAL(rangeChanged(QCPRange)), customPlot->xAxis2, SLOT(setRange(QCPRange)));
connect(customPlot->yAxis, SIGNAL(rangeChanged(QCPRange)), customPlot->yAxis2, SLOT(setRange(QCPRange)));
// 将数据点传递给图形:
customPlot->graph(0)->setData(x, y0);
customPlot->graph(1)->setData(x, y1);
// 让范围自行缩放，使图0完全适合可见区域:
customPlot->graph(0)->rescaleAxes();
// 图1也是一样，但只是扩大范围(在这种情况下，图1小于图0):
customPlot->graph(1)->rescaleAxes(true);
// 注意:我们也可以调用customPlot->rescaleAxes();instead
// 允许用户用鼠标拖动轴范围，用鼠标轮缩放并单击：
customPlot->setInteractions(QCP::iRangeDrag | QCP::iRangeZoom | QCP::iSelectPlottables);
```



`Qt::NoBrush`填充将从图(这里是图0)到与键(这里为x)轴平行的零值线。

如果我们想要在这个图和另一个图之间填充一个通道，我们会另外调用`graph>setChannelFillGraph(otherGraph) `。

若要删除通道填充，只需通过`0`和其他图形一样，填充会像以前一样一直到达零值线.若要完全删除填充，请调用 `graph->setBrush(Qt::NoBrush)` .



## 3.Demo03

您可以自由地定义一个图的哪个轴应该扮演哪个角色。例如，索引为0的图使用左轴(`yAxis`)作为它的键和底部轴(`xAxis`)作为它的价值。因此，这张图是靠左轴向上站着的：

![image-20210710103639882](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202203311422168.png)

```cpp
// 句点为小数分隔符，逗号为千分隔符。
customPlot->setLocale(QLocale(QLocale::English, QLocale::UnitedKingdom)); 
customPlot->legend->setVisible(true);
QFont legendFont = font();  // 开始与主窗口的字体..
legendFont.setPointSize(9); // 把图例做小一点
customPlot->legend->setFont(legendFont);
customPlot->legend->setBrush(QBrush(QColor(255,255,255,230)));
// 默认情况下，图例是在主轴矩形的插入布局中，下面是我们如何访问它来改变图例的位置:
customPlot->axisRect()->insetLayout()->setInsetAlignment(0, Qt::AlignBottom|Qt::AlignRight);
 
// 图0的设置:键轴向左，值轴底部
// 会包含左类麦克斯韦函数
customPlot->addGraph(customPlot->yAxis, customPlot->xAxis);
customPlot->graph(0)->setPen(QPen(QColor(255, 100, 0)));
customPlot->graph(0)->setBrush(QBrush(QPixmap("./balboa.jpg"))); // 填充指定图像的纹理
customPlot->graph(0)->setLineStyle(QCPGraph::lsLine);
customPlot->graph(0)->setScatterStyle(QCPScatterStyle(QCPScatterStyle::ssDisc, 5));
customPlot->graph(0)->setName("Left maxwell function");
 
// 图1的设置:键轴底部，值轴左侧(这些是默认轴)
// 包含带有误差杆的底部麦克斯韦函数
customPlot->addGraph();
customPlot->graph(1)->setPen(QPen(Qt::red));
customPlot->graph(1)->setBrush(QBrush(QPixmap("./balboa.jpg"))); // 和图0的填充一样
customPlot->graph(1)->setLineStyle(QCPGraph::lsStepCenter);
customPlot->graph(1)->setScatterStyle(QCPScatterStyle(QCPScatterStyle::ssCircle, Qt::red, Qt::white, 7));
customPlot->graph(1)->setName("Bottom maxwell function");
QCPErrorBars *errorBars = new QCPErrorBars(customPlot->xAxis, customPlot->yAxis);
errorBars->removeFromLegend();
errorBars->setDataPlottable(customPlot->graph(1));
 
// 图2的设置:键轴上，值轴右
// 将包含高频正弦与低频拍差:
customPlot->addGraph(customPlot->xAxis2, customPlot->yAxis2);
customPlot->graph(2)->setPen(QPen(Qt::blue));
customPlot->graph(2)->setName("High frequency sine");
 
// 图3的设置:与图2相同的轴
// 图2将包含低频拍包络线
customPlot->addGraph(customPlot->xAxis2, customPlot->yAxis2);
QPen blueDotPen;
blueDotPen.setColor(QColor(30, 40, 255, 150));
blueDotPen.setStyle(Qt::DotLine);
blueDotPen.setWidthF(4);
customPlot->graph(3)->setPen(blueDotPen);
customPlot->graph(3)->setName("Sine envelope");
 
// 图4的设置:键轴右，值轴上
// 将包含具有随机扰动的抛物线分布的数据点
customPlot->addGraph(customPlot->yAxis2, customPlot->xAxis2);
customPlot->graph(4)->setPen(QColor(50, 50, 50, 255));
customPlot->graph(4)->setLineStyle(QCPGraph::lsNone);
customPlot->graph(4)->setScatterStyle(QCPScatterStyle(QCPScatterStyle::ssCircle, 4));
customPlot->graph(4)->setName("Some random data around\na quadratic function");
 
// 生成数据，只是玩数字，这里没有太多要学习:
QVector<double> x0(25), y0(25);
QVector<double> x1(15), y1(15), y1err(15);
QVector<double> x2(250), y2(250);
QVector<double> x3(250), y3(250);
QVector<double> x4(250), y4(250);
for (int i=0; i<25; ++i) // 图0的数据
{
  x0[i] = 3*i/25.0;
  y0[i] = qExp(-x0[i]*x0[i]*0.8)*(x0[i]*x0[i]+x0[i]);
}
for (int i=0; i<15; ++i) // 图1数据
{
  x1[i] = 3*i/15.0;;
  y1[i] = qExp(-x1[i]*x1[i])*(x1[i]*x1[i])*2.6;
  y1err[i] = y1[i]*0.25;
}
for (int i=0; i<250; ++i) // 图2、3和4的数据
{
  x2[i] = i/250.0*3*M_PI;
  x3[i] = x2[i];
  x4[i] = i/250.0*100-50;
  y2[i] = qSin(x2[i]*12)*qCos(x2[i])*10;
  y3[i] = qCos(x3[i])*10;
  y4[i] = 0.01*x4[i]*x4[i] + 1.5*(rand()/(double)RAND_MAX-0.5) + 1.5*M_PI;
}
 
// 将数据点传递给图形:
customPlot->graph(0)->setData(x0, y0);
customPlot->graph(1)->setData(x1, y1);
errorBars->setData(y1err);
customPlot->graph(2)->setData(x2, y2);
customPlot->graph(3)->setData(x3, y3);
customPlot->graph(4)->setData(x4, y4);
// 激活顶部和右侧轴，默认不可见:
customPlot->xAxis2->setVisible(true);
customPlot->yAxis2->setVisible(true);
// 设置适合显示数据的范围:
customPlot->xAxis->setRange(0, 2.7);
customPlot->yAxis->setRange(0, 2.6);
customPlot->xAxis2->setRange(0, 3.0*M_PI);
customPlot->yAxis2->setRange(-70, 35);
// 在上轴设置PI刻度:
customPlot->xAxis2->setTicker(QSharedPointer<QCPAxisTickerPi>(new QCPAxisTickerPi));
// 添加标题布局元素:
customPlot->plotLayout()->insertRow(0);
customPlot->plotLayout()->addElement(0, 0, new QCPTextElement(customPlot, "Way too many graphs in one plot", QFont("sans", 12, QFont::Bold)));
// set labels:
customPlot->xAxis->setLabel("Bottom axis with outward ticks");
customPlot->yAxis->setLabel("Left axis label");
customPlot->xAxis2->setLabel("Top axis label");
customPlot->yAxis2->setLabel("Right axis label");
// 让底部轴上的刻度向外:
customPlot->xAxis->setTickLength(0, 5);
customPlot->xAxis->setSubTickLength(0, 3);
// 让右轴上的刻度向内和向外:
customPlot->yAxis2->setTickLength(3, 3);
customPlot->yAxis2->setSubTickLength(1, 1);
```

## 4.一个简单的折线图

```cpp
    QCustomPlot *p = ui->plot;

//    p->clearPlottables();     //清除所有图形
//    p->setBackground(Qt::lightGray);

//    p->setNoAntialiasingOnDrag(true);     //禁各种反锯齿,提高效率
//    p->setNotAntialiasedElements(QCP::aeAll);

    p->setInteractions(QCP::iRangeDrag | QCP::iRangeZoom);      //可拖拽+可滚轮缩放
    p->legend->setVisible(true);                                //右上角指示曲线的缩略框
    p->xAxis->setLabel(QStringLiteral("X轴文字"));
    p->yAxis->setLabel(QStringLiteral("Y轴文字"));

    //设各种线条、文字、背景颜色等接口，尝试取消注释来发现效果
//    p->xAxis->setLabelColor(Qt::red);
//    p->xAxis->setTickLabelColor(Qt::red);
//    p->xAxis->setBasePen(QPen(Qt::red));
//    p->xAxis->setTickPen(QPen(Qt::red));
//    p->xAxis->setSubTickPen(QPen(Qt::red));
//    p->xAxis->grid()->setPen(QPen(Qt::red));
//    p->xAxis->grid()->setSubGridPen(QPen(Qt::red));
//    p->xAxis->grid()->setZeroLinePen(QPen(Qt::red));

//    p->yAxis->setTickLabelSide(QCPAxis::lsInside);
//    p->yAxis->ticker()->setTickCount(20);
//    p->yAxis->setLabelColor(Qt::red);
//    p->yAxis->setTickLabelColor(Qt::red);
//    p->yAxis->setBasePen(QPen(Qt::red));
//    p->yAxis->setTickPen(QPen(Qt::red));
//    p->yAxis->setSubTickPen(QPen(Qt::red));
//    p->yAxis->grid()->setPen(QPen(Qt::red));
//    p->yAxis->grid()->setSubGridPen(QPen(Qt::red));
//    p->yAxis->grid()->setZeroLinePen(QPen(Qt::red));

    //轴当前可见部分的范围
    p->xAxis->setRange(0,100);
    p->yAxis->setRange(0,10);

    //添加两个图形
    p->addGraph();
    p->addGraph();
    p->graph(0)->setPen(QPen(Qt::red));
    p->graph(1)->setPen(QPen(Qt::blue));
//    p->graph(0)->setName(QStringLiteral("name1"));
//    p->graph(1)->setName(QStringLiteral("name2"));

    for(int i=0;i<100;i++)
    {
        p->graph(0)->addData(i, i%10);
        p->graph(1)->addData(i, (double)i/10.0);
    }

    //添加右键菜单
//    p->setContextMenuPolicy(Qt::CustomContextMenu);
//    connect(p, SIGNAL(customContextMenuRequested(const QPoint&)), this, SLOT(slotPlotContextMenu(const QPoint&)));
}
```

