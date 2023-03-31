# QSS样式设计

[TOC]



## 应用QSS样式表的两种方法

```c++
//应用样式1 apply the qss style
QFile file(":/qss/qss/sensorParameter.qss");
if(file.open(QIODevice::ReadOnly | QIODevice::Text))
{
    qApp->setStyleSheet(file.readAll());
    file.close();
}

//应用样式2 apply the qss style
QFile file(":/qss/qss/MessageBox.qss");
file.open(QFile::ReadOnly);
QTextStream filetext(&file);
QString stylesheet = filetext.readAll();
this->setStyleSheet(stylesheet);
file.close();
```

## 1.QWidget样式

```css
.QWidget{
    border-image:url(:/ico/image/background.png);
}
```

## 2.QLabel样式

```css
/*******标签设置*********/
QLabel{
   background:transparent;
}
```

## 3.QTabBar样式

```CSS
QTabBar::tab{
    width:140px;
    height:40px;
    border-image:url(:/ico/image/QTabWidget_title_default.png);
    color:rgb(255,255,255);
}
QTabBar::tab::hover{
    border-image:url(:/ico/image/QTabWidget_title_hover.png);
}
QTabBar::tab::selected{
    border-image:url(:/ico/image/QTabWidget_title_selected.png);
    color:rgb(0,0,0);
}
```

## 4.QTableWidget样式

```css
.QTableWidget{
    border-image:url(:/ico/image/background.png);
}
```

## 5.QTreeWidget样式

```css
.QTreeWidget{
    border-image:url(:/ico/image/background_treeWidget.png);
}
QHeaderView::section{
    background-color: rgb(202, 227, 249);
}
QTreeWidget::item{
    height:30px;
    border-radius:4px;
}
QTreeWidget::item:hover{
    background-color:rgba(114,178,234,30%);
}
QTreeWidget::item:selected{
    background-color:rgba(114,178,234,60%);
}
```

## 6.QScrollBar样式

```css
/*垂直滚动条*/
.QScrollBar:vertical{
    padding-left:4px;
    padding-right:4px;
    padding-top:25px;
    padding-bottom:25px;
    border-radius:2px;
    background:rgba(139,191,238,20%);
}
.QScrollBar::handle:vertical{
    border-radius:2px;
    background:rgba(139,191,238, 40%);
}
/*水平滚动条*/
.QScrollBar:horizontal{
    padding-left:25px;
    padding-right:25px;
    padding-top:4px;
    padding-bottom:4px;
    border-radius:2px;
    background:rgba(139,191,238,20%);
}
.QScrollBar::handle:horizontal{
    border-radius:2px;
    background:rgba(139,191,238, 40%);
}
```

## 7.QPushButton样式

```css
#addPushButton, #preservationPushButton, #deletePushButton
{
    color:white;
    border-radius: 5px;
    border-image:url(:/ico/image/QPushButton_default.png);
}

#addPushButton:hover, #preservationPushButton:hover
{
    border-image:url(:/ico/image/QPushButton_suspension.png);
}
#addPushButton:pressed, #preservationPushButton:pressed
{
    border-image:url(:/ico/image/QPushButton_press.png);
}

#deletePushButton:hover
{
    border-image:url(:/ico/image/Delete_suspension.png);
}
#deletePushButton:pressed
{
    border-image:url(:/ico/image/Delete_press.png);
}
```

## 8.QLineEdit, QComboBox, QSpinBox样式

```css
.QLineEdit, .QComboBox, .QSpinBox
{
    border-style: outset;
    border : 1px solid #72b2ea;
    border-radius: 2px;
    background-color:rgba(229, 241, 253,100%);
}

QComboBox::drop-down { 
    padding-top:2px;
    padding-right:2px;
    image: url(:/ico/image/QComboBox_down_default.png);
}
QComboBox::drop-down:hover { 
    image: url(:/ico/image/QComboBox_down_suspension.png);
}
QComboBox::drop-down:pressed { 
    image: url(:/ico/image/QComboBox_down_press.png);
}

/* 下拉后，整个下拉窗体样式 */
QComboBox QAbstractItemView {
    border: 1px solid #72b2ea;   /* 整个下拉窗体的边框 */
    color: black;
    background-color: #e5f1fd;   /* 整个下拉窗体的背景色 */
    selection-background-color: #97c7f0;   /* 整个下拉窗体被选中项的背景色 */
}

/* 下拉后，整个下拉窗体越过每项的样式 */
QComboBox QAbstractItemView::item:hover {
    color: black;
    background-color: #b7d9f5;   /* 整个下拉窗体越过每项的背景色 */
}

QSpinBox::up-button {
    padding-top:1px;
    padding-right:1px;
    border-image: url(:/ico/image/QSpinBox_up_default.png);
}
QSpinBox::down-button {
    padding-top:1px;
    padding-right:1px;
    border-image: url(:/ico/image/QSpinBox_down_default.png);
}
QSpinBox::up-button::pressed {
    border-image: url(:/ico/image/QSpinBox_up_pressd.png);
}
QSpinBox::down-button::pressed {
    border-image: url(:/ico/image/QSpinBox_down_pressd.png);
}
```

## 9.QGroupBox样式

```CSS
QGroupBox {
    border:1px solid #72b2ea;
    border-radius: 3px;
    margin-top: 6ex;	/* 标题栏置于控件上 */
}

#TechnicalParameterGroupBox::title {
    subcontrol-origin: margin;
    subcontrol-position:top centor;		/* 居中对齐 */
    border-radius: 3px;
    padding-left:198px;
    padding-right:190px;
    border-image: url(:/ico/image/QGroupBox_title.png);
}
```

## 10.水平滚动条按需出现

```c++
//水平滚动条按需出现
QHeaderView *pHeader = ui_Platform->treeWidget_Platform->header();
pHeader->setSectionResizeMode(QHeaderView::ResizeToContents);
pHeader->setStretchLastSection(false);
```

## 11.设置QTreeWidget节点展开、折叠的图标

```c++
//设置QTreeWidget节点展开、折叠的图标
ui_Platform->treeWidget_Platform->setStyleSheet
("QTreeView::branch:has-children:!has-siblings:closed,\
QTreeView::branch:closed:has-children:has-siblings{border-image: none; image: url(:/ico/image/QTreeWidget_close.png);}\
QTreeView::branch:open:has-children:!has-siblings,\
QTreeView::branch:open:has-children:has-siblings{border-image: none; image: url(:/ico/image/QTreeWidget_open.png);}");
```

