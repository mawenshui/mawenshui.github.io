# QCustomPlotDemo绘图控件

[toc]



## 1.QCustomPlot类解读

### 1.1视口设置，通常就是本部件所在的长宽

```cpp
QRect   viewport () const

void    setViewport (const QRect &rect)
```



### 1.2背景  

```cpp
QPixmap     background () const  
bool    backgroundScaled () const  
Qt::AspectRatioMode     backgroundScaledMode () const  

void    setBackground (const QPixmap &pm)//在整个视口的最底层绘制图片，用作背景  
void    setBackground (const QPixmap &pm, bool scaled, Qt::AspectRatioMode mode=Qt::KeepAspectRatioByExpanding)  
void    setBackground (const QBrush &brush)  
void    setBackgroundScaled (bool scaled)//设置setBackground 所绘的图是否进行缩放以适应Viewport  
void    setBackgroundScaledMode (Qt::AspectRatioMode mode)//有Qt::IgnoreAspectRatio（忽略比例，缩放至适应视口），Qt::KeepAspectRatio（保持长宽比，直到一边最大至视口边缘）Qt::KeepAspectRatioByExpanding（保持长宽比，缩放至充满视口）
```

### 1.3抗锯齿

```cpp
QCP::AntialiasedElements    antialiasedElements () const  
QCP::AntialiasedElements    notAntialiasedElements () const  

void    setAntialiasedElements (const QCP::AntialiasedElements &antialiasedElements)//抗锯齿效果，对于各对象，都有默认设置，可通过本函数，改变默认不抗锯齿的元素，使它也抗。  
void    setAntialiasedElement (QCP::AntialiasedElement antialiasedElement, bool enabled=true)//更方便的开关  
void    setNotAntialiasedElements (const QCP::AntialiasedElements ¬AntialiasedElements)//设置不抗  
void    setNotAntialiasedElement (QCP::AntialiasedElement notAntialiasedElement, bool enabled=true)
```

### 1.4是否自动添加图形到图例，默认是 

```cpp
bool    autoAddPlottableToLegend () const  

void    setAutoAddPlottableToLegend (bool on)
```

### 1.5设置交互方式

```cpp
const QCP::Interactions     interactions () const 

void    setInteraction (const QCP::Interaction &interaction, bool enabled=true)//设置交互方式，如是否可拖动或缩放轴，是否可选中轴或图例等。  
void    setInteractions (const QCP::Interactions &interactions) 
```

### 1.6选择公差设置

```cpp
int     selectionTolerance () const  

void    setSelectionTolerance (int pixels) //选择公差设置，在多少像素中能选中某个元素 
```

### 1.7其他设置

```cpp
bool    noAntialiasingOnDrag () const  
QCP::PlottingHints  plottingHints () const  
Qt::KeyboardModifier    multiSelectModifier () const 

void    setNoAntialiasingOnDrag (bool enabled)//拖动时是否也抗锯齿  
void    setPlottingHint (QCP::PlottingHint hint, bool enabled=true)//设置其它参数，如快速绘制线（损失质量），缓存轴的lable(提高效率)  
void    setPlottingHints (const QCP::PlottingHints &hints)  
void    setMultiSelectModifier (Qt::KeyboardModifier modifier)//要多选时的按键，默认Qt::ControlModifier，即ctrl  
```

### 1.8各函数定义

