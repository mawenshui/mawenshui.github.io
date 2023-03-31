# [QTableWidget用法](https://blog.csdn.net/xiezhongyuan07/article/details/79538446)

# [QTableWidget的用法总结](https://www.cnblogs.com/findumars/p/5553367.html)

### 1.QTableWidget不能在mainwindow中随主窗口的大小变化？

 解决：在表格外部添加布局。

 代码：tableWidget = new QTableWidget;

​     tableWidget ->setObjectName(QString::fromUtf8("tableWidget"));

​     QVBoxLayout *verticalLayout;

​     verticalLayout->addWidget(tableWidget );

### 2.将表格变为禁止编辑：

 tableWidget->setEditTriggers(QAbstractItemView::NoEditTriggers);

 （参数含义：QAbstractItemView.NoEditTriggers--不能对表格内容进行修改

​       QAbstractItemView.CurrentChanged--任何时候都能对单元格修改

​       QAbstractItemView.DoubleClicked--双击单元格

​       QAbstractItemView.SelectedClicked--单击已选中的内容 

​       QAbstractItemView.EditKeyPressed--

​       QAbstractItemView.AnyKeyPressed--按下任意键就能修改

​       QAbstractItemView.AllEditTriggers--以上条件全包括）

### 3.设置表格为整行选择

 tableWidget->setSelectionBehavior(QAbstractItemView::SelectRows); //整行选中的方式

 （参数含义：AbstractItemView.SelectItems--选中单个单元格

​       QAbstractItemView.SelectRows--选中一行

​       QAbstractItemView.SelectColumns--选中一列）

### 4.单个选中和多个选中的设置：

 tableWidget->setSelectionMode(QAbstractItemView::ExtendedSelection); //设置为可以选中多个目标

 （参数含义：QAbstractItemView.NoSelection--不能选择

​       QAbstractItemView.SingleSelection--选中单个目标

​       QAbstractItemView.MultiSelection--选中多个目标

 QAbstractItemView.ExtendedSelection/QAbstractItemView.ContiguousSelection 的区别不明显，主要功能是正常情况下是单选，但按下Ctrl或Shift键后，可以多选）

### 5.表格表头的显示与隐藏

 对于水平或垂直方法的表头，可以用以下方式进行 隐藏/显示 的设置：

 tableWidget->verticalHeader()->setVisible(false);  //隐藏列表头 

 tableWidget->horizontalHeader()->setVisible(false); //隐藏行表头 

 注意：需要 #include <QHeaderView>

### 6.对表头文字的字体、颜色进行设置 

 QTableWidgetItem *columnHeaderItem0 = tableWidget->horizontalHeaderItem(0); //获得水平方向表头的Item对象 

 columnHeaderItem0->setFont(QFont("Helvetica")); //设置字体 

 columnHeaderItem0->setBackgroundColor(QColor(0,60,10)); //设置单元格背景颜色 

 columnHeaderItem0->setTextColor(QColor(200,111,30)); //设置文字颜色

 注意：需要 #include <QHeaderView>

### 7.在单元格里加入控件：

  QComboBox *comBox = new QComboBox();

  comBox->addItem("Y");

  comBox->addItem("N");

  tableWidget->setCellWidget(0,2,comBox); 

### 8.单元格中添加图片：

 tableWidget->setItem(row, 0, new QTableWidgetItem(QIcon(":/new/images/kingdemo.ico"),tr("")));

### 9设置单元格字体颜色、背景颜色和字体字符：

 QTableWidgetItem *item = new QTableWidgetItem("Apple");

 item->setBackgroundColor(QColor(0,60,10));

 item->setTextColor(QColor(200,111,100));

 item->setFont(QFont("Helvetica"));

 tableWidget->setItem(0,3,item);

 另：如果需要对所有的单元格都使用这种字体，则可以使用 tableWidget->setFont(QFont("Helvetica"));

### 10.设置单元格内文字的对齐方式

 水平对齐方式有：

 Constant Value Description

 Qt.AlignLeft 0x0001 Aligns with the left edge.

 Qt.AlignRight 0x0002 Aligns with the right edge.

 Qt.AlignHCenter 0x0004 Centers horizontally in the available space.

 Qt.AlignJustify 0x0008 Justifies the text in the available space.

 垂直对齐方式：

 Constant Value Description

 Qt.AlignTop 0x0020 Aligns with the top.

 Qt.AlignBottom 0x0040 Aligns with the bottom.

 Qt.AlignVCenter 0x0080 Centers vertically in the available space.

 如果两种都要设置，只要用 Qt.AlignHCenter | Qt.AlignVCenter 的方式即可

