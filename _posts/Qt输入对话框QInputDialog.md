# [Qt输入对话框(QInputDialog)](https://www.cnblogs.com/narjaja/p/9115341.html)

## 头文件

#####  \#include <QInputDialog>

## 参数

##### 	1.父组件指针

##### 	2.对话框标题

##### 	3.指导用户输入的提示语句

##### 	4.对话框的输入模式

##### 	5.输入框中的默认内容

##### 		6.接收返回值，用户按下OK返回true，用户按下cancel返回false。



```c++
#include <QInputDialog>

bool isOK;
QString text = QInputDialog::getText(NULL, "Input Dialog",
                                    "Please input your comment",
                                     QLineEdit::Normal,
                                     "your comment",
                                     &isOK);
if(isOK) {
QMessageBox::information(NULL, "Information",
                         "Your comment is: <b>" + text + "</b>",
                         QMessageBox::Yes | QMessageBox::No,
                         QMessageBox::Yes);
}
```

![1324700-20180531104343239-695157175](https://mawenshui.oss-cn-beijing.aliyuncs.com/img_Markdown/202203311426567.png)

```c++
static QString getText(QWidget *parent, const QString &title, const QString &label,
QLineEdit::EchoMode echo = QLineEdit::Normal,
						   const QString &text = QString(), bool 							   *ok = 0, Qt::WindowFlags flags = 0);
```

##### 接口介绍

```c++
//double类型的接口
double getDouble(QWidget *parent, const QString &title, const QString &label, double value = 0, double min = -2147483647, double max = 2147483647, int decimals = 1, bool *ok = Q_NULLPTR, Qt::WindowFlags flags = Qt::WindowFlags())

//int类型的接口
int getInt(QWidget *parent, const QString &title, const QString &label, int value = 0, int min = -2147483647, int max = 2147483647, int step = 1, bool *ok = Q_NULLPTR, Qt::WindowFlags flags = Qt::WindowFlags())

//单选框获取选中的某一列
QString getItem(QWidget *parent, const QString &title, const QString &label, const QStringList &items, int current = 0, bool editable = true, bool *ok = Q_NULLPTR, Qt::WindowFlags flags = Qt::WindowFlags(), Qt::InputMethodHints inputMethodHints = Qt::ImhNone)

//多文本输入框
QString getMultiLineText(QWidget *parent, const QString &title, const QString &label, const QString &text = QString(), bool *ok = Q_NULLPTR, Qt::WindowFlags flags = Qt::WindowFlags(), Qt::InputMethodHints inputMethodHints = Qt::ImhNone)

//单文本输入框
QString getText(QWidget *parent, const QString &title, const QString &label, QLineEdit::EchoMode mode = QLineEdit::Normal, const QString &text = QString(), bool *ok = Q_NULLPTR, Qt::WindowFlags flags = Qt::WindowFlags(), Qt::InputMethodHints inputMethodHints = Qt::ImhNone)
```

##### double类型的接口参数介绍：

```c++
double getDouble( 
QWidget *parent,   //指向父对象的指针 
const QString &title,  //窗口标题
const QString &label,  //窗口提示文本信息
double value = 0,  //默认值
double min = -2147483647,  //最小值范围
double max = 2147483647,  //最大值范围
int decimals = 1,  //步长（单次操作，值递增幅度）
bool *ok = Q_NULLPTR,  //用户是否点击确认按钮（确认：OK，  取消：Cancel）
Qt::WindowFlags flags = Qt::WindowFlags())//其他窗口标志,一般直接使用默认值

返回值：double 类型
如果点击“OK”按钮：返回当前输入框的值。
如果点击“Cancel”按钮：返回当设置的默认值。
```

### 举例

```c++
//获取输入整数
bool bRet = false;

//qApp->translate(c_strQtGuiApplication, c_strInputPaswd) 多文本翻译
double dbRet = QInputDialog::getDouble(this, qApp->translate(c_strQtGuiApplication, c_strInputPaswd),
				qApp->translate(c_strQtGuiApplication, c_strPaswd), 0.0, 0.0, 100.0, 1, &bRet);
if (bRet)
{
	qDebug() << "press ok iRet = " << dbRet;
}
else
{
	qDebug() << "press Cancel iRet = " << dbRet;
}
```



## 1.获取字符串

```c++
// 获取字符串
QString string = QInputDialog::getText(this, tr("输入字符串对话框"),tr("请输入用户名："), QLineEdit::Normal,tr("admin"), &ok);
if(ok) qDebug() << "string:" << string;
```

## 2.获取整数

```c++
// 获取整数
int value1 = QInputDialog::getInt(this, tr("输入整数对话框"), tr("请输入-1000到1000之间的数值"), 100, -1000, 1000, 10, &ok);
if(ok) qDebug() << "value1:" << value1;
```

## 3.获取小数、浮点数

```c++
// 获取浮点数
double value2 = QInputDialog::getDouble(this, tr("输入浮点数对话框"),tr("请输入-1000到1000之间的数值"), 0.00, -1000, 1000, 2, &ok);
if(ok) qDebug() << "value2:" << value2;
```

## 4.获取条目

```c++
QStringList items;
items << tr("条目1") << tr("条目2");
// 获取条目
QString item = QInputDialog::getItem(this, tr("输入条目对话框"),tr("请选择或输入一个条目"), items, 0, true, &ok);
if(ok) qDebug() << "item:" << item;
```