```cpp
QCPAbstractPlottable *  plottable (int index)//所有添加的图线都自动加了索引，按先后顺序  
QCPAbstractPlottable *  plottable ()  
bool    addPlottable (QCPAbstractPlottable *plottable)//添加除graph线外的其它种类型的曲线或图  
bool    removePlottable (QCPAbstractPlottable *plottable)  
bool    removePlottable (int index)  
int     clearPlottables ()  
int     plottableCount () const  
QList< QCPAbstractPlottable * >   selectedPlottables () const//返回选中的线  
QCPAbstractPlottable *  plottableAt (const QPointF &pos, bool onlySelectable=false) const//返回某个点是否有图线  
bool    hasPlottable (QCPAbstractPlottable *plottable) const  

QCPGraph *  addGraph (QCPAxis *keyAxis=0, QCPAxis *valueAxis=0)//为Graph线留的特殊接口，因Graph是最常用的  
bool    removeGraph (QCPGraph *graph)  
bool    removeGraph (int index)  
QCPGraph *  graph (int index) const  
QCPGraph *  graph () const  
int     clearGraphs ()  
int     graphCount () const  
QList< QCPGraph * >   selectedGraphs () const  


bool    addItem (QCPAbstractItem *item)//添加一些附加item，如箭头等  
bool    removeItem (QCPAbstractItem *item)  
bool    removeItem (int index)  
int     clearItems ()  
int     itemCount () const  
QCPAbstractItem *   item (int index) const  
QCPAbstractItem *   item () const  
QList< QCPAbstractItem * >    selectedItems () const  
QCPAbstractItem *   itemAt (const QPointF &pos, bool onlySelectable=false) const  
bool    hasItem (QCPAbstractItem *item) const  
QCPLayoutElement *  layoutElementAt (const QPointF &pos) const  

QCPLayer *  layer (const QString &name) const//QCustomPlot分层的实现与管理  
QCPLayer *  layer (int index) const  
QCPLayer *  currentLayer () const  
bool    setCurrentLayer (const QString &name)  
bool    setCurrentLayer (QCPLayer *layer)  
int     layerCount () const  
bool    addLayer (const QString &name, QCPLayer *otherLayer=0, LayerInsertMode insertMode=limAbove)  
bool    removeLayer (QCPLayer *layer)  
bool    moveLayer (QCPLayer *layer, QCPLayer *otherLayer, LayerInsertMode insertMode=limAbove)  

QCPAxisRect *   axisRect (int index=0) const  
QList< QCPAxisRect * >    axisRects () const  
QList< QCPAxis * >    selectedAxes () const  
QList< QCPLegend * >  selectedLegends () const  
int     axisRectCount () const  

bool    savePdf (const QString &fileName, bool noCosmeticPen=false, int width=0, int height=0, const QString &pdfCreator="", const QString &pdfTitle="")//将图表导出成各种格式  
bool    savePng (const QString &fileName, int width=0, int height=0, double scale=1.0, int quality=-1)  
bool    saveJpg (const QString &fileName, int width=0, int height=0, double scale=1.0, int quality=-1)  
bool    saveBmp (const QString &fileName, int width=0, int height=0, double scale=1.0)  
bool    saveRastered (const QString &fileName, int width, int height, double scale, const char *format, int quality=-1)  
QPixmap     toPixmap (int width=0, int height=0, double scale=1.0)  
void    toPainter (QCPPainter *painter, int width=0, int height=0)  
  
//三个槽  
Q_SLOT void     replot (QCustomPlot::RefreshPriority refreshPriority=QCustomPlot::rpHint)//重新生成图表  
Q_SLOT void     rescaleAxes (bool onlyVisiblePlottables=false)  
Q_SLOT void     deselectAll ()//取消选择  
```

## 2. QCustomPlot布局

![img](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202203311419564.png)

其中最外层是顶层窗口的边缘，每个布局和相邻的窗口都有默认的间隔（每个布局都有自己的索引，布局中可以插入子布局），同样布局中的元素也有默认的间隔（每个元素都有自己的索引）。正是由于源码这种灵活的实现，使得我们可以在布局上有极大的自主权。







## 遇到的问题

### 1.程序异常结束

```cpp
customPlot = ui->customPlot;//直接使用customPlot会报错,如下图
customPlot -> addGraph();
```

![image-20210710163749675](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202203311419748.png)

```cpp
QCustomPlot *p = ui->customPlot;//使用p指针代替变量customPlot后解决问题
p -> addGraph();
```

### 2.路径或权限错误

![image-20210717173748439](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202203311419451.png)