### 11.合并单元格：

 tableWidget->setSpan(0, 0, 3, 1) # 其参数为： 要改变单元格的1行数、2列数，要合并的3行数、4列数

### 12.设置单元格的大小

 首先，可以指定某个行或者列的大小

  tableWidget->setColumnWidth(3,200);

  tableWidget->setRowHeight(3,60);

还可以将行和列的大小设为与内容相匹配

  tableWidget->resizeColumnsToContents();

  tableWidget->resizeRowsToContents();

### 13.获得单击单元格的内容

 通过实现 itemClicked (QTableWidgetItem *) 信号的槽函数，就可以获得鼠标单击到的单元格指针，进而获得其中的文字信息

connect(tableWidget,SIGNAL(itemDoubleClicked(QTreeWidgetItem*,int)),this,SLOT(getItem(QTreeWidgetItem*,int)));

//将itemClicked信号与函数getItem绑定

### 14.QTableWidget要调整表格行宽主要涉及以下函数

 tableWidget->horizontalHeader()->setResizeMode(QHeaderView::Stretch);//使列完全填充并平分

 tableWidget->verticalHeader()->setResizeMode(QHeaderView::Stretch);//行自适应宽度                                                   

 tableWidget->resizeColumnsToContents(); //根据内容调整列宽

 tableWidget->resizeColumnToContents(int col);//根据内容自动调整给定列宽

 tableWidget->horizontalHeader()->setResizeMode//把给定列设置为给定模式

 主要模式有Stretch和Fixed

### 15.添加表头内容：

 方法一：

 QStringList header;

 header<<""<<tr("1")<<tr("2")<<tr("3")<<tr("4)<<tr("5");

 方法二：

 tableWidget->setHorizontalHeaderLabels(QStringList() << tr("1")<<tr("2")<<tr("3")<<tr("4)<<tr("5"));

### 16.清除：

 tableWidget->clear();//清除所有可见数据（包括表头），行还在

 tableWidget->clearContents();//只清除表中数据，不清除表头内容

 tableWidget->setRowCount(0)；//连行也清除掉

### 15.一些零碎的知识点代码：

 int row = tableWidget->rowCount();//获取表格中当前总行数

 tableWidget->setRowCount(row+1);//添加一行

 tableWidget->removeRow(row);//清除已有的行列

 Int row1 = tableWidget->currentItem()->row();//当前选中行

 bool focus = tableWidget->isItemSelected(tableWidget->currentItem());//判断是否选中一行

 QString proName = tableWidget->item(row, col)->text();//获取某一格内容

 setShowGrid(true);//显示表格线

 verticalHeader()->setVisible(false);//隐藏左边垂直

 QHeaderView *headerView = horizontalHeader();

 headerView->setMovable(false);//去除表头的移动

 headerView->resizeSection(0,284);//设置第一列宽

 headerView->resizeSection(1,127);//设置第二列宽

 headerView->setResizeMode(QHeaderView::Fixed);//列表不能移动

 headerView->setClickable(false);//不响应鼠标单击

 setEditTriggers(QTableWidget::NoEditTriggers);//不能编辑

 setSelectionBehavior(QTableWidget::SelectRows);//一次选中一行

 setSelectionMode(QAbstractItemView::SingleSelection);//只能单选

 /*QScrollBar *scrollBar = horizontalScrollBar();

 scrollBar->hide();*/

 setHorizontalScrollBarPolicy(Qt::ScrollBarAlwaysOff);//去掉水平滚动条

 setVerticalScrollMode(QAbstractItemView::ScrollPerItem);//垂直滚动条按项移动

 setAutoScroll(false);//去掉自动滚动

### 17.排序：

 tableWidget->sortByColumn(0, Qt::AscendingOrder);//顾名思义，该函数意思是将某列按升序/降序的方式排列



#### [参考](http://blog.csdn.net/mingxia_sui/article/details/7681863)

