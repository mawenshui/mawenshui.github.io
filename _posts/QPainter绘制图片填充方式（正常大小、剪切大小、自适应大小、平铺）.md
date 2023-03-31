# QPainter绘制图片填充方式（正常大小、剪切大小、自适应大小、平铺）

[原文链接](https://www.cnblogs.com/MakeView660/p/11225445.html)

Qt中QPainter提供了绘制图像的API，极大地方便了我们对图像的绘制。

Qt中提供了QPixmap, QBitmap, QBitMapQImage, QPicture等图像绘图设备，它们的类关系如下图所示：

![img](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202204011655747.png)

**QPixmap**继承了QPaintDevice，您可用以建立QPainter并于上进行绘图，您也可以直接指定图案加载Qt所支持的图档，像是BMP、GIF、JPG、JPEG、PNG等，并使用QPainter的drawPixmap()绘制在其它的绘图装置上。您可以在QLabel、QPushButton上设定QPixmap以显示图像。QPixmap是针对屏幕显示图像而设计并最佳化，依赖于所在平台的原生绘图引擎，所以一些效果的展现（像是反锯齿），在不同的平台上可能会有不一致的结果。

**QBitmap**是QPixmap的子类别，提供单色图像，可用于制作光标（QCursor）或笔刷（QBrush）物件。

**QPixmap**使用平台的绘图引擎，在不同的平台所呈现的效果不一，无法提供个别像素的存取，QImage使用Qt自身的绘图引擎，可提供在不同平台上相同的图像呈现效果，并可透过setPixpel()、pixel()等方法，直接存取指定的像素。

**QPicture**则是个绘图装置，可以记录并回放QPainter的绘图指令，您可以使用QPainter的begin()方法，指定在QPicture上进行绘图，使用end()方法结束绘图，使用QPicture的save()方法将QPainter所使用过的绘图指令存至档案

 

QPainter绘图引擎提供了drawImage、drawPicture和drawPixmap三类重载API。

drawImage类API支持绘制正常大小和自适应大小两种图片显示模式；

drawPicture类API支持绘制正常大小图片显示模式，主要用于回放QPainter的绘制；

drawPixmap类API支持绘制正常大小和自适应大小两种图片显示模式；

drawTiledPixmap提供了平铺显示模式。

综上，使用QPixmap结合QPainter可以绘制正常大小、自适应大小和平铺三种模式。

 

1、在指定位置绘制 pixmap，pixmap 不会被缩放

/* pixmap 的左上角和 widget 上 x, y 处重合 */

void QPainter::drawPixmap(int x, int y, const QPixmap & pixmap)

void QPainter::drawPixmap(const QPointF &point, const QPixmap &pixmap)

2、指定的矩形内绘制 pixmap，pixmap 被缩放填充到此矩形内

/* target 是 widget 上要绘制 pixmap 的矩形区域 */

void QPainter::drawPixmap(int x, int y, int width, int height, const QPixmap &pixmap)

void QPainter::drawPixmap(const QRect &target, const QPixmap &pixmap)

3、绘制 pixmap 的一部分，可以称其为 sub-pixmap（剪切大小）

/* source 是 sub-pixmap 的 rectangle */

void QPainter::drawPixmap(const QPoint &point, const QPixmap &pixmap, const QRect &source)

void QPainter::drawPixmap(const QRect &target, const QPixmap &pixmap, const QRect &source)

void QPainter::drawPixmap(int x, int y, const QPixmap &pixmap, int sx, int sy, int sw, int sh)

4、平铺绘制 pixmap，水平和垂直方向都会同时使用平铺的方式

void QPainter::drawTiledPixmap(const QRect &rectangle, const QPixmap &pixmap, const QPoint &position = QPoint())

void QPainter::drawTiledPixmap(int x, int y, int width, int height, const QPixmap & pixmap, int sx = 0, int sy = 0)

 

**测试代码：**

 C++ Code 

```c++
painter->setRenderHint(QPainter::Antialiasing);
QRectF target(-m_size / 2, -m_size / 2, m_size, m_size);
QRectF source(0.0, 0.0, 128.0, 128.0);
QRectF clipSource(0.0, 0.0, 100.0, 100.0);
QPixmap pixmap(":/image/qt-rocket.png");
// 自适应
painter->drawImage(target, image, source);
// 正常大小
painter->drawImage(QPointF(-m_size / 2, -m_size / 2), image, source);
// 子大小
painter->drawImage(QPointF(-m_size / 2, -m_size / 2), image, clipSource);
// 平铺
painter->drawTiledPixmap(target, pixmap);
```

![img](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202204011655753.png)