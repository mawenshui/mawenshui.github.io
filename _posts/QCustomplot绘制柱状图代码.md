DrawHistogram.pro

```c++
greaterThan(QT_MAJOR_VERSION, 4): QT += widgets printsupport
```

DrawHistogram.h

```c++
/**
* @ProjectName   //DrawHistogram.pro
* @Brief         //使用QCustomplot绘制竖直柱状图
* @param         //QVector<double> &l_vecTicks X轴坐标
* @param         //QVector<QString> &l_vecLabels X轴刻度的文字显示
* @param         //QVector<double> &l_vecYData Y值
* @Author        //mawenshui
* @Date          //2022-11-29
*/

#ifndef CDRAWHISTOGRAM_H
#define CDRAWHISTOGRAM_H

#include <QWidget>
#include "qcustomplot.h"

QT_BEGIN_NAMESPACE
namespace Ui { class CDrawHistogram; }
QT_END_NAMESPACE

class CDrawHistogram : public QWidget
{
    Q_OBJECT

public:
    CDrawHistogram(QWidget *parent = nullptr);
    ~CDrawHistogram();

    void drawHistogram(QVector<double> &l_vecTicks, QVector<QString> &l_vecLabels, QVector<double> &l_vecYData);

private:
    Ui::CDrawHistogram *ui;
};
#endif // CDRAWHISTOGRAM_H
```

DrawHistogram.cpp

```c++
#include "DrawHistogram.h"
#include "ui_DrawHistogram.h"

CDrawHistogram::CDrawHistogram(QWidget *parent)
    : QWidget(parent)
    , ui(new Ui::CDrawHistogram)
{
    ui->setupUi(this);

    QVector<double> l_vecTicks;
    QVector<QString> l_vecLabels;
    QVector<double> l_vecYData;
    //测试数据
    l_vecTicks << 1 << 2 << 3 << 4 << 5 << 6 << 7 << 8 << 9 << 10 << 11 << 12 << 13 << 14;//x轴的范围
    l_vecLabels << "A" << "B" << "C" << "D" << "E" << "F" << "G"<< "H" << "I" << "J" << "K" << "L" << "M" << "N";//x轴的刻度文字显示
    l_vecYData  << 10 << 9 << 2 << 5 << 7 << 4 << 1 << 100 << 90 << 20 << 50 << 70 << 40 << 10;//y轴坐标值

    drawHistogram(l_vecTicks, l_vecLabels, l_vecYData);
}

CDrawHistogram::~CDrawHistogram()
{
    delete ui;
}

/***********************************************************************
*
*@Description: // 绘制柱状图
* l_vecTicks x轴刻度 l_vecLabels x轴刻度的文字 l_vecYData y值
*@Author:      // mawenshui
*@Time:        // 2022-11-28
*
************************************************************************/
void CDrawHistogram::drawHistogram(QVector<double> &l_vecTicks, QVector<QString> &l_vecLabels, QVector<double> &l_vecYData)
{
    //传入的数组大小务必一致
    if((l_vecTicks.size() == l_vecLabels.size()) && (l_vecLabels.size() == l_vecYData.size()) && (l_vecTicks.size() == l_vecYData.size()))
    {
        QCPAxis *xAxis = ui->customPlot->xAxis;//x轴
        QCPAxis *yAxis = ui->customPlot->yAxis;//y轴
        QCPBars *bars = new QCPBars(xAxis, yAxis);  // 使用xAxis作为柱状图的x轴，yAxis作为y轴

        bars->setAntialiased(false); // 为了更好的边框效果，关闭抗齿锯
        bars->setName("Bars"); // 设置图例
        bars->setPen(QPen(QColor(0, 160, 140).lighter(130))); // 设置柱状图的边框颜色
        bars->setBrush(QColor(20,68,106));  // 设置柱状图的画刷颜色

        int l_ixAxis_Max = l_vecTicks.size();
        qDebug()<<"l_ixAxis_Max:"<<l_ixAxis_Max;

        QSharedPointer<QCPAxisTickerText> textTicker(new QCPAxisTickerText);
        textTicker->addTicks(l_vecTicks, l_vecLabels);
        xAxis->setTicker(textTicker);        // 设置为文字轴
        xAxis->setTickLabelRotation(60);     // 轴刻度文字旋转60度
        xAxis->setSubTicks(false);           // 显示子刻度
        xAxis->setTickLength(0, 4);          // 轴内外刻度的长度分别是0,4,也就是轴内的刻度线不显示
        xAxis->setRange(0, l_ixAxis_Max);    // 设置x轴范围
        //    xAxis->setLabel("x");
        xAxis->setUpperEnding(QCPLineEnding::esSpikeArrow);

//        double l_dbyAxis_Max;
//        auto max = std::max_element(std::begin(l_vecYData), std::end(l_vecYData));
//        l_dbyAxis_Max = *max;

        double l_dbyAxis_Max = *(std::max_element(std::begin(l_vecYData), std::end(l_vecYData)));

        qDebug()<<"l_dbyAxis_Max: "<<l_dbyAxis_Max;

        yAxis->setRange(0, l_dbyAxis_Max * 1.1);  //设置y轴坐标范围
        yAxis->setPadding(35);                    // 轴的内边距
        //    yAxis->setLabel("y");
        yAxis->setUpperEnding(QCPLineEnding::esSpikeArrow);

        bars->setData(l_vecTicks, l_vecYData);

        //设置为可拖拽、 可放大缩小、可选中
        //    ui->customPlot->setInteractions(QCP::iRangeDrag | QCP::iRangeZoom | QCP::iSelectPlottables);
        ui->customPlot->setInteractions(QCP::iRangeDrag);

        ui->customPlot->replot();
    }
    else
    {
        QMessageBox::warning(this, "drawHistogram", "请检查传入数据是否正确！");
    }
}
```

![image-20221130190540301](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/image-20221130190540301.png)



![image-20221130202939097](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/image-20221130202939097.png)
